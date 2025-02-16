# GraphQL

## Overview
GraphQL is a query language and runtime for APIs that enables declarative data fetching. It allows clients to request specific data, making it possible to get all required data in a single request. GraphQL provides a complete description of the data in your API and gives clients the power to ask for exactly what they need.

## Technical Details

### Schema Definition

1. **Types and Fields**
   ```graphql
   type User {
     id: ID!
     name: String!
     email: String!
     posts: [Post!]!
     role: UserRole
     createdAt: DateTime!
   }

   type Post {
     id: ID!
     title: String!
     content: String!
     author: User!
     comments: [Comment!]!
     tags: [String!]
     published: Boolean!
   }

   enum UserRole {
     ADMIN
     EDITOR
     USER
   }

   scalar DateTime
   ```

2. **Queries and Mutations**
   ```graphql
   type Query {
     user(id: ID!): User
     users(filter: UserFilter): [User!]!
     posts(authorId: ID, status: PostStatus): [Post!]!
     search(term: String!): [SearchResult!]!
   }

   type Mutation {
     createUser(input: CreateUserInput!): User!
     updateUser(id: ID!, input: UpdateUserInput!): User!
     deleteUser(id: ID!): Boolean!
     publishPost(id: ID!): Post!
   }

   union SearchResult = User | Post | Comment
   ```

### Query Operations

1. **Basic Queries**
   ```graphql
   query GetUser {
     user(id: "123") {
       id
       name
       email
       posts {
         id
         title
         comments {
           content
           author {
             name
           }
         }
       }
     }
   }
   ```

2. **Mutations**
   ```graphql
   mutation CreateNewUser {
     createUser(input: {
       name: "John Doe"
       email: "john@example.com"
       role: USER
     }) {
       id
       name
       email
       createdAt
     }
   }
   ```

3. **Subscriptions**
   ```graphql
   subscription OnNewComment {
     commentAdded(postId: "123") {
       id
       content
       author {
         name
       }
       createdAt
     }
   }
   ```

## Implementation

### Server Implementation (Node.js)

1. **Schema Definition**
   ```javascript
   const typeDefs = gql`
     type User {
       id: ID!
       name: String!
       email: String!
       posts: [Post!]!
     }

     type Post {
       id: ID!
       title: String!
       content: String!
       author: User!
     }

     type Query {
       user(id: ID!): User
       posts: [Post!]!
     }

     type Mutation {
       createPost(title: String!, content: String!): Post!
     }
   `;
   ```

2. **Resolvers**
   ```javascript
   const resolvers = {
     Query: {
       user: async (_, { id }, context) => {
         // Authorization check
         if (!context.isAuthenticated) {
           throw new AuthenticationError('Must be logged in');
         }
         
         // Data fetching
         const user = await User.findById(id);
         if (!user) {
           throw new UserInputError('User not found');
         }
         
         return user;
       },
       posts: async (_, __, context) => {
         return await Post.find();
       }
     },
     
     User: {
       posts: async (parent) => {
         return await Post.find({ authorId: parent.id });
       }
     },
     
     Mutation: {
       createPost: async (_, { title, content }, context) => {
         const post = new Post({
           title,
           content,
           authorId: context.userId
         });
         return await post.save();
       }
     }
   };
   ```

### Client Implementation (React)

1. **Query Component**
   ```javascript
   import { useQuery, gql } from '@apollo/client';

   const GET_USER = gql`
     query GetUser($id: ID!) {
       user(id: $id) {
         id
         name
         email
         posts {
           id
           title
         }
       }
     }
   `;

   function UserProfile({ userId }) {
     const { loading, error, data } = useQuery(GET_USER, {
       variables: { id: userId },
     });

     if (loading) return <Loading />;
     if (error) return <Error message={error.message} />;

     return (
       <div>
         <h1>{data.user.name}</h1>
         <p>{data.user.email}</p>
         <PostList posts={data.user.posts} />
       </div>
     );
   }
   ```

