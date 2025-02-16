# API Gateway

## Overview
API Gateway is a server that acts as an API front-end, receiving API requests, enforcing throttling and security policies, passing requests to the back-end service, and then passing the response back to the requester. It acts as a reverse proxy to accept all application programming interface (API) calls, aggregate the various services required to fulfill them, and return the appropriate result.

## Technical Details

### Core Components
1. **Request Routing**: Direct requests to appropriate services
2. **Authentication**: Verify client identity
3. **Authorization**: Control access to services
4. **Rate Limiting**: Control request rates
5. **Caching**: Cache responses
6. **Transformation**: Modify requests/responses
7. **Monitoring**: Track API usage
8. **Load Balancing**: Distribute load across services

### Common Implementations
1. **Kong**: Open-source API Gateway
2. **AWS API Gateway**: Amazon's managed service
3. **Azure API Management**: Microsoft's API management solution
4. **Spring Cloud Gateway**: Spring's API Gateway
5. **Nginx**: Web server with API Gateway capabilities

## Implementation

### Spring Cloud Gateway Implementation
```java
@SpringBootApplication
@EnableDiscoveryClient
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}

// application.yml
spring:
  cloud:
    gateway:
      routes:
        - id: user_service
          uri: lb://user-service
          predicates:
            - Path=/api/users/**
          filters:
            - StripPrefix=1
            - name: CircuitBreaker
              args:
                name: userServiceBreaker
                fallbackUri: forward:/fallback
        
        - id: order_service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20

// Rate Limiter Configuration
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("user_service", r -> r
                .path("/api/users/**")
                .filters(f -> f
                    .stripPrefix(1)
                    .requestRateLimiter(c -> c
                        .setRateLimiter(redisRateLimiter())
                    )
                )
                .uri("lb://user-service")
            )
            .build();
    }
    
    @Bean
    public RedisRateLimiter redisRateLimiter() {
        return new RedisRateLimiter(10, 20);
    }
}
```

### Kong Implementation
```yaml
# docker-compose.yml
version: '3.7'
services:
  kong-database:
    image: postgres:9.6
    environment:
      - POSTGRES_USER=kong
      - POSTGRES_DB=kong
      - POSTGRES_PASSWORD=kong
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "kong"]
      interval: 5s
      timeout: 5s
      retries: 5

  kong-migration:
    image: kong:latest
    depends_on:
      - kong-database
    environment:
      - KONG_DATABASE=postgres
      - KONG_PG_HOST=kong-database
      - KONG_PG_USER=kong
      - KONG_PG_PASSWORD=kong
    command: kong migrations bootstrap

  kong:
    image: kong:latest
    depends_on:
      - kong-database
      - kong-migration
    environment:
      - KONG_DATABASE=postgres
      - KONG_PG_HOST=kong-database
      - KONG_PG_USER=kong
      - KONG_PG_PASSWORD=kong
      - KONG_PROXY_ACCESS_LOG=/dev/stdout
      - KONG_ADMIN_ACCESS_LOG=/dev/stdout
      - KONG_PROXY_ERROR_LOG=/dev/stderr
      - KONG_ADMIN_ERROR_LOG=/dev/stderr
      - KONG_ADMIN_LISTEN=0.0.0.0:8001
    ports:
      - "8000:8000"
      - "8001:8001"
    healthcheck:
      test: ["CMD", "kong", "health"]
      interval: 10s
      timeout: 10s
      retries: 10

# Kong service and route configuration
curl -i -X POST http://localhost:8001/services \
  --data name=user-service \
  --data url='http://user-service:8080'

curl -i -X POST http://localhost:8001/services/user-service/routes \
  --data 'paths[]=/api/users' \
  --data name=user-route

# Add rate limiting plugin
curl -i -X POST http://localhost:8001/services/user-service/plugins \
  --data name=rate-limiting \
  --data config.minute=5 \
  --data config.hour=100

# Add authentication plugin
curl -i -X POST http://localhost:8001/services/user-service/plugins \
  --data name=key-auth
```

