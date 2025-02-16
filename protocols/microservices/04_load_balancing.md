# Load Balancing

## Overview
Load Balancing is a critical component in microservices architecture that distributes incoming network traffic across multiple service instances to ensure optimal resource utilization, maximize throughput, minimize response time, and avoid overload of any single instance.

## Technical Details

### Core Components
1. **Load Balancer**: Central traffic distributor
2. **Service Instances**: Backend servers/services
3. **Health Checks**: Instance monitoring
4. **Distribution Algorithms**: Traffic routing logic
5. **Session Management**: Request affinity handling

### Common Implementations
1. **Nginx**: Popular web server and load balancer
2. **HAProxy**: High-performance TCP/HTTP load balancer
3. **AWS ELB**: Amazon's Elastic Load Balancer
4. **Kubernetes Service**: Container orchestration load balancing
5. **Client-side Load Balancing**: Service mesh implementations

## Implementation

### Nginx Load Balancer Configuration
```nginx
# nginx.conf
events {
    worker_connections 1024;
}

http {
    upstream backend_services {
        # Round Robin (default)
        server backend1.example.com:8080;
        server backend2.example.com:8080;
        server backend3.example.com:8080;
        
        # Least Connections
        least_conn;
        
        # IP Hash
        ip_hash;
        
        # Weighted Round Robin
        server backend1.example.com:8080 weight=3;
        server backend2.example.com:8080 weight=2;
        server backend3.example.com:8080 weight=1;
        
        # Server health checks
        server backend1.example.com:8080 max_fails=3 fail_timeout=30s;
    }
    
    server {
        listen 80;
        server_name example.com;
        
        location / {
            proxy_pass http://backend_services;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            
            # Health check
            health_check interval=5s fails=3 passes=2;
            
            # Session persistence
            proxy_cookie_path / "/; HttpOnly; SameSite=strict";
        }
    }
}
```

### HAProxy Configuration
```haproxy
# haproxy.cfg
global
    log /dev/log local0
    log /dev/log local1 notice
    daemon
    maxconn 4096
    
defaults
    log global
    mode http
    option httplog
    option dontlognull
    timeout connect 5000
    timeout client 50000
    timeout server 50000
    
frontend http_front
    bind *:80
    stats uri /haproxy?stats
    default_backend http_back
    
backend http_back
    balance roundrobin
    cookie SERVERID insert indirect nocache
    option httpchk HEAD /health HTTP/1.1\r\nHost:\ localhost
    server server1 backend1.example.com:8080 check cookie server1
    server server2 backend2.example.com:8080 check cookie server2
    server server3 backend3.example.com:8080 check cookie server3
```

### Client-Side Load Balancer (Python)
```python
import random
import time
from dataclasses import dataclass
from typing import List, Dict
import aiohttp
import asyncio

@dataclass
class ServiceInstance:
    host: str
    port: int
    weight: int = 1
    healthy: bool = True
    last_check: float = 0
    failed_checks: int = 0

class LoadBalancer:
    def __init__(self, check_interval: int = 10):
        self.instances: List[ServiceInstance] = []
        self.check_interval = check_interval
        self.algorithms = {
            'round_robin': self.round_robin,
            'weighted_round_robin': self.weighted_round_robin,
            'least_connections': self.least_connections,
            'random': self.random
        }
        self.current_index = 0
        self.connections: Dict[ServiceInstance, int] = {}
        
    def add_instance(self, host: str, port: int, weight: int = 1):
        instance = ServiceInstance(host=host, port=port, weight=weight)
        self.instances.append(instance)
        self.connections[instance] = 0
        
    def remove_instance(self, host: str, port: int):
        self.instances = [
            i for i in self.instances 
            if not (i.host == host and i.port == port)
        ]
        
    async def health_check(self, instance: ServiceInstance):
        """Perform health check on an instance"""
        try:
            async with aiohttp.ClientSession() as session:
                url = f"http://{instance.host}:{instance.port}/health"
                async with session.get(url) as response:
                    healthy = response.status == 200
                    instance.healthy = healthy
                    instance.last_check = time.time()
                    if not healthy:
                        instance.failed_checks += 1
                    else:
                        instance.failed_checks = 0
        except Exception:
            instance.healthy = False
            instance.failed_checks += 1
            
    async def check_instances_health(self):
        """Periodically check health of all instances"""
        while True:
            tasks = [
                self.health_check(instance) 
                for instance in self.instances
            ]
            await asyncio.gather(*tasks)
            await asyncio.sleep(self.check_interval)
            
    def get_instance(self, algorithm: str = 'round_robin') -> ServiceInstance:
        """Get next instance based on selected algorithm"""
        if not self.instances:
            raise Exception("No instances available")
            
        healthy_instances = [
            i for i in self.instances if i.healthy
        ]
        if not healthy_instances:
            raise Exception("No healthy instances available")
            
        return self.algorithms[algorithm](healthy_instances)
        
    def round_robin(self, instances: List[ServiceInstance]) -> ServiceInstance:
        """Round Robin selection"""
        instance = instances[self.current_index % len(instances)]
        self.current_index += 1
        return instance
        
    def weighted_round_robin(
        self,
        instances: List[ServiceInstance]
    ) -> ServiceInstance:
        """Weighted Round Robin selection"""
        total_weight = sum(instance.weight for instance in instances)
        position = self.current_index % total_weight
        
        current_weight = 0
        for instance in instances:
            current_weight += instance.weight
            if position < current_weight:
                self.current_index += 1
                return instance
                
        return instances[0]
        
    def least_connections(
        self,
        instances: List[ServiceInstance]
    ) -> ServiceInstance:
        """Least Connections selection"""
        return min(
            instances,
            key=lambda x: self.connections.get(x, 0)
        )
        
    def random(self, instances: List[ServiceInstance]) -> ServiceInstance:
        """Random selection"""
        return random.choice(instances)
        
    async def handle_request(
        self,
        path: str,
        method: str = 'GET',
        algorithm: str = 'round_robin'
    ):
        """Handle incoming request"""
        instance = self.get_instance(algorithm)
        self.connections[instance] += 1
        
        try:
            async with aiohttp.ClientSession() as session:
                url = f"http://{instance.host}:{instance.port}{path}"
                async with session.request(method, url) as response:
                    return await response.text()
        finally:
            self.connections[instance] -= 1

# Usage Example
async def main():
    lb = LoadBalancer()
    
    # Add service instances
    lb.add_instance('backend1.example.com', 8080, weight=2)
    lb.add_instance('backend2.example.com', 8080, weight=1)
    lb.add_instance('backend3.example.com', 8080, weight=1)
    
    # Start health checks
    asyncio.create_task(lb.check_instances_health())
    
    # Handle requests
    try:
        response = await lb.handle_request(
            '/api/users',
            method='GET',
            algorithm='weighted_round_robin'
        )
        print(f"Response: {response}")
    except Exception as e:
        print(f"Error: {str(e)}")

if __name__ == '__main__':
    asyncio.run(main())
```

