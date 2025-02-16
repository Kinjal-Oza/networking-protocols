# Circuit Breaker Pattern

## Overview
The Circuit Breaker is a design pattern used in modern software development that prevents an application from repeatedly trying to execute an operation that's likely to fail. It allows you to continue without waiting for the fault to be fixed or wasting CPU cycles while it's being fixed.

## Technical Details

### Core Components
1. **Circuit States**:
   - Closed (normal operation)
   - Open (failure mode)
   - Half-Open (testing recovery)

2. **Failure Detection**:
   - Error count/rate tracking
   - Timeout monitoring
   - Resource usage monitoring

3. **Recovery Mechanism**:
   - Fallback operations
   - Retry policies
   - Recovery testing

### Common Implementations
1. **Netflix Hystrix**: Java-based implementation
2. **Resilience4j**: Lightweight Java implementation
3. **Polly**: .NET implementation
4. **Custom implementations**: Language-specific solutions

## Implementation

### Basic Circuit Breaker (Python)
```python
import time
from functools import wraps
from enum import Enum
import logging
import asyncio

class CircuitState(Enum):
    CLOSED = "CLOSED"
    OPEN = "OPEN"
    HALF_OPEN = "HALF_OPEN"

class CircuitBreaker:
    def __init__(
        self,
        failure_threshold=5,
        recovery_timeout=60,
        half_open_max_calls=3
    ):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.half_open_max_calls = half_open_max_calls
        
        self.state = CircuitState.CLOSED
        self.failures = 0
        self.last_failure_time = None
        self.half_open_calls = 0
        
        self.logger = logging.getLogger(__name__)
        
    def __call__(self, func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            return await self.call(func, *args, **kwargs)
        return wrapper
        
    async def call(self, func, *args, **kwargs):
        if self.state == CircuitState.OPEN:
            if self.should_attempt_recovery():
                self.state = CircuitState.HALF_OPEN
                self.logger.info("Circuit switched to HALF-OPEN")
            else:
                raise CircuitBreakerError("Circuit is OPEN")
                
        if self.state == CircuitState.HALF_OPEN:
            if self.half_open_calls >= self.half_open_max_calls:
                raise CircuitBreakerError(
                    "Maximum half-open calls reached"
                )
            self.half_open_calls += 1
            
        try:
            result = await func(*args, **kwargs)
            self.handle_success()
            return result
        except Exception as e:
            self.handle_failure(e)
            raise
            
    def handle_success(self):
        if self.state == CircuitState.HALF_OPEN:
            self.state = CircuitState.CLOSED
            self.failures = 0
            self.half_open_calls = 0
            self.last_failure_time = None
            self.logger.info("Circuit switched to CLOSED")
            
    def handle_failure(self, exception):
        self.failures += 1
        self.last_failure_time = time.time()
        
        if self.state == CircuitState.CLOSED:
            if self.failures >= self.failure_threshold:
                self.state = CircuitState.OPEN
                self.logger.warning(
                    f"Circuit switched to OPEN after {self.failures} failures"
                )
                
        elif self.state == CircuitState.HALF_OPEN:
            self.state = CircuitState.OPEN
            self.logger.warning("Circuit switched back to OPEN from HALF-OPEN")
            
    def should_attempt_recovery(self):
        if not self.last_failure_time:
            return False
        return time.time() - self.last_failure_time >= self.recovery_timeout
        
class CircuitBreakerError(Exception):
    pass

# Usage Example
@CircuitBreaker(failure_threshold=3, recovery_timeout=30)
async def external_service_call():
    # Simulate external service call
    if random.random() < 0.7:  # 70% failure rate
        raise Exception("Service call failed")
    return "Success"

# Example with fallback
class CircuitBreakerWithFallback(CircuitBreaker):
    def __init__(self, fallback_function, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fallback_function = fallback_function
        
    async def call(self, func, *args, **kwargs):
        try:
            return await super().call(func, *args, **kwargs)
        except CircuitBreakerError:
            return await self.fallback_function(*args, **kwargs)

async def fallback_function():
    return "Fallback response"

@CircuitBreakerWithFallback(
    fallback_function=fallback_function,
    failure_threshold=3
)
async def service_with_fallback():
    # Main service call
    pass
```

### Resilience4j Implementation (Java)
```java
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
import io.vavr.control.Try;

import java.time.Duration;

public class UserService {
    private final CircuitBreaker circuitBreaker;
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
        
        CircuitBreakerConfig config = CircuitBreakerConfig.custom()
            .failureRateThreshold(50)
            .waitDurationInOpenState(Duration.ofMillis(1000))
            .permittedNumberOfCallsInHalfOpenState(2)
            .slidingWindowSize(2)
            .build();
            
        this.circuitBreaker = CircuitBreaker.of("userService", config);
    }
    
    public User getUser(String userId) {
        return Try.ofSupplier(
            CircuitBreaker.decorateSupplier(
                circuitBreaker,
                () -> userRepository.findById(userId)
            )
        ).recover(throwable -> getDefaultUser()).get();
    }
    
    private User getDefaultUser() {
        return new User("default", "Default User");
    }
}

@RestController
public class UserController {
    private final UserService userService;
    
    @GetMapping("/users/{userId}")
    public ResponseEntity<User> getUser(@PathVariable String userId) {
        try {
            User user = userService.getUser(userId);
            return ResponseEntity.ok(user);
        } catch (Exception e) {
            return ResponseEntity.status(503).build();
        }
    }
}
```