### Custom API Gateway Implementation (Python with FastAPI)
```python
from fastapi import FastAPI, Request, HTTPException
from fastapi.responses import JSONResponse
import httpx
import jwt
import time
from datetime import datetime
import redis
import logging

class APIGateway:
    def __init__(self):
        self.app = FastAPI()
        self.service_registry = {}
        self.rate_limiter = RateLimiter()
        self.cache = ResponseCache()
        self.setup_routes()
        
    def setup_routes(self):
        @self.app.middleware("http")
        async def gateway_middleware(request: Request, call_next):
            # Authentication
            if not self.authenticate_request(request):
                raise HTTPException(status_code=401, detail="Unauthorized")
            
            # Rate Limiting
            if not self.rate_limiter.allow_request(request):
                raise HTTPException(status_code=429, detail="Too many requests")
            
            # Route Request
            try:
                response = await self.route_request(request)
                return response
            except Exception as e:
                logging.error(f"Gateway error: {str(e)}")
                raise HTTPException(status_code=500, detail="Internal server error")
                
    async def route_request(self, request: Request):
        path = request.url.path
        service = self.get_service_for_path(path)
        
        if not service:
            raise HTTPException(status_code=404, detail="Service not found")
            
        # Check cache
        cache_key = self.generate_cache_key(request)
        cached_response = self.cache.get(cache_key)
        if cached_response:
            return cached_response
            
        # Forward request
        async with httpx.AsyncClient() as client:
            response = await client.request(
                method=request.method,
                url=f"{service['url']}{path}",
                headers=dict(request.headers),
                content=await request.body()
            )
            
        # Cache response
        self.cache.set(cache_key, response)
        
        return response
        
    def authenticate_request(self, request: Request):
        token = request.headers.get('Authorization')
        if not token:
            return False
            
        try:
            payload = jwt.decode(
                token.split()[1],
                'secret_key',
                algorithms=['HS256']
            )
            return True
        except jwt.InvalidTokenError:
            return False
            
    def get_service_for_path(self, path: str):
        # Simple path-based routing
        if path.startswith('/api/users'):
            return self.service_registry.get('user-service')
        elif path.startswith('/api/orders'):
            return self.service_registry.get('order-service')
        return None

class RateLimiter:
    def __init__(self):
        self.redis_client = redis.Redis(host='localhost', port=6379, db=0)
        self.rate_limit = 100  # requests per minute
        
    def allow_request(self, request: Request):
        client_ip = request.client.host
        current_minute = int(time.time() / 60)
        key = f"{client_ip}:{current_minute}"
        
        current_count = self.redis_client.incr(key)
        if current_count == 1:
            self.redis_client.expire(key, 60)
            
        return current_count <= self.rate_limit

class ResponseCache:
    def __init__(self):
        self.redis_client = redis.Redis(host='localhost', port=6379, db=1)
        self.default_ttl = 300  # 5 minutes
        
    def generate_cache_key(self, request: Request):
        return f"{request.method}:{request.url.path}:{request.query_params}"
        
    def get(self, key: str):
        return self.redis_client.get(key)
        
    def set(self, key: str, value, ttl: int = None):
        self.redis_client.setex(
            key,
            ttl or self.default_ttl,
            value
        )

# Usage
gateway = APIGateway()
app = gateway.app
```

## Design Considerations

### Architecture Planning
1. **Routing Strategy**
   - Path-based routing
   - Header-based routing
   - Content-based routing
   - Load balancing

2. **Security**
   - Authentication
   - Authorization
   - SSL/TLS termination
   - Request validation

3. **Performance**
   - Caching
   - Rate limiting
   - Circuit breaking
   - Request/Response transformation

### Implementation Patterns

