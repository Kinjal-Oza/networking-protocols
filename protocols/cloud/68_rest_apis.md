# REST APIs (Representational State Transfer)

## Overview
REST (Representational State Transfer) is an architectural style for designing networked applications. It uses a stateless, client-server, cacheable communications protocol -- typically HTTP. REST APIs are the foundation of modern web services, providing a standardized way to expose and interact with resources over the internet.

## Technical Details

### Core Principles (REST Constraints)

1. **Client-Server Architecture**
   - Separation of concerns
   - Independent evolution
   - Resource management
   - Interface consistency
   - Scalability support

2. **Statelessness**
   - No client context stored
   - Each request is complete
   - Session state client responsibility
   - Improved reliability
   - Easier load balancing

3. **Cacheability**
   - Response must be cacheable
   - Cache-Control headers
   - ETag implementation
   - Conditional requests
   - Performance optimization

4. **Uniform Interface**
   - Resource identification
   - Resource manipulation through representations
   - Self-descriptive messages
   - HATEOAS (Hypermedia as the Engine of Application State)

5. **Layered System**
   - Hierarchical layers
   - Intermediary servers
   - Load balancing
   - Shared caches
   - Security enforcement

### HTTP Methods

1. **CRUD Operations**
   ```
   GET    - Retrieve resource
   POST   - Create resource
   PUT    - Update resource (complete)
   PATCH  - Update resource (partial)
   DELETE - Remove resource
   ```

2. **Additional Methods**
   ```
   HEAD   - Get headers only
   OPTIONS - Get supported methods
   TRACE  - Echo request
   CONNECT - Tunnel connection
   ```

## Implementation

### Basic REST API Structure

1. **Resource Endpoints**
   ```
   # Collection endpoints
   GET    /api/v1/users
   POST   /api/v1/users

   # Single resource endpoints
   GET    /api/v1/users/{id}
   PUT    /api/v1/users/{id}
   PATCH  /api/v1/users/{id}
   DELETE /api/v1/users/{id}
   ```

2. **Query Parameters**
   ```
   # Filtering
   GET /api/v1/users?status=active

   # Sorting
   GET /api/v1/users?sort=name:asc

   # Pagination
   GET /api/v1/users?page=2&limit=10

   # Field selection
   GET /api/v1/users?fields=id,name,email
   ```

### Response Formats

1. **JSON Structure**
   ```json
   {
     "data": {
       "id": "123",
       "type": "users",
       "attributes": {
         "name": "John Doe",
         "email": "john@example.com"
       },
       "relationships": {
         "orders": {
           "data": [
             { "id": "456", "type": "orders" }
           ]
         }
       }
     },
     "meta": {
       "timestamp": "2024-03-20T10:00:00Z"
     }
   }
   ```

2. **Error Response**
   ```json
   {
     "errors": [{
       "status": "404",
       "title": "Resource not found",
       "detail": "User with ID 123 does not exist",
       "code": "USER_NOT_FOUND"
     }]
   }
   ```

## Design Considerations

### API Versioning
1. **URL Versioning**
   ```
   /api/v1/users
   /api/v2/users
   ```

2. **Header Versioning**
   ```
   Accept: application/vnd.company.api+json;version=1
   ```

3. **Query Parameter**
   ```
   /api/users?version=1
   ```

### Security Implementation

1. **Authentication**
   ```
   # Bearer Token
   Authorization: Bearer <token>

   # API Key
   X-API-Key: <api_key>

   # OAuth 2.0
   Authorization: Bearer <oauth_token>
   ```

2. **Rate Limiting**
   ```
   # Response Headers
   X-RateLimit-Limit: 100
   X-RateLimit-Remaining: 95
   X-RateLimit-Reset: 1616789000
   ```

## Best Practices

### API Design Guidelines

1. **Resource Naming**
   ```
   # Use nouns for resources
   /users
   /orders
   /products

   # Use plural forms
   /users (not /user)
   /categories (not /category)
   ```

2. **Status Codes**
   ```
   200 OK           - Success
   201 Created      - Resource created
   204 No Content   - Success, no response body
   400 Bad Request  - Invalid request
   401 Unauthorized - Authentication required
   403 Forbidden    - Permission denied
   404 Not Found    - Resource not found
   500 Server Error - Internal error
   ```

### Performance Optimization

1. **Caching Headers**
   ```
   Cache-Control: public, max-age=3600
   ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
   Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT
   ```

2. **Compression**
   ```
   Accept-Encoding: gzip, deflate
   Content-Encoding: gzip
   ```

## Security Considerations

### Request Validation

1. **Input Sanitization**
   ```python
   def validate_user_input(data):
       if not data.get('email'):
           raise ValidationError('Email is required')
       if not re.match(r'^[\w\.-]+@[\w\.-]+\.\w+$', data['email']):
           raise ValidationError('Invalid email format')
   ```

2. **CORS Configuration**
   ```
   Access-Control-Allow-Origin: https://trusted-domain.com
   Access-Control-Allow-Methods: GET, POST, PUT, DELETE
   Access-Control-Allow-Headers: Content-Type, Authorization
   ```

## Interview Tips
- Understand REST principles
- Know HTTP methods and status codes
- Explain API versioning approaches
- Understand security best practices
- Be familiar with:
  - Resource naming
  - Response formats
  - Authentication methods
  - Caching strategies
- Real-world scenarios:
  - API design decisions
  - Security implementation
  - Performance optimization
  - Error handling
- Best practices:
  - Consistent naming
  - Proper status codes
  - Security headers
  - Documentation
- Advanced concepts:
  - HATEOAS
  - Content negotiation
  - Rate limiting
  - API gateways 