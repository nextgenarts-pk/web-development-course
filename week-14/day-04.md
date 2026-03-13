# 📅 Week 14 — Day 4: API Documentation & Testing 📝

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Document APIs with Swagger/OpenAPI
- Generate interactive API documentation automatically
- Write API tests with Node.js test runner and Supertest
- Test all CRUD operations and edge cases
- Understand test-driven development (TDD) basics

---

## 📖 Lesson Content

### 1. Why API Documentation?

Without documentation, no one knows how to use your API — not even future you.

| Bad | Good |
|-----|------|
| "Just read the code" | Interactive Swagger docs |
| Slack messages explaining endpoints | Auto-generated from code |
| Outdated README | Always in sync with the API |

### 2. Setting Up Swagger

```bash
npm install swagger-ui-express swagger-jsdoc
```

```js
// config/swagger.js
import swaggerJSDoc from 'swagger-jsdoc';

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'Blog API',
      version: '1.0.0',
      description: 'A RESTful Blog API built with Express.js',
      contact: {
        name: 'NextGen Arts',
        url: 'https://nextgenarts.pk',
      },
    },
    servers: [
      {
        url: 'http://localhost:3000',
        description: 'Development server',
      },
    ],
  },
  apis: ['./src/routes/*.js'],  // Files containing annotations
};

export const swaggerSpec = swaggerJSDoc(options);
```

```js
// app.js
import swaggerUi from 'swagger-ui-express';
import { swaggerSpec } from './config/swagger.js';

// Serve Swagger docs at /api-docs
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));

// Serve raw JSON spec
app.get('/api-docs.json', (req, res) => {
  res.json(swaggerSpec);
});
```

Now visit: `http://localhost:3000/api-docs`

### 3. Documenting Endpoints with JSDoc

Add Swagger annotations as comments above routes:

```js
// routes/postRoutes.js

/**
 * @swagger
 * components:
 *   schemas:
 *     Post:
 *       type: object
 *       required:
 *         - title
 *         - content
 *         - author
 *       properties:
 *         id:
 *           type: integer
 *           description: Auto-generated ID
 *         title:
 *           type: string
 *           minLength: 3
 *           maxLength: 200
 *         slug:
 *           type: string
 *         content:
 *           type: string
 *           minLength: 10
 *         author:
 *           type: string
 *         status:
 *           type: string
 *           enum: [draft, published]
 *           default: draft
 *         tags:
 *           type: array
 *           items:
 *             type: string
 *         createdAt:
 *           type: string
 *           format: date-time
 *       example:
 *         id: 1
 *         title: "Getting Started with Express"
 *         slug: "getting-started-with-express"
 *         content: "Express is a fast, minimalist web framework..."
 *         author: "Ali Khan"
 *         status: "published"
 *         tags: ["express", "node", "tutorial"]
 */

/**
 * @swagger
 * /api/posts:
 *   get:
 *     summary: Get all posts
 *     tags: [Posts]
 *     parameters:
 *       - in: query
 *         name: page
 *         schema:
 *           type: integer
 *           default: 1
 *         description: Page number
 *       - in: query
 *         name: limit
 *         schema:
 *           type: integer
 *           default: 10
 *         description: Items per page
 *       - in: query
 *         name: status
 *         schema:
 *           type: string
 *           enum: [draft, published]
 *         description: Filter by status
 *       - in: query
 *         name: search
 *         schema:
 *           type: string
 *         description: Search in title
 *     responses:
 *       200:
 *         description: List of posts
 *         content:
 *           application/json:
 *             schema:
 *               type: object
 *               properties:
 *                 status:
 *                   type: string
 *                 count:
 *                   type: integer
 *                 total:
 *                   type: integer
 *                 data:
 *                   type: array
 *                   items:
 *                     $ref: '#/components/schemas/Post'
 */
router.get('/', asyncHandler(getAllPosts));

/**
 * @swagger
 * /api/posts:
 *   post:
 *     summary: Create a new post
 *     tags: [Posts]
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - title
 *               - content
 *               - author
 *             properties:
 *               title:
 *                 type: string
 *               content:
 *                 type: string
 *               author:
 *                 type: string
 *               tags:
 *                 type: array
 *                 items:
 *                   type: string
 *     responses:
 *       201:
 *         description: Post created
 *       400:
 *         description: Validation error
 */
router.post('/', validateBody(postSchema), asyncHandler(createPost));

/**
 * @swagger
 * /api/posts/{id}:
 *   get:
 *     summary: Get a post by ID
 *     tags: [Posts]
 *     parameters:
 *       - in: path
 *         name: id
 *         required: true
 *         schema:
 *           type: integer
 *     responses:
 *       200:
 *         description: The post
 *         content:
 *           application/json:
 *             schema:
 *               $ref: '#/components/schemas/Post'
 *       404:
 *         description: Post not found
 */
router.get('/:id', asyncHandler(getOnePost));
```