### Spring Cloud Circuit Breaker
```java
@Configuration
public class CircuitBreakerConfiguration {
    @Bean
    public Customizer<Resilience4JCircuitBreakerFactory> defaultCustomizer() {
        return factory -> factory.configureDefault(id -> new Resilience4JConfigBuilder(id)
            .circuitBreakerConfig(CircuitBreakerConfig.custom()
                .failureRateThreshold(50)
                .waitDurationInOpenState(Duration.ofSeconds(30))
                .permittedNumberOfCallsInHalfOpenState(2)
                .slidingWindowSize(5)
                .build())
            .timeLimiterConfig(TimeLimiterConfig.custom()
                .timeoutDuration(Duration.ofSeconds(3))
                .build())
            .build());
    }
}

@Service
public class UserService {
    private final CircuitBreakerFactory circuitBreakerFactory;
    
    public User getUser(String userId) {
        return circuitBreakerFactory.create("userService")
            .run(() -> userRepository.findById(userId),
                throwable -> getDefaultUser());
    }
}
```

## Design Considerations

### Architecture Planning
1. **Failure Detection**
   - Error thresholds
   - Timeout settings
   - Failure categories
   - Health checks

2. **Recovery Strategy**
   - Fallback mechanisms
   - Retry policies
   - Cache strategies
   - Graceful degradation

3. **Monitoring**
   - Circuit state
   - Failure rates
   - Response times
   - Resource usage

### Implementation Patterns

#### Bulkhead Pattern Integration
```python
class BulkheadCircuitBreaker(CircuitBreaker):
    def __init__(
        self,
        max_concurrent_calls=10,
        *args,
        **kwargs
    ):
        super().__init__(*args, **kwargs)
        self.semaphore = asyncio.Semaphore(max_concurrent_calls)
        
    async def call(self, func, *args, **kwargs):
        async with self.semaphore:
            return await super().call(func, *args, **kwargs)
```

#### Retry Pattern Integration
```python
class RetryingCircuitBreaker(CircuitBreaker):
    def __init__(
        self,
        max_retries=3,
        retry_delay=1,
        *args,
        **kwargs
    ):
        super().__init__(*args, **kwargs)
        self.max_retries = max_retries
        self.retry_delay = retry_delay
        
    async def call(self, func, *args, **kwargs):
        for attempt in range(self.max_retries):
            try:
                return await super().call(func, *args, **kwargs)
            except Exception as e:
                if attempt == self.max_retries - 1:
                    raise
                await asyncio.sleep(
                    self.retry_delay * (attempt + 1)
                )
```

## Security Implementation

### Secure Circuit Breaker
```python
class SecureCircuitBreaker(CircuitBreaker):
    def __init__(self, auth_service, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.auth_service = auth_service
        
    async def call(self, func, *args, **kwargs):
        if not await self.auth_service.is_authorized():
            raise SecurityException("Unauthorized access")
            
        return await super().call(func, *args, **kwargs)
```

## Best Practices

### Implementation Guidelines
1. Set appropriate thresholds
2. Implement proper logging
3. Use appropriate timeouts
4. Implement fallback mechanisms
5. Monitor circuit state

### Monitoring and Maintenance
```python
class CircuitBreakerMetrics:
    def __init__(self):
        self.metrics = {
            'total_calls': 0,
            'successful_calls': 0,
            'failed_calls': 0,
            'circuit_trips': 0,
            'average_response_time': 0,
            'current_state': None
        }
        
    def track_call(self, start_time, success):
        self.metrics['total_calls'] += 1
        if success:
            self.metrics['successful_calls'] += 1
        else:
            self.metrics['failed_calls'] += 1
            
        end_time = time.time()
        response_time = end_time - start_time
        
        # Update average response time
        current_avg = self.metrics['average_response_time']
        total_calls = self.metrics['total_calls']
        self.metrics['average_response_time'] = (
            (current_avg * (total_calls - 1) + response_time) / total_calls
        )
        
    def track_state_change(self, new_state):
        if (new_state == CircuitState.OPEN and 
            self.metrics['current_state'] != CircuitState.OPEN):
            self.metrics['circuit_trips'] += 1
        self.metrics['current_state'] = new_state
        
    def get_metrics(self):
        return self.metrics
```

### Error Handling
1. Handle timeout exceptions
2. Manage resource cleanup
3. Implement graceful degradation
4. Log detailed error information
5. Monitor failure patterns

## Interview Tips

### Key Concepts
1. Circuit breaker states and transitions
2. Failure detection mechanisms
3. Recovery strategies
4. Fallback implementations
5. Monitoring and metrics

### Common Questions
1. What are the different states of a circuit breaker?
2. How to handle partial failures?
3. When to use circuit breakers?
4. How to implement fallback mechanisms?
5. How to monitor circuit breaker health?

### Real-world Scenarios
1. Protecting database calls
2. Managing external service calls
3. Handling API rate limits
4. Implementing graceful degradation
5. Managing distributed systems 