# 📅 Week 14 — Day 1: REST Principles & API Design 🏗️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand REST architecture and its constraints
- Design clean, intuitive API endpoints
- Master HTTP methods, status codes, and headers
- Learn resource naming conventions and URL design
- Plan API structure before writing code

---

## 📖 Lesson Content

### 1. What Is REST?

**REST** (Representational State Transfer) is an architectural style for building web APIs. Created by Roy Fielding in 2000.

**REST Constraints:**

| Constraint | Meaning |
|-----------|---------|
| **Client-Server** | Frontend and backend are separate systems |
| **Stateless** | Each request contains all information needed — server stores no session |
| **Cacheable** | Responses indicate if they can be cached |
| **Uniform Interface** | Consistent URL patterns, HTTP methods, and data formats |
| **Layered System** | Client doesn't know if it's talking to the server directly or through a proxy |

### 2. Resources & URL Design

A **resource** is any entity your API exposes. URLs should be **nouns**, not verbs:

```
✅ Good (nouns):
GET  /api/posts          → Get all posts
GET  /api/posts/5        → Get post with ID 5
GET  /api/posts/5/comments → Get comments for post 5

❌ Bad (verbs):
GET  /api/getPosts
GET  /api/getPostById/5
POST /api/createPost
POST /api/deletePost/5
```

**Naming Rules:**
- Use **plural nouns**: `/posts`, `/users`, `/comments`
- Use **lowercase** with **hyphens**: `/blog-posts`, not `/blogPosts`
- Use **nesting** for relationships: `/posts/5/comments`
- Keep URLs **shallow** — max 2-3 levels deep
- Never expose implementation details: avoid `/api/db/table/posts`

### 3. HTTP Methods — Complete Guide

| Method | Purpose | Request Body | Idempotent | Safe |
|--------|---------|-------------|------------|------|
| `GET` | Read resource(s) | No | ✅ | ✅ |
| `POST` | Create resource | Yes | ❌ | ❌ |
| `PUT` | Replace entire resource | Yes | ✅ | ❌ |
| `PATCH` | Partial update | Yes | ✅ | ❌ |
| `DELETE` | Remove resource | No | ✅ | ❌ |

**Idempotent** = calling it multiple times produces the same result.
**Safe** = doesn't modify data.

### 4. HTTP Status Codes — The Complete Reference

```
2xx — Success
  200 OK              → Request succeeded (GET, PUT, PATCH)
  201 Created         → Resource created (POST)
  204 No Content      → Success, no body to return (DELETE)

3xx — Redirection
  301 Moved Permanently
  304 Not Modified    → Use cached version

4xx — Client Error
  400 Bad Request     → Invalid input / validation failed
  401 Unauthorized    → Not authenticated (no token)
  403 Forbidden       → Authenticated but not allowed
  404 Not Found       → Resource doesn't exist
  405 Method Not Allowed
  409 Conflict        → Duplicate resource
  422 Unprocessable Entity → Semantic validation error
  429 Too Many Requests → Rate limited

5xx — Server Error
  500 Internal Server Error
  502 Bad Gateway
  503 Service Unavailable
```

### 5. Consistent Response Format

Always return a **consistent JSON structure**:

```js
// Success (single resource)
{
  "status": "success",
  "data": {
    "id": 1,
    "title": "My Post",
    "content": "Hello world"
  }
}

// Success (collection)
{
  "status": "success",
  "count": 2,
  "total": 50,
  "page": 1,
  "totalPages": 25,
  "data": [
    { "id": 1, "title": "Post 1" },
    { "id": 2, "title": "Post 2" }
  ]
}

// Error
{
  "status": "error",
  "message": "Post not found",
  "errors": []  // optional: field-level errors
}
```

### 6. Planning the Blog API

Before writing code, design the API on paper:

**Resources:**
- `Post` — Blog posts with title, content, author, category, tags
- `Comment` — Comments on posts
- `Category` — Post categories

**Endpoint Map:**