#### Circuit Breaker Pattern
```python
import time
from functools import wraps

class CircuitBreaker:
    def __init__(self, failure_threshold=5, reset_timeout=60):
        self.failure_threshold = failure_threshold
        self.reset_timeout = reset_timeout
        self.failures = 0
        self.last_failure_time = None
        self.state = "CLOSED"
        
    def __call__(self, func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            if self.state == "OPEN":
                if self.should_reset():
                    self.state = "HALF-OPEN"
                else:
                    raise Exception("Circuit is OPEN")
                    
            try:
                result = await func(*args, **kwargs)
                if self.state == "HALF-OPEN":
                    self.state = "CLOSED"
                    self.failures = 0
                return result
            except Exception as e:
                self.handle_failure()
                raise e
                
        return wrapper
        
    def handle_failure(self):
        self.failures += 1
        self.last_failure_time = time.time()
        
        if self.failures >= self.failure_threshold:
            self.state = "OPEN"
            
    def should_reset(self):
        if not self.last_failure_time:
            return False
        return time.time() - self.last_failure_time >= self.reset_timeout
```

## Security Implementation

### JWT Authentication
```python
from datetime import datetime, timedelta
import jwt
from fastapi import Security, HTTPException
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

class JWTAuth:
    def __init__(self, secret_key: str):
        self.secret_key = secret_key
        self.security = HTTPBearer()
        
    def create_token(self, data: dict, expires_delta: timedelta = None):
        to_encode = data.copy()
        if expires_delta:
            expire = datetime.utcnow() + expires_delta
        else:
            expire = datetime.utcnow() + timedelta(minutes=15)
            
        to_encode.update({"exp": expire})
        return jwt.encode(to_encode, self.secret_key, algorithm="HS256")
        
    async def verify_token(self, credentials: HTTPAuthorizationCredentials = Security(HTTPBearer())):
        try:
            payload = jwt.decode(
                credentials.credentials,
                self.secret_key,
                algorithms=["HS256"]
            )
            return payload
        except jwt.InvalidTokenError:
            raise HTTPException(
                status_code=401,
                detail="Invalid authentication credentials"
            )
```

## Best Practices

### Implementation Guidelines
1. Use appropriate authentication mechanisms
2. Implement rate limiting
3. Enable response caching
4. Implement circuit breakers
5. Monitor API usage

### Monitoring and Maintenance
```python
class APIMetrics:
    def __init__(self):
        self.requests = {}
        self.response_times = {}
        self.errors = {}
        
    def track_request(self, path: str, method: str, start_time: float):
        key = f"{method}:{path}"
        self.requests[key] = self.requests.get(key, 0) + 1
        
        end_time = time.time()
        response_time = end_time - start_time
        
        if key not in self.response_times:
            self.response_times[key] = []
        self.response_times[key].append(response_time)
        
    def track_error(self, path: str, method: str, error: Exception):
        key = f"{method}:{path}"
        if key not in self.errors:
            self.errors[key] = []
        self.errors[key].append(str(error))
        
    def get_metrics(self):
        return {
            "requests": self.requests,
            "average_response_times": {
                k: sum(v)/len(v) for k, v in self.response_times.items()
            },
            "error_counts": {
                k: len(v) for k, v in self.errors.items()
            }
        }
```

### Error Handling
1. Handle service timeouts
2. Implement retry mechanisms
3. Provide meaningful error responses
4. Log error details
5. Monitor error rates

## Interview Tips

### Key Concepts
1. API Gateway patterns and responsibilities
2. Authentication and authorization
3. Rate limiting and caching
4. Circuit breaking and fallback
5. Request routing and transformation

### Common Questions
1. What are the benefits of using an API Gateway?
2. How to handle service authentication?
3. What are the different routing strategies?
4. How to implement rate limiting?
5. How to handle service failures?

### Real-world Scenarios
1. Implementing API Gateway for microservices
2. Handling cross-cutting concerns
3. Managing API versioning
4. Implementing security policies
5. Scaling API Gateway 