2. **Mutation Hook**
   ```javascript
   import { useMutation, gql } from '@apollo/client';

   const CREATE_POST = gql`
     mutation CreatePost($title: String!, $content: String!) {
       createPost(title: $title, content: $content) {
         id
         title
         content
       }
     }
   `;

   function CreatePostForm() {
     const [createPost, { loading }] = useMutation(CREATE_POST, {
       update(cache, { data: { createPost } }) {
         cache.modify({
           fields: {
             posts(existingPosts = []) {
               const newPostRef = cache.writeFragment({
                 data: createPost,
                 fragment: gql`
                   fragment NewPost on Post {
                     id
                     title
                     content
                   }
                 `
               });
               return [...existingPosts, newPostRef];
             }
           }
         });
       }
     });

     const handleSubmit = async (e) => {
       e.preventDefault();
       await createPost({
         variables: {
           title: formData.title,
           content: formData.content
         }
       });
     };

     return (/* form implementation */);
   }
   ```

## Design Considerations

### Performance Optimization

1. **Field Selection**
   ```javascript
   // Resolver-level complexity calculation
   const resolvers = {
     Query: {
       posts: {
         resolve: async (_, args, context) => {
           return await Post.find();
         },
         complexity: (args, childComplexity) => {
           return childComplexity * (args.limit || 10);
         }
       }
     }
   };
   ```

2. **Batching and Caching**
   ```javascript
   import DataLoader from 'dataloader';

   const userLoader = new DataLoader(async (ids) => {
     const users = await User.find({ _id: { $in: ids } });
     return ids.map(id => users.find(user => user.id === id));
   });
   ```

### Error Handling

1. **Custom Error Types**
   ```javascript
   class ValidationError extends ApolloError {
     constructor(message) {
       super(message, 'VALIDATION_ERROR');
     }
   }

   const resolvers = {
     Mutation: {
       createUser: async (_, { input }) => {
         if (!isValidEmail(input.email)) {
           throw new ValidationError('Invalid email format');
         }
         // Continue with user creation
       }
     }
   };
   ```

## Security Considerations

### Authentication and Authorization

1. **Context Setup**
   ```javascript
   const server = new ApolloServer({
     typeDefs,
     resolvers,
     context: async ({ req }) => {
       const token = req.headers.authorization;
       const user = await validateToken(token);
       return {
         user,
         isAuthenticated: !!user,
         dataSources: {
           userAPI: new UserAPI(),
           postAPI: new PostAPI()
         }
       };
     }
   });
   ```

2. **Directive-based Authorization**
   ```graphql
   directive @auth(requires: Role = USER) on OBJECT | FIELD_DEFINITION

   enum Role {
     ADMIN
     USER
     GUEST
   }

   type User @auth(requires: ADMIN) {
     id: ID!
     email: String!
     role: Role!
   }
   ```

## Best Practices

### Schema Design

1. **Type Naming**
   ```graphql
   type User {
     id: ID!
     firstName: String!
     lastName: String!
     fullName: String!
     email: String!
   }

   input CreateUserInput {
     firstName: String!
     lastName: String!
     email: String!
   }
   ```

2. **Pagination**
   ```graphql
   type Query {
     users(first: Int, after: String): UserConnection!
   }

   type UserConnection {
     edges: [UserEdge!]!
     pageInfo: PageInfo!
   }

   type UserEdge {
     node: User!
     cursor: String!
   }

   type PageInfo {
     hasNextPage: Boolean!
     endCursor: String
   }
   ```

## Interview Tips
- Understand GraphQL concepts
- Know schema definition language
- Explain resolver patterns
- Understand N+1 problem
- Be familiar with:
  - Query language
  - Type system
  - Execution model
  - Client tooling
- Real-world scenarios:
  - API aggregation
  - Mobile applications
  - Microservices
  - Real-time updates
- Best practices:
  - Schema design
  - Performance optimization
  - Error handling
  - Security implementation
- Advanced concepts:
  - Directives
  - Subscriptions
  - Schema stitching
  - Federation 