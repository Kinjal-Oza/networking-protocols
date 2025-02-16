# REST (Representational State Transfer)

## Overview
REST is an architectural style for distributed systems, emphasizing scalability, statelessness, and a uniform interface. It uses standard HTTP methods and status codes, making it the foundation for modern web APIs and services.

## Technical Details

### Architectural Constraints

1. **Client-Server Architecture**
   - Separation of concerns
   - Independent evolution
   - Interface contract
   - Resource-based interaction

2. **Statelessness**
   - No client context stored
   - Each request is complete
   - Session state in client
   - Improved scalability

3. **Cacheability**
   - Response must be cacheable
   - Cache controls defined
   - Improved performance
   - Reduced server load

4. **Uniform Interface**
   - Resource identification
   - Resource manipulation
   - Self-descriptive messages
   - HATEOAS (Hypermedia)

5. **Layered System**
   - Hierarchical layers
   - Intermediary servers
   - Load balancing
   - Security enforcement

### HTTP Methods

1. **CRUD Operations**
   ```
   GET     /users          # List users
   GET     /users/{id}     # Get user details
   POST    /users          # Create user
   PUT     /users/{id}     # Update user (full)
   PATCH   /users/{id}     # Update user (partial)
   DELETE  /users/{id}     # Delete user
   ```

2. **Method Properties**
   ```
   Safe Methods:
   - GET
   - HEAD
   - OPTIONS

   Idempotent Methods:
   - GET
   - HEAD
   - PUT
   - DELETE
   - OPTIONS
   ```

## Implementation

### API Design

1. **Resource Modeling**
   ```javascript
   // User Resource
   {
     "id": "123",
     "username": "john_doe",
     "email": "john@example.com",
     "profile": {
       "firstName": "John",
       "lastName": "Doe",
       "avatar": "https://example.com/avatars/123.jpg"
     },
     "created": "2024-01-15T10:30:00Z",
     "_links": {
       "self": { "href": "/users/123" },
       "posts": { "href": "/users/123/posts" },
       "followers": { "href": "/users/123/followers" }
     }
   }
   ```

2. **URL Structure**
   ```
   # Resource Hierarchy
   /users
   /users/{id}
   /users/{id}/posts
   /users/{id}/posts/{post_id}
   /users/{id}/followers
   
   # Query Parameters
   /users?page=2&limit=10
   /users?sort=created&order=desc
   /posts?category=tech&status=published
   ```

### Server Implementation (Node.js/Express)

1. **Basic REST Server**
   ```javascript
   const express = require('express');
   const app = express();

   // Middleware
   app.use(express.json());
   app.use(express.urlencoded({ extended: true }));

   // User Routes
   app.get('/users', async (req, res) => {
     try {
       const { page = 1, limit = 10 } = req.query;
       const users = await UserModel.findPaginated(page, limit);
       
       res.json({
         data: users,
         pagination: {
           page: parseInt(page),
           limit: parseInt(limit),
           total: await UserModel.count()
         }
       });
     } catch (error) {
       res.status(500).json({
         error: 'Internal Server Error',
         message: error.message
       });
     }
   });

   app.post('/users', async (req, res) => {
     try {
       const userData = req.body;
       const validationResult = validateUser(userData);
       
       if (!validationResult.valid) {
         return res.status(400).json({
           error: 'Validation Error',
           details: validationResult.errors
         });
       }
       
       const user = await UserModel.create(userData);
       res.status(201).json(user);
     } catch (error) {
       res.status(500).json({
         error: 'Internal Server Error',
         message: error.message
       });
     }
   });
   ```

2. **Advanced Features**
   ```javascript
   // HATEOAS Middleware
   function addHATEOAS(req, res, next) {
     const originalJson = res.json;
     res.json = function(obj) {
       if (obj.data) {
         obj.data = obj.data.map(item => ({
           ...item,
           _links: {
             self: { href: `/users/${item.id}` },
             posts: { href: `/users/${item.id}/posts` }
           }
         }));
       }
       return originalJson.call(this, obj);
     };
     next();
   }

   // Conditional Requests
   app.get('/users/:id', async (req, res) => {
     const user = await UserModel.findById(req.params.id);
     const etag = generateETag(user);
     
     if (req.headers['if-none-match'] === etag) {
       return res.status(304).end();
     }
     
     res.set('ETag', etag);
     res.json(user);
   });

   // Rate Limiting
   const rateLimit = require('express-rate-limit');
   
   app.use('/api/', rateLimit({
     windowMs: 15 * 60 * 1000, // 15 minutes
     max: 100 // limit each IP to 100 requests per windowMs
   }));
   ```

### Client Implementation