## Design Considerations

### Architecture Planning
1. **Algorithm Selection**
   - Round Robin
   - Weighted Round Robin
   - Least Connections
   - IP Hash
   - Random

2. **Health Checking**
   - Active checks
   - Passive checks
   - Timeout settings
   - Failure thresholds

3. **Session Persistence**
   - Cookie-based
   - IP-based
   - Application-controlled
   - Sticky sessions

### Implementation Patterns

#### Session Affinity
```python
class SessionAwareLoadBalancer(LoadBalancer):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.session_mapping = {}
        
    def get_instance_for_session(
        self,
        session_id: str,
        algorithm: str = 'round_robin'
    ) -> ServiceInstance:
        if session_id in self.session_mapping:
            instance = self.session_mapping[session_id]
            if instance.healthy:
                return instance
                
        instance = self.get_instance(algorithm)
        self.session_mapping[session_id] = instance
        return instance
```

#### Rate Limiting
```python
class RateLimitedLoadBalancer(LoadBalancer):
    def __init__(self, rate_limit: int, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.rate_limit = rate_limit
        self.request_counts = {}
        
    async def handle_request(self, *args, **kwargs):
        instance = self.get_instance()
        current_time = time.time()
        
        # Clean old counts
        self.request_counts = {
            k: v for k, v in self.request_counts.items()
            if current_time - k < 60
        }
        
        # Check rate limit
        current_rate = sum(self.request_counts.values())
        if current_rate >= self.rate_limit:
            raise Exception("Rate limit exceeded")
            
        # Track request
        self.request_counts[current_time] = (
            self.request_counts.get(current_time, 0) + 1
        )
        
        return await super().handle_request(*args, **kwargs)
```

## Security Implementation

### SSL Termination
```nginx
# nginx.conf with SSL
server {
    listen 443 ssl;
    server_name example.com;
    
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    
    location / {
        proxy_pass http://backend_services;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Best Practices

### Implementation Guidelines
1. Implement proper health checks
2. Use appropriate load balancing algorithm
3. Handle session persistence when needed
4. Monitor instance health
5. Implement proper error handling

### Monitoring and Maintenance
```python
class LoadBalancerMetrics:
    def __init__(self):
        self.metrics = {
            'requests_total': 0,
            'requests_per_instance': {},
            'response_times': [],
            'errors': 0,
            'healthy_instances': 0
        }
        
    def track_request(
        self,
        instance: ServiceInstance,
        response_time: float,
        error: bool = False
    ):
        self.metrics['requests_total'] += 1
        self.metrics['requests_per_instance'][instance] = (
            self.metrics['requests_per_instance'].get(instance, 0) + 1
        )
        self.metrics['response_times'].append(response_time)
        if error:
            self.metrics['errors'] += 1
            
    def update_health_status(self, healthy_count: int):
        self.metrics['healthy_instances'] = healthy_count
        
    def get_metrics(self):
        return {
            **self.metrics,
            'average_response_time': (
                sum(self.metrics['response_times']) /
                len(self.metrics['response_times'])
                if self.metrics['response_times'] else 0
            )
        }
```

### Error Handling
1. Handle instance failures
2. Implement retry logic
3. Handle timeout scenarios
4. Manage connection pools
5. Monitor error rates

## Interview Tips

### Key Concepts
1. Load balancing algorithms
2. Health checking mechanisms
3. Session persistence
4. SSL termination
5. High availability

### Common Questions
1. What are different load balancing algorithms?
2. How to handle session persistence?
3. How to implement health checks?
4. What are the benefits of layer 4 vs layer 7 load balancing?
5. How to handle SSL termination?

### Real-world Scenarios
1. Implementing global server load balancing
2. Handling microservices load balancing
3. Managing container orchestration
4. Implementing API gateway load balancing
5. Scaling web applications 