### 4. Introduction to API Testing

Testing ensures your API works correctly and doesn't break when you make changes.

**Test types:**
- **Unit tests** — test individual functions
- **Integration tests** — test complete API endpoints
- **End-to-end tests** — test full user workflows

### 5. Setting Up Tests with Node Test Runner

Node.js has a **built-in test runner** (no extra packages needed for running tests):

```bash
npm install -D supertest
```

```json
// package.json
{
  "scripts": {
    "test": "node --test src/tests/*.test.js",
    "test:watch": "node --test --watch src/tests/*.test.js"
  }
}
```

### 6. Writing Your First API Test

```js
// src/tests/posts.test.js
import { describe, it, before, after } from 'node:test';
import assert from 'node:assert/strict';
import supertest from 'supertest';
import app from '../app.js';

const request = supertest(app);

describe('Posts API', () => {
  let createdPostId;

  // Test: GET /api/posts (empty at start)
  it('should return empty posts array initially', async () => {
    const res = await request.get('/api/posts');

    assert.equal(res.status, 200);
    assert.equal(res.body.status, 'success');
    assert.equal(res.body.count, 0);
    assert.ok(Array.isArray(res.body.data));
  });

  // Test: POST /api/posts
  it('should create a new post', async () => {
    const newPost = {
      title: 'Test Post',
      content: 'This is a test post content that is long enough.',
      author: 'Test Author',
      tags: ['test', 'api'],
    };

    const res = await request
      .post('/api/posts')
      .send(newPost)
      .set('Content-Type', 'application/json');

    assert.equal(res.status, 201);
    assert.equal(res.body.status, 'success');
    assert.equal(res.body.data.title, 'Test Post');
    assert.equal(res.body.data.slug, 'test-post');
    assert.ok(res.body.data.id);

    createdPostId = res.body.data.id;
  });

  // Test: GET /api/posts/:id
  it('should return the created post', async () => {
    const res = await request.get(`/api/posts/${createdPostId}`);

    assert.equal(res.status, 200);
    assert.equal(res.body.data.title, 'Test Post');
  });

  // Test: PATCH /api/posts/:id
  it('should update the post', async () => {
    const res = await request
      .patch(`/api/posts/${createdPostId}`)
      .send({ title: 'Updated Post' })
      .set('Content-Type', 'application/json');

    assert.equal(res.status, 200);
    assert.equal(res.body.data.title, 'Updated Post');
    assert.equal(res.body.data.slug, 'updated-post');
  });

  // Test: DELETE /api/posts/:id
  it('should delete the post', async () => {
    const res = await request.delete(`/api/posts/${createdPostId}`);
    assert.equal(res.status, 200);
  });

  // Test: GET non-existent post
  it('should return 404 for non-existent post', async () => {
    const res = await request.get('/api/posts/99999');
    assert.equal(res.status, 404);
  });
});
```

### 7. Testing Validation

```js
describe('Post Validation', () => {
  it('should reject post without title', async () => {
    const res = await request
      .post('/api/posts')
      .send({ content: 'No title here', author: 'Test' })
      .set('Content-Type', 'application/json');

    assert.equal(res.status, 400);
    assert.ok(res.body.errors.some(e => e.includes('title')));
  });

  it('should reject post with title too short', async () => {
    const res = await request
      .post('/api/posts')
      .send({ title: 'ab', content: 'Content here', author: 'Test' })
      .set('Content-Type', 'application/json');

    assert.equal(res.status, 400);
  });

  it('should reject invalid ID format', async () => {
    const res = await request.get('/api/posts/abc');
    assert.equal(res.status, 400);
    assert.ok(res.body.message.includes('Invalid ID'));
  });
});
```

