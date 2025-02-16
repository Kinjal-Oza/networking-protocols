# CQRS (Command Query Responsibility Segregation)

## Overview
CQRS is an architectural pattern that separates read and write operations for a data store. Commands handle write operations (create, update, delete) while queries handle read operations, allowing each side to be optimized independently.

## Technical Details

### Core Components

1. **Command Side**
   - Command Models
   - Command Handlers
   - Write Database
   - Validation Logic
   - Domain Logic

2. **Query Side**
   - Query Models
   - Query Handlers
   - Read Database
   - Denormalized Views
   - Optimization Logic

### Characteristics
- Separate read/write models
- Different data stores possible
- Eventual consistency
- Scalable independently
- Optimized performance

## Implementation

### Python Example with FastAPI

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List, Optional
import asyncpg
from datetime import datetime

app = FastAPI()

# Command Models
class CreateOrderCommand(BaseModel):
    user_id: str
    product_id: str
    quantity: int
    
class UpdateOrderCommand(BaseModel):
    order_id: str
    quantity: int

# Query Models
class OrderQuery(BaseModel):
    order_id: str
    user_id: str
    product_id: str
    quantity: int
    status: str
    created_at: datetime

# Command Handler
class OrderCommandHandler:
    async def create_order(self, command: CreateOrderCommand):
        # Write to command database
        async with asyncpg.create_pool('postgresql://user:pass@localhost/write_db') as pool:
            async with pool.acquire() as conn:
                order_id = await conn.fetchval('''
                    INSERT INTO orders (user_id, product_id, quantity, status)
                    VALUES ($1, $2, $3, 'PENDING')
                    RETURNING order_id
                ''', command.user_id, command.product_id, command.quantity)
                
                # Publish event for query side
                await self.publish_event('OrderCreated', {
                    'order_id': order_id,
                    'user_id': command.user_id,
                    'product_id': command.product_id,
                    'quantity': command.quantity
                })
                
                return order_id

# Query Handler
class OrderQueryHandler:
    async def get_order(self, order_id: str) -> Optional[OrderQuery]:
        # Read from query database
        async with asyncpg.create_pool('postgresql://user:pass@localhost/read_db') as pool:
            async with pool.acquire() as conn:
                result = await conn.fetchrow('''
                    SELECT * FROM order_views WHERE order_id = $1
                ''', order_id)
                
                if result:
                    return OrderQuery(**dict(result))
                return None

# API Endpoints
@app.post("/orders/")
async def create_order(command: CreateOrderCommand):
    handler = OrderCommandHandler()
    order_id = await handler.create_order(command)
    return {"order_id": order_id}

@app.get("/orders/{order_id}")
async def get_order(order_id: str):
    handler = OrderQueryHandler()
    order = await handler.get_order(order_id)
    if not order:
        raise HTTPException(status_code=404, detail="Order not found")
    return order
```

### Database Schema Example

```sql
-- Write Database (Commands)
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    product_id VARCHAR(255) NOT NULL,
    quantity INTEGER NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Read Database (Queries)
CREATE TABLE order_views (
    order_id INTEGER PRIMARY KEY,
    user_id VARCHAR(255) NOT NULL,
    product_id VARCHAR(255) NOT NULL,
    quantity INTEGER NOT NULL,
    status VARCHAR(50) NOT NULL,
    created_at TIMESTAMP NOT NULL,
    product_name VARCHAR(255),
    user_email VARCHAR(255),
    total_price DECIMAL(10,2)
);
```

## Design Considerations

### Architecture Planning
1. **Data Consistency**
   - Event synchronization
   - Eventual consistency handling
   - Conflict resolution
   - Version tracking

2. **Performance Optimization**
   - Read model denormalization
   - Query optimization
   - Caching strategies
   - Scaling patterns

3. **Integration Strategy**
   - Event bus selection
   - Message reliability
   - Error handling
   - Retry policies

### Implementation Patterns
1. **Synchronization**
   - Event-driven updates
   - Batch synchronization
   - Real-time propagation
   - Conflict resolution

2. **Read Model Updates**
   - Projection updates
   - Cache invalidation
   - Background processing
   - Version management

## Security Implementation

1. **Command Authorization**
   - Command validation
   - Role-based access
   - Audit logging
   - Rate limiting

2. **Query Authorization**
   - Data filtering
   - View restrictions
   - Cache security
   - Access logging

## Best Practices

1. **Design Guidelines**
   - Keep commands simple
   - Optimize read models
   - Use event sourcing
   - Implement idempotency
   - Handle eventual consistency

2. **Performance Optimization**
   - Cache read models
   - Batch updates
   - Async processing
   - Query optimization

3. **Monitoring and Debugging**
   - Command tracking
   - Query performance
   - Sync monitoring
   - Error tracking

## Interview Tips

1. **Key Concepts**
   - Explain CQRS benefits
   - Discuss consistency models
   - Describe implementation approaches
   - Explain integration patterns

2. **Common Questions**
   - When to use CQRS?
   - How to handle consistency?
   - How to sync read/write models?
   - How to scale CQRS?

3. **Real-world Scenarios**
   - E-commerce platforms
   - Financial systems
   - Reporting systems
   - High-traffic applications 