1. **Axios Client**
   ```javascript
   const axios = require('axios');

   class RESTClient {
     constructor(baseURL) {
       this.client = axios.create({
         baseURL,
         timeout: 5000,
         headers: {
           'Content-Type': 'application/json'
         }
       });
       
       // Request Interceptor
       this.client.interceptors.request.use(
         config => {
           const token = getAuthToken();
           if (token) {
             config.headers.Authorization = `Bearer ${token}`;
           }
           return config;
         },
         error => Promise.reject(error)
       );
       
       // Response Interceptor
       this.client.interceptors.response.use(
         response => response.data,
         error => {
           if (error.response.status === 401) {
             // Handle token refresh
             return this.refreshToken().then(() => {
               return this.client(error.config);
             });
           }
           return Promise.reject(error);
         }
       );
     }
     
     async getUsers(params = {}) {
       return this.client.get('/users', { params });
     }
     
     async createUser(userData) {
       return this.client.post('/users', userData);
     }
     
     async updateUser(id, userData) {
       return this.client.put(`/users/${id}`, userData);
     }
     
     async deleteUser(id) {
       return this.client.delete(`/users/${id}`);
     }
   }
   ```

## Design Considerations

### Performance Optimization

1. **Caching Strategy**
   ```javascript
   // Server-side caching
   const cache = require('memory-cache');

   app.get('/users', async (req, res) => {
     const cacheKey = `users_${JSON.stringify(req.query)}`;
     const cached = cache.get(cacheKey);
     
     if (cached) {
       return res.json(cached);
     }
     
     const users = await UserModel.find(req.query);
     cache.put(cacheKey, users, 5 * 60 * 1000); // 5 minutes
     res.json(users);
   });

   // Cache headers
   app.get('/users/:id', async (req, res) => {
     const user = await UserModel.findById(req.params.id);
     
     res.set({
       'Cache-Control': 'public, max-age=300',
       'Last-Modified': user.updatedAt.toUTCString(),
       'ETag': generateETag(user)
     });
     
     res.json(user);
   });
   ```

2. **Pagination and Filtering**
   ```javascript
   function buildQuery(params) {
     const query = {};
     const { search, status, category, startDate, endDate } = params;
     
     if (search) {
       query.$text = { $search: search };
     }
     
     if (status) {
       query.status = status;
     }
     
     if (category) {
       query.category = category;
     }
     
     if (startDate || endDate) {
       query.createdAt = {};
       if (startDate) query.createdAt.$gte = new Date(startDate);
       if (endDate) query.createdAt.$lte = new Date(endDate);
     }
     
     return query;
   }
   ```

### Security Considerations

1. **Authentication**
   ```javascript
   const jwt = require('jsonwebtoken');

   function authenticateToken(req, res, next) {
     const authHeader = req.headers['authorization'];
     const token = authHeader && authHeader.split(' ')[1];
     
     if (!token) {
       return res.status(401).json({
         error: 'Authentication required'
       });
     }
     
     try {
       const user = jwt.verify(token, process.env.JWT_SECRET);
       req.user = user;
       next();
     } catch (error) {
       res.status(403).json({
         error: 'Invalid token'
       });
     }
   }
   ```

2. **Input Validation**
   ```javascript
   const Joi = require('joi');

   const userSchema = Joi.object({
     username: Joi.string().alphanum().min(3).max(30).required(),
     email: Joi.string().email().required(),
     password: Joi.string().pattern(new RegExp('^[a-zA-Z0-9]{3,30}$')),
     profile: Joi.object({
       firstName: Joi.string().max(50),
       lastName: Joi.string().max(50)
     })
   });

   function validateUser(req, res, next) {
     const { error } = userSchema.validate(req.body);
     if (error) {
       return res.status(400).json({
         error: 'Validation Error',
         details: error.details
       });
     }
     next();
   }
   ```

## Best Practices

### API Design Guidelines

1. **Response Format**
   ```javascript
   // Success Response
   {
     "status": "success",
     "data": {
       "id": "123",
       "username": "john_doe",
       "email": "john@example.com"
     },
     "meta": {
       "timestamp": "2024-01-15T10:30:00Z"
     }
   }

   // Error Response
   {
     "status": "error",
     "error": {
       "code": "VALIDATION_ERROR",
       "message": "Invalid input",
       "details": [
         {
           "field": "email",
           "message": "Invalid email format"
         }
       ]
     }
   }
   ```

2. **Versioning Strategy**
   ```javascript
   // URL Versioning
   app.use('/api/v1', v1Router);
   app.use('/api/v2', v2Router);

   // Header Versioning
   app.use((req, res, next) => {
     const version = req.headers['accept-version'] || '1.0';
     req.apiVersion = version;
     next();
   });
   ```

## Interview Tips
- Understand REST principles
- Know HTTP methods and status codes
- Explain resource modeling
- Understand HATEOAS
- Be familiar with:
  - API design patterns
  - Authentication methods
  - Caching strategies
  - Security best practices
- Real-world scenarios:
  - API versioning
  - Rate limiting
  - Error handling
  - Performance optimization
- Best practices:
  - Resource naming
  - Status code usage
  - Response formatting
  - Security implementation
- Advanced concepts:
  - HATEOAS
  - Content negotiation
  - Conditional requests
  - API documentation 