### 8. Testing Filters & Pagination

```js
describe('Post Filtering & Pagination', () => {
  before(async () => {
    // Seed test data
    const posts = [
      { title: 'JavaScript Basics', content: 'Learn JS fundamentals...', author: 'Ali', status: 'published' },
      { title: 'React Hooks Guide', content: 'Understanding hooks...', author: 'Sara', status: 'published' },
      { title: 'Draft Post', content: 'Work in progress...', author: 'Ali', status: 'draft' },
    ];

    for (const post of posts) {
      await request.post('/api/posts').send(post).set('Content-Type', 'application/json');
    }
  });

  it('should filter by status', async () => {
    const res = await request.get('/api/posts?status=published');
    assert.equal(res.status, 200);
    assert.ok(res.body.data.every(p => p.status === 'published'));
  });

  it('should search by title', async () => {
    const res = await request.get('/api/posts?search=javascript');
    assert.equal(res.status, 200);
    assert.ok(res.body.data.length > 0);
    assert.ok(res.body.data[0].title.toLowerCase().includes('javascript'));
  });

  it('should paginate results', async () => {
    const res = await request.get('/api/posts?page=1&limit=2');
    assert.equal(res.status, 200);
    assert.ok(res.body.count <= 2);
    assert.ok(res.body.totalPages >= 1);
  });
});
```

### 9. Testing Error Responses

```js
describe('Error Handling', () => {
  it('should return 404 for unknown routes', async () => {
    const res = await request.get('/api/unknown');
    assert.equal(res.status, 404);
    assert.equal(res.body.status, 'error');
  });

  it('should return JSON errors, not HTML', async () => {
    const res = await request.get('/api/posts/99999');
    assert.equal(res.headers['content-type'].includes('application/json'), true);
  });

  it('should include error message', async () => {
    const res = await request.get('/api/posts/99999');
    assert.ok(res.body.message);
  });
});
```

### 10. Running Tests

```bash
# Run all tests
npm test

# Run tests in watch mode (re-run on file change)
npm run test:watch

# Run a specific test file
node --test src/tests/posts.test.js
```

**Expected output:**

```
▶ Posts API
  ✔ should return empty posts array initially (15ms)
  ✔ should create a new post (8ms)
  ✔ should return the created post (5ms)
  ✔ should update the post (4ms)
  ✔ should delete the post (3ms)
  ✔ should return 404 for non-existent post (3ms)
▶ Posts API (38ms)

▶ Post Validation
  ✔ should reject post without title (4ms)
  ✔ should reject post with title too short (3ms)
  ✔ should reject invalid ID format (2ms)
▶ Post Validation (9ms)

tests 9
pass 9
fail 0
```

---

## 💪 Hands-On Exercise

### Document & Test the Blog API

1. Install `swagger-jsdoc` and `swagger-ui-express`
2. Create Swagger config with API info, server URL, and schema definitions
3. Add `@swagger` annotations to at least 4 endpoints (GET all, GET by ID, POST, DELETE)
4. Verify docs render at `http://localhost:3000/api-docs`
5. Write tests for:
   - Creating a post (happy path)
   - Getting a post by ID
   - Validation rejection (missing title)
   - 404 for non-existent post
6. Run tests and ensure all pass

---

## 📝 Homework

1. Add Swagger annotations to ALL Blog API endpoints (including comments and categories)
2. Write tests for the comments endpoints (add comment, get comments, delete comment)
3. Add a `GET /api/health` test that checks uptime and status
4. Write a test that creates a post, adds 3 comments, then verifies the comment count
5. Aim for at least 15 total test cases covering happy paths and error cases

---

## 💡 Pro Tips

- Swagger annotations look verbose, but they generate beautiful interactive docs automatically
- Write tests **as you build** endpoints, not after — you'll catch bugs earlier
- Test both happy paths (200, 201) and error paths (400, 404, 500)
- `supertest` doesn't start the server — it hooks into the Express app directly
- Separate `app.js` (exports app) from `index.js` (starts server) so tests can import the app without starting the server
- Use `before()` to seed test data and `after()` to clean up

---

[← Day 3](day-03.md) | [Back to Week 14](README.md) | [Day 5 →](day-05.md)
