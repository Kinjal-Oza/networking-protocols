# Service Discovery

## Overview
Service Discovery is a key component of microservices architecture that enables services to find and communicate with each other dynamically. It maintains a registry of available service instances and their locations, allowing for automatic service registration and discovery.

## Technical Details

### Core Components
1. **Service Registry**: Central database of service instances
2. **Service Registration**: Process of adding service instances
3. **Service Discovery**: Process of finding service instances
4. **Health Checking**: Monitoring service instance health
5. **Load Balancing**: Distributing requests across instances

### Common Implementations
1. **Consul**: HashiCorp's service mesh solution
2. **Eureka**: Netflix's service discovery server
3. **Etcd**: Distributed key-value store
4. **ZooKeeper**: Distributed coordination service
5. **Kubernetes Service Discovery**: DNS-based discovery

## Implementation

### Consul Implementation
```python
import consul
import socket
import random
import time

class ServiceRegistry:
    def __init__(self, host='localhost', port=8500):
        self.consul = consul.Consul(host=port)
        self.service_id = None
        
    def register_service(self, service_name, service_port):
        """Register a service with Consul"""
        self.service_id = f"{service_name}-{random.randint(1000, 9999)}"
        
        service_address = socket.gethostbyname(socket.gethostname())
        
        self.consul.agent.service.register(
            name=service_name,
            service_id=self.service_id,
            address=service_address,
            port=service_port,
            check=consul.Check.tcp(service_address, service_port, "10s")
        )
        
        print(f"Registered service: {service_name} ({self.service_id})")
        
    def deregister_service(self):
        """Deregister a service from Consul"""
        if self.service_id:
            self.consul.agent.service.deregister(self.service_id)
            print(f"Deregistered service: {self.service_id}")
            
    def get_service_address(self, service_name):
        """Get address of a service instance"""
        _, services = self.consul.health.service(service_name, passing=True)
        
        if services:
            service = random.choice(services)
            address = service['Service']['Address']
            port = service['Service']['Port']
            return f"http://{address}:{port}"
        
        return None

# Example service registration
def run_service():
    registry = ServiceRegistry()
    
    try:
        # Register service
        registry.register_service("user-service", 8080)
        
        # Keep service running
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        registry.deregister_service()
```

### Eureka Implementation (Spring Boot)
```java
// Service Registration
@SpringBootApplication
@EnableEurekaClient
public class UserServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(UserServiceApplication.class, args);
    }
}

// application.yml
spring:
  application:
    name: user-service
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
  instance:
    preferIpAddress: true

// Service Discovery
@RestController
public class UserController {
    @Autowired
    private DiscoveryClient discoveryClient;
    
    @GetMapping("/service-instances/{applicationName}")
    public List<ServiceInstance> serviceInstancesByApplicationName(
        @PathVariable String applicationName
    ) {
        return this.discoveryClient.getInstances(applicationName);
    }
}
```

### Kubernetes Service Discovery
```yaml
# Service definition
apiVersion: v1
kind: Service
metadata:
  name: user-service
spec:
  selector:
    app: user-service
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP

# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: user-service:latest
        ports:
        - containerPort: 8080
```

## Design Considerations

### Architecture Planning
1. **Service Registration**
   - Automatic vs Manual registration
   - Health check mechanisms
   - Service metadata
   - Instance identification

2. **Service Discovery**
   - Client-side vs Server-side discovery
   - Caching strategies
   - Failover handling
   - Load balancing

3. **High Availability**
   - Registry replication
   - Failure detection
   - Recovery procedures
   - Data consistency

### Implementation Patterns

#### Client-Side Discovery
```python
class ServiceDiscoveryClient:
    def __init__(self, registry_url):
        self.registry_url = registry_url
        self.cache = {}
        self.cache_ttl = 60  # seconds
        
    def get_service(self, service_name):
        # Check cache first
        if self.is_cache_valid(service_name):
            return self.cache[service_name]['instances']
            
        # Query registry
        instances = self.query_registry(service_name)
        self.update_cache(service_name, instances)
        return instances
        
    def is_cache_valid(self, service_name):
        if service_name not in self.cache:
            return False
            
        cache_entry = self.cache[service_name]
        return time.time() - cache_entry['timestamp'] < self.cache_ttl
        
    def update_cache(self, service_name, instances):
        self.cache[service_name] = {
            'instances': instances,
            'timestamp': time.time()
        }
```

#### Server-Side Discovery
```python
class LoadBalancer:
    def __init__(self, registry_client):
        self.registry_client = registry_client
        self.algorithms = {
            'round_robin': self.round_robin,
            'random': self.random,
            'least_connections': self.least_connections
        }
        self.current_index = 0
        
    def get_instance(self, service_name, algorithm='round_robin'):
        instances = self.registry_client.get_service(service_name)
        if not instances:
            raise Exception(f"No instances found for {service_name}")
            
        return self.algorithms[algorithm](instances)
        
    def round_robin(self, instances):
        instance = instances[self.current_index % len(instances)]
        self.current_index += 1
        return instance
        
    def random(self, instances):
        return random.choice(instances)
        
    def least_connections(self, instances):
        return min(instances, key=lambda x: x.get('connections', 0))
```

## Security Implementation

### Authentication and Authorization
```python
from functools import wraps
import jwt

def authenticate_service(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        token = request.headers.get('Service-Auth-Token')
        if not token:
            raise Exception("No service authentication token provided")
            
        try:
            payload = jwt.decode(token, 'secret_key', algorithms=['HS256'])
            service_name = payload.get('service_name')
            if not is_service_authorized(service_name):
                raise Exception("Service not authorized")
        except jwt.InvalidTokenError:
            raise Exception("Invalid service authentication token")
            
        return func(*args, **kwargs)
    return wrapper

def is_service_authorized(service_name):
    # Check service authorization
    authorized_services = get_authorized_services()
    return service_name in authorized_services
```

## Best Practices

### Implementation Guidelines
1. Implement automatic service registration
2. Use health checks for service monitoring
3. Implement service deregistration
4. Cache service locations
5. Handle failure scenarios gracefully

### Monitoring and Maintenance
```python
class ServiceMonitor:
    def __init__(self, registry_client):
        self.registry_client = registry_client
        self.metrics = {}
        
    def track_service_health(self, service_name):
        instances = self.registry_client.get_service(service_name)
        healthy_count = sum(1 for i in instances if i['status'] == 'UP')
        total_count = len(instances)
        
        self.metrics[service_name] = {
            'healthy_instances': healthy_count,
            'total_instances': total_count,
            'health_percentage': (healthy_count / total_count * 100)
        }
        
    def get_service_metrics(self):
        return self.metrics
```

### Error Handling
1. Handle service registration failures
2. Implement retry mechanisms
3. Handle network partitions
4. Manage stale cache entries
5. Handle service timeouts

## Interview Tips

### Key Concepts
1. Service registration and discovery patterns
2. Health checking mechanisms
3. Load balancing strategies
4. Caching and failover
5. Security considerations

### Common Questions
1. What are the different service discovery patterns?
2. How to handle service registration and deregistration?
3. What are the advantages of client-side vs server-side discovery?
4. How to implement service health monitoring?
5. How to handle service discovery in a distributed system?

### Real-world Scenarios
1. Implementing service discovery in a microservices architecture
2. Handling service discovery in a cloud environment
3. Implementing service discovery with containers
4. Managing service discovery in a hybrid cloud setup
5. Implementing cross-region service discovery 