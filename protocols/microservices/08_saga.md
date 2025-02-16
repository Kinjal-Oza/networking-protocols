# Saga Pattern

## Overview
The Saga Pattern is a distributed transaction management pattern that maintains data consistency across multiple microservices without tight coupling. It breaks down a distributed transaction into a sequence of local transactions, where each local transaction updates data within a single service and publishes events to trigger the next local transaction in the saga.

## Technical Details

### Core Components

1. **Saga Orchestrator**
   - Transaction coordination
   - State management
   - Error handling
   - Compensation logic
   - Event publishing

2. **Saga Participants**
   - Local transactions
   - Compensation handlers
   - Event handlers
   - State management
   - Domain logic

### Saga Types

1. **Choreography-based Saga**
   - Event-driven
   - Decentralized coordination
   - Service autonomy
   - Loose coupling

2. **Orchestration-based Saga**
   - Centralized coordination
   - Sequential execution
   - Transaction monitoring
   - Error management

## Implementation

### Python Example with FastAPI and RabbitMQ

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
import aio_pika
import asyncpg
from enum import Enum
from typing import List, Optional
from datetime import datetime

app = FastAPI()

# Saga States
class SagaState(str, Enum):
    STARTED = "STARTED"
    COMPLETED = "COMPLETED"
    FAILED = "FAILED"
    COMPENSATING = "COMPENSATING"

# Command Models
class CreateOrderCommand(BaseModel):
    user_id: str
    product_id: str
    quantity: int
    total_price: float

# Event Models
class OrderEvent(BaseModel):
    order_id: str
    event_type: str
    data: dict
    timestamp: datetime

# Saga Orchestrator
class OrderSagaOrchestrator:
    def __init__(self):
        self.saga_id: str = None
        self.state: SagaState = None
        self.current_step: int = 0
        self.compensation_steps: List[callable] = []
        
    async def start_saga(self, command: CreateOrderCommand):
        self.saga_id = str(uuid.uuid4())
        self.state = SagaState.STARTED
        
        try:
            # Step 1: Create Order
            order_id = await self.create_order(command)
            self.compensation_steps.append(self.cancel_order)
            
            # Step 2: Reserve Inventory
            await self.reserve_inventory(order_id, command.product_id, command.quantity)
            self.compensation_steps.append(self.release_inventory)
            
            # Step 3: Process Payment
            await self.process_payment(order_id, command.total_price)
            self.compensation_steps.append(self.refund_payment)
            
            # Complete Saga
            self.state = SagaState.COMPLETED
            await self.publish_event("OrderSagaCompleted", {
                "saga_id": self.saga_id,
                "order_id": order_id
            })
            
            return order_id
            
        except Exception as e:
            await self.compensate()
            raise HTTPException(status_code=500, detail=str(e))
    
    async def compensate(self):
        self.state = SagaState.COMPENSATING
        
        for compensation_step in reversed(self.compensation_steps):
            try:
                await compensation_step()
            except Exception as e:
                # Log compensation failure
                print(f"Compensation failed: {str(e)}")
                
        self.state = SagaState.FAILED

# Service Implementation
class OrderService:
    async def create_order(self, command: CreateOrderCommand) -> str:
        async with asyncpg.create_pool('postgresql://user:pass@localhost/orders_db') as pool:
            async with pool.acquire() as conn:
                order_id = await conn.fetchval('''
                    INSERT INTO orders (user_id, product_id, quantity, status)
                    VALUES ($1, $2, $3, 'PENDING')
                    RETURNING order_id
                ''', command.user_id, command.product_id, command.quantity)
                
                await self.publish_event('OrderCreated', {
                    'order_id': order_id,
                    'user_id': command.user_id,
                    'product_id': command.product_id,
                    'quantity': command.quantity
                })
                
                return order_id
    
    async def cancel_order(self, order_id: str):
        async with asyncpg.create_pool('postgresql://user:pass@localhost/orders_db') as pool:
            async with pool.acquire() as conn:
                await conn.execute('''
                    UPDATE orders SET status = 'CANCELLED'
                    WHERE order_id = $1
                ''', order_id)
                
                await self.publish_event('OrderCancelled', {
                    'order_id': order_id
                })

# API Endpoints
@app.post("/orders/")
async def create_order(command: CreateOrderCommand):
    orchestrator = OrderSagaOrchestrator()
    order_id = await orchestrator.start_saga(command)
    return {"order_id": order_id, "saga_id": orchestrator.saga_id}

@app.get("/orders/{order_id}/saga-status")
async def get_saga_status(order_id: str):
    # Implementation to retrieve saga status
    pass
```

### Database Schema Example

```sql
-- Orders Service
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    product_id VARCHAR(255) NOT NULL,
    quantity INTEGER NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Inventory Service
CREATE TABLE inventory (
    product_id VARCHAR(255) PRIMARY KEY,
    quantity INTEGER NOT NULL,
    reserved INTEGER DEFAULT 0
);

-- Payment Service
CREATE TABLE payments (
    payment_id SERIAL PRIMARY KEY,
    order_id VARCHAR(255) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Saga State Store
CREATE TABLE saga_instances (
    saga_id VARCHAR(255) PRIMARY KEY,
    saga_type VARCHAR(100) NOT NULL,
    current_step INTEGER NOT NULL,
    status VARCHAR(50) NOT NULL,
    payload JSONB,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## Design Considerations

### Architecture Planning
1. **Saga Coordination**
   - Choreography vs Orchestration
   - Event schema design
   - State persistence
   - Timeout handling

2. **Compensation Strategy**
   - Rollback mechanisms
   - Idempotency
   - Partial completion
   - Data consistency

3. **Monitoring and Tracking**
   - Saga state visibility
   - Transaction tracing
   - Error detection
   - Performance metrics

### Implementation Patterns
1. **Event Management**
   - Event schema versioning
   - Message reliability
   - Order guarantee
   - Duplicate handling

2. **Error Handling**
   - Retry policies
   - Circuit breaking
   - Timeout management
   - Compensation tracking

## Security Implementation

1. **Transaction Security**
   - Event encryption
   - Authentication
   - Authorization
   - Audit logging

2. **Data Protection**
   - Sensitive data handling
   - Access control
   - Encryption at rest
   - Secure communication

## Best Practices

1. **Design Guidelines**
   - Keep sagas simple
   - Minimize participants
   - Handle partial failures
   - Ensure idempotency
   - Maintain atomicity

2. **Performance Optimization**
   - Async processing
   - Batch operations
   - State caching
   - Event buffering

3. **Monitoring and Debugging**
   - Transaction tracking
   - State monitoring
   - Error logging
   - Performance metrics

## Interview Tips

1. **Key Concepts**
   - Explain saga pattern benefits
   - Compare with 2PC
   - Describe compensation
   - Explain consistency models

2. **Common Questions**
   - When to use sagas?
   - How to handle failures?
   - How to ensure consistency?
   - How to manage timeouts?

3. **Real-world Scenarios**
   - E-commerce orders
   - Banking transactions
   - Travel bookings
   - Supply chain management 