```
Posts:
  GET    /api/posts              → List all posts (paginated, filterable)
  GET    /api/posts/:id          → Get single post
  POST   /api/posts              → Create post
  PUT    /api/posts/:id          → Replace post
  PATCH  /api/posts/:id          → Partial update
  DELETE /api/posts/:id          → Delete post

Comments (nested under posts):
  GET    /api/posts/:id/comments     → List comments for a post
  POST   /api/posts/:id/comments     → Add comment to a post
  PUT    /api/posts/:id/comments/:commentId    → Update comment
  DELETE /api/posts/:id/comments/:commentId    → Delete comment

Categories:
  GET    /api/categories         → List all categories
  POST   /api/categories         → Create category
  PUT    /api/categories/:id     → Update category
  DELETE /api/categories/:id     → Delete category

Search & Filters:
  GET /api/posts?category=tech&sortBy=createdAt&order=desc&page=1&limit=10
  GET /api/posts?search=javascript&tag=tutorial
```

### 7. Data Models (In-Memory)

```js
// models/Post.js
let posts = [];
let nextPostId = 1;

export function createPostModel(data) {
  const post = {
    id: nextPostId++,
    title: data.title,
    slug: data.title.toLowerCase().replace(/[^a-z0-9]+/g, '-').replace(/(^-|-$)/g, ''),
    content: data.content,
    excerpt: data.content.substring(0, 150) + '...',
    author: data.author,
    categoryId: data.categoryId || null,
    tags: data.tags || [],
    status: data.status || 'draft',  // draft, published
    views: 0,
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };

  posts.push(post);
  return post;
}

export function getAllPosts() {
  return posts;
}

export function getPostById(id) {
  return posts.find(p => p.id === id);
}
```

### 8. Express Project Structure for Large APIs

```
blog-api/
├── src/
│   ├── controllers/
│   │   ├── postController.js
│   │   ├── commentController.js
│   │   └── categoryController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   ├── validate.js
│   │   └── notFound.js
│   ├── models/
│   │   ├── Post.js
│   │   ├── Comment.js
│   │   └── Category.js
│   ├── routes/
│   │   ├── index.js          ← Central route registry
│   │   ├── postRoutes.js
│   │   ├── commentRoutes.js
│   │   └── categoryRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   └── helpers.js
│   └── app.js
├── .env
├── .gitignore
├── index.js
└── package.json
```

### 9. Central Route Registry

```js
// src/routes/index.js
import { Router } from 'express';
import postRoutes from './postRoutes.js';
import commentRoutes from './commentRoutes.js';
import categoryRoutes from './categoryRoutes.js';

const router = Router();

router.use('/posts', postRoutes);
router.use('/posts', commentRoutes);  // Nested: /posts/:id/comments
router.use('/categories', categoryRoutes);

export default router;
```

```js
// src/app.js
import apiRoutes from './routes/index.js';

app.use('/api', apiRoutes);
// All routes now start with /api/
```

### 10. Content Negotiation & Headers

```js
// Indicate content type
res.set('Content-Type', 'application/json');

// Locations for created resources
res.status(201)
   .set('Location', `/api/posts/${post.id}`)
   .json({ status: 'success', data: post });

// Cache control
res.set('Cache-Control', 'public, max-age=300');  // 5 minutes

// Indicate allowed methods
res.set('Allow', 'GET, POST');
```

---

## 💪 Hands-On Exercise

### Design a RESTful API for a Library System

Without writing code, design the API:

1. Identify resources: Books, Authors, Members, Loans
2. Design all endpoint URLs with HTTP methods
3. Define the response format for each endpoint
4. Identify relationships (Author has many Books, Member borrows Books)
5. List query parameters for filtering and sorting
6. Map status codes for each operation

Write your API design in a **Markdown document** with tables for each resource's endpoints.

---

## 📝 Homework

1. Set up a new Express project for the Blog API using the project structure above
2. Create the central route registry with post, comment, and category routers
3. Implement a `GET /api/posts` endpoint that returns sample data in the consistent response format
4. Add a `404 Not Found` response for `GET /api/posts/:id` with a non-existent ID
5. Add proper `Location` header when creating a resource with `POST`

---

## 💡 Pro Tips

- Design your API **before** writing code — API-first development
- Use **plural nouns** consistently: `/posts`, not `/post`
- Keep URLs **resource-focused** — let HTTP methods express the action
- Always return **consistent response shapes** — clients should know what to expect
- Use `201 Created` for POST, `204 No Content` for DELETE — status codes convey meaning
- Version your API from day one: `/api/v1/posts` (covered later this week)

---

[Back to Week 14](README.md) | [Day 2 →](day-02.md)
