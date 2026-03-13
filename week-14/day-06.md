# 📅 Week 14 — Day 6: Project Day — Blog API with CRUD 📝

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goal

Build a **complete Blog API** with posts, comments, categories, file uploads, API documentation, filtering, pagination, and production-ready middleware — the culmination of everything learned in Weeks 13-14.

---

## 🏗️ Project Overview

| Feature | Description |
|---------|-------------|
| **Application** | Blog REST API |
| **Resources** | Posts, Comments, Categories |
| **Architecture** | MVC with modular route/controller separation |
| **Features** | Full CRUD, nested resources, file uploads, search, pagination, Swagger docs |
| **Middleware** | Security, CORS, logging, validation, rate limiting, compression, error handling |

---

## 📁 Project Structure

```
blog-api/
├── src/
│   ├── config/
│   │   └── swagger.js
│   ├── controllers/
│   │   ├── postController.js
│   │   ├── commentController.js
│   │   └── categoryController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   ├── logger.js
│   │   ├── rateLimiter.js
│   │   ├── upload.js
│   │   └── validate.js
│   ├── models/
│   │   ├── Post.js
│   │   ├── Comment.js
│   │   └── Category.js
│   ├── routes/
│   │   ├── index.js
│   │   ├── postRoutes.js
│   │   ├── commentRoutes.js
│   │   └── categoryRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   └── helpers.js
│   └── app.js
├── uploads/
│   └── .gitkeep
├── .env
├── .gitignore
├── index.js
└── package.json
```

---

## 🔧 Step 1: Project Setup

```bash
mkdir blog-api && cd blog-api
npm init -y
npm install express cors morgan helmet dotenv compression express-rate-limit multer swagger-jsdoc swagger-ui-express
npm install -D nodemon supertest
mkdir -p src/{config,controllers,middleware,models,routes,utils} uploads
```

```json
{
  "name": "blog-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "node --test src/tests/*.test.js"
  }
}
```

**.env**
```env
PORT=3000
NODE_ENV=development
```

**.gitignore**
```
node_modules/
.env
uploads/*
!uploads/.gitkeep
```

---

## 🔧 Step 2: Utilities

```js
// src/utils/AppError.js
export class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

```js
// src/utils/asyncHandler.js
export function asyncHandler(fn) {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}
```

```js
// src/utils/helpers.js
export function generateSlug(title) {
  return title
    .toLowerCase()
    .trim()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/(^-|-$)/g, '');
}

export function parseId(val) {
  const id = Number(val);
  if (isNaN(id)) {
    throw new (await import('./AppError.js')).AppError('Invalid ID format', 400);
  }
  return id;
}
```

---

## 🔧 Step 3: Models (In-Memory Data Store)

```js
// src/models/Post.js
import { generateSlug } from '../utils/helpers.js';

let posts = [];
let nextId = 1;

export function createPost(data) {
  const post = {
    id: nextId++,
    title: data.title.trim(),
    slug: generateSlug(data.title),
    content: data.content.trim(),
    excerpt: data.content.substring(0, 150).trim() + '...',
    author: data.author.trim(),
    categoryId: data.categoryId ? Number(data.categoryId) : null,
    tags: data.tags || [],
    status: data.status || 'draft',
    coverImage: null,
    views: 0,
    isDeleted: false,
    deletedAt: null,
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };
  posts.push(post);
  return post;
}

export function findAllPosts() { return posts.filter(p => !p.isDeleted); }
export function findPostById(id) { return posts.find(p => p.id === id && !p.isDeleted); }
export function findDeletedPosts() { return posts.filter(p => p.isDeleted); }
export function removePost(id) { posts = posts.filter(p => p.id !== id); }
export { posts };
```

```js
// src/models/Comment.js
let comments = [];
let nextId = 1;

export function createComment(postId, data) {
  const comment = {
    id: nextId++,
    postId,
    author: data.author.trim(),
    content: data.content.trim(),
    createdAt: new Date().toISOString(),
  };
  comments.push(comment);
  return comment;
}

export function findCommentsByPost(postId) { return comments.filter(c => c.postId === postId); }
export function findCommentById(id) { return comments.find(c => c.id === id); }
export function removeComment(id) { comments = comments.filter(c => c.id !== id); }
export function removeCommentsByPost(postId) { comments = comments.filter(c => c.postId !== postId); }
export { comments };
```

```js
// src/models/Category.js
let categories = [];
let nextId = 1;

export function createCategory(data) {
  const category = {
    id: nextId++,
    name: data.name.trim(),
    slug: data.name.toLowerCase().replace(/\s+/g, '-'),
    description: data.description?.trim() || '',
    createdAt: new Date().toISOString(),
  };
  categories.push(category);
  return category;
}

export function findAllCategories() { return categories; }
export function findCategoryById(id) { return categories.find(c => c.id === id); }
export function removeCategory(id) { categories = categories.filter(c => c.id !== id); }
export { categories };
```

---

## 🔧 Step 4: Middleware

```js
// src/middleware/logger.js
export function requestLogger(req, res, next) {
  const start = Date.now();
  res.on('finish', () => {
    const ms = Date.now() - start;
    console.log(`[${new Date().toISOString()}] ${req.method} ${req.originalUrl} ${res.statusCode} ${ms}ms`);
  });
  next();
}
```

```js
// src/middleware/validate.js
export function validateBody(schema) {
  return (req, res, next) => {
    const errors = [];
    for (const [field, rules] of Object.entries(schema)) {
      const value = req.body[field];
      if (rules.required && (value === undefined || value === null || value === '')) {
        errors.push(`${field} is required`);
        continue;
      }
      if (value !== undefined && value !== null) {
        if (rules.type && typeof value !== rules.type) errors.push(`${field} must be a ${rules.type}`);
        if (rules.minLength && typeof value === 'string' && value.trim().length < rules.minLength) errors.push(`${field} must be at least ${rules.minLength} characters`);
        if (rules.maxLength && typeof value === 'string' && value.length > rules.maxLength) errors.push(`${field} must be at most ${rules.maxLength} characters`);
        if (rules.enum && !rules.enum.includes(value)) errors.push(`${field} must be one of: ${rules.enum.join(', ')}`);
      }
    }
    if (errors.length > 0) return res.status(400).json({ status: 'error', errors });
    next();
  };
}
```

```js
// src/middleware/errorHandler.js
export function notFound(req, res, next) {
  res.status(404).json({ status: 'error', message: `Route ${req.method} ${req.originalUrl} not found` });
}

export function errorHandler(err, req, res, next) {
  const statusCode = err.statusCode || 500;
  console.error(`[ERROR] ${err.message}`);
  if (process.env.NODE_ENV === 'development') console.error(err.stack);
  res.status(statusCode).json({
    status: 'error',
    message: statusCode === 500 ? 'Internal Server Error' : err.message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
}
```

```js
// src/middleware/rateLimiter.js
import rateLimit from 'express-rate-limit';

export const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
  message: { status: 'error', message: 'Too many requests — try again later' },
});
```

```js
// src/middleware/upload.js
import multer from 'multer';
import path from 'path';

const storage = multer.diskStorage({
  destination: (req, file, cb) => cb(null, 'uploads/'),
  filename: (req, file, cb) => {
    const unique = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
    cb(null, `${file.fieldname}-${unique}${path.extname(file.originalname)}`);
  },
});

function fileFilter(req, file, cb) {
  const allowed = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];
  if (allowed.includes(file.mimetype)) cb(null, true);
  else cb(new Error('Only JPEG, PNG, GIF, and WebP images are allowed'), false);
}

export const upload = multer({ storage, fileFilter, limits: { fileSize: 5 * 1024 * 1024 } });
```

---

## 🔧 Step 5: Controllers

```js
// src/controllers/postController.js
import { AppError } from '../utils/AppError.js';
import { generateSlug } from '../utils/helpers.js';
import * as Post from '../models/Post.js';
import { findCommentsByPost, removeCommentsByPost } from '../models/Comment.js';
import { findCategoryById } from '../models/Category.js';

export function getAllPosts(req, res) {
  let result = Post.findAllPosts();

  // Filters
  if (req.query.status) result = result.filter(p => p.status === req.query.status);
  if (req.query.categoryId) result = result.filter(p => p.categoryId === Number(req.query.categoryId));
  if (req.query.tag) result = result.filter(p => p.tags.includes(req.query.tag));
  if (req.query.search) {
    const s = req.query.search.toLowerCase();
    result = result.filter(p => p.title.toLowerCase().includes(s) || p.content.toLowerCase().includes(s));
  }

  // Sort
  const sortBy = req.query.sortBy || 'createdAt';
  const order = req.query.order === 'asc' ? 1 : -1;
  result.sort((a, b) => (a[sortBy] < b[sortBy] ? -1 * order : a[sortBy] > b[sortBy] ? order : 0));

  // Pagination
  const page = Math.max(1, parseInt(req.query.page) || 1);
  const limit = Math.min(100, Math.max(1, parseInt(req.query.limit) || 10));
  const start = (page - 1) * limit;
  const paginated = result.slice(start, start + limit);

  // Add comment count to each post
  const data = paginated.map(p => ({
    ...p,
    commentCount: findCommentsByPost(p.id).length,
  }));

  res.json({ status: 'success', count: data.length, total: result.length, page, totalPages: Math.ceil(result.length / limit), data });
}

export function getOnePost(req, res) {
  const post = findOrThrow(req.params.id);
  post.views++;

  const comments = findCommentsByPost(post.id);
  const category = post.categoryId ? findCategoryById(post.categoryId) : null;

  res.json({ status: 'success', data: { ...post, category, comments, commentCount: comments.length } });
}

export function createPost(req, res) {
  const post = Post.createPost(req.body);
  res.status(201).set('Location', `/api/posts/${post.id}`).json({ status: 'success', data: post });
}

export function updatePost(req, res) {
  const post = findOrThrow(req.params.id);
  const allowed = ['title', 'content', 'author', 'categoryId', 'tags', 'status'];
  for (const field of allowed) {
    if (req.body[field] !== undefined) post[field] = req.body[field];
  }
  if (req.body.title) { post.slug = generateSlug(req.body.title); post.excerpt = post.content.substring(0, 150) + '...'; }
  if (req.body.content) post.excerpt = req.body.content.substring(0, 150) + '...';
  post.updatedAt = new Date().toISOString();
  res.json({ status: 'success', data: post });
}

export function deletePost(req, res) {
  const post = findOrThrow(req.params.id);
  post.isDeleted = true;
  post.deletedAt = new Date().toISOString();
  res.json({ status: 'success', message: `Post "${post.title}" moved to trash` });
}

export function restorePost(req, res) {
  const id = Number(req.params.id);
  if (isNaN(id)) throw new AppError('Invalid ID', 400);
  const post = Post.posts.find(p => p.id === id && p.isDeleted);
  if (!post) throw new AppError('Deleted post not found', 404);
  post.isDeleted = false;
  post.deletedAt = null;
  res.json({ status: 'success', data: post });
}

export function permanentDelete(req, res) {
  const post = findOrThrow(req.params.id);
  removeCommentsByPost(post.id);
  Post.removePost(post.id);
  res.status(204).end();
}

export function uploadCover(req, res) {
  const post = findOrThrow(req.params.id);
  if (!req.file) throw new AppError('No image file provided', 400);
  post.coverImage = `/uploads/${req.file.filename}`;
  post.updatedAt = new Date().toISOString();
  res.json({ status: 'success', data: { coverImage: post.coverImage } });
}

export function getPostStats(req, res) {
  const all = Post.findAllPosts();
  res.json({
    status: 'success',
    data: {
      total: all.length,
      published: all.filter(p => p.status === 'published').length,
      draft: all.filter(p => p.status === 'draft').length,
      totalViews: all.reduce((sum, p) => sum + p.views, 0),
    },
  });
}

function findOrThrow(id) {
  const numId = Number(id);
  if (isNaN(numId)) throw new AppError('Invalid ID format', 400);
  const post = Post.findPostById(numId);
  if (!post) throw new AppError('Post not found', 404);
  return post;
}
```

```js
// src/controllers/commentController.js
import { AppError } from '../utils/AppError.js';
import { findPostById } from '../models/Post.js';
import * as Comment from '../models/Comment.js';

export function getComments(req, res) {
  const postId = parseId(req.params.id);
  if (!findPostById(postId)) throw new AppError('Post not found', 404);
  const comments = Comment.findCommentsByPost(postId);
  res.json({ status: 'success', count: comments.length, data: comments });
}

export function addComment(req, res) {
  const postId = parseId(req.params.id);
  if (!findPostById(postId)) throw new AppError('Post not found', 404);
  const comment = Comment.createComment(postId, req.body);
  res.status(201).json({ status: 'success', data: comment });
}

export function deleteComment(req, res) {
  const commentId = parseId(req.params.commentId);
  const comment = Comment.findCommentById(commentId);
  if (!comment) throw new AppError('Comment not found', 404);
  Comment.removeComment(commentId);
  res.json({ status: 'success', message: 'Comment deleted' });
}

function parseId(val) {
  const id = Number(val);
  if (isNaN(id)) throw new AppError('Invalid ID format', 400);
  return id;
}
```

```js
// src/controllers/categoryController.js
import { AppError } from '../utils/AppError.js';
import * as Category from '../models/Category.js';

export function getAllCategories(req, res) {
  res.json({ status: 'success', data: Category.findAllCategories() });
}

export function createCategory(req, res) {
  const category = Category.createCategory(req.body);
  res.status(201).json({ status: 'success', data: category });
}

export function updateCategory(req, res) {
  const id = Number(req.params.id);
  const category = Category.findCategoryById(id);
  if (!category) throw new AppError('Category not found', 404);
  if (req.body.name) { category.name = req.body.name.trim(); category.slug = req.body.name.toLowerCase().replace(/\s+/g, '-'); }
  if (req.body.description !== undefined) category.description = req.body.description.trim();
  res.json({ status: 'success', data: category });
}

export function deleteCategory(req, res) {
  const id = Number(req.params.id);
  if (!Category.findCategoryById(id)) throw new AppError('Category not found', 404);
  Category.removeCategory(id);
  res.json({ status: 'success', message: 'Category deleted' });
}
```

---

## 🔧 Step 6: Routes

```js
// src/routes/postRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { validateBody } from '../middleware/validate.js';
import { upload } from '../middleware/upload.js';
import * as ctrl from '../controllers/postController.js';

const router = Router();

const postSchema = {
  title: { required: true, type: 'string', minLength: 3, maxLength: 200 },
  content: { required: true, type: 'string', minLength: 10 },
  author: { required: true, type: 'string', minLength: 2, maxLength: 100 },
  status: { type: 'string', enum: ['draft', 'published'] },
};

router.get('/stats', asyncHandler(ctrl.getPostStats));
router.route('/').get(asyncHandler(ctrl.getAllPosts)).post(validateBody(postSchema), asyncHandler(ctrl.createPost));
router.route('/:id').get(asyncHandler(ctrl.getOnePost)).patch(asyncHandler(ctrl.updatePost)).delete(asyncHandler(ctrl.deletePost));
router.patch('/:id/restore', asyncHandler(ctrl.restorePost));
router.delete('/:id/permanent', asyncHandler(ctrl.permanentDelete));
router.post('/:id/cover', upload.single('cover'), asyncHandler(ctrl.uploadCover));

export default router;
```

```js
// src/routes/commentRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { validateBody } from '../middleware/validate.js';
import * as ctrl from '../controllers/commentController.js';

const router = Router();
const commentSchema = {
  author: { required: true, type: 'string', minLength: 2, maxLength: 100 },
  content: { required: true, type: 'string', minLength: 1, maxLength: 2000 },
};

router.route('/:id/comments').get(asyncHandler(ctrl.getComments)).post(validateBody(commentSchema), asyncHandler(ctrl.addComment));
router.delete('/:id/comments/:commentId', asyncHandler(ctrl.deleteComment));

export default router;
```

```js
// src/routes/categoryRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { validateBody } from '../middleware/validate.js';
import * as ctrl from '../controllers/categoryController.js';

const router = Router();
const categorySchema = { name: { required: true, type: 'string', minLength: 2, maxLength: 50 } };

router.route('/').get(asyncHandler(ctrl.getAllCategories)).post(validateBody(categorySchema), asyncHandler(ctrl.createCategory));
router.route('/:id').patch(asyncHandler(ctrl.updateCategory)).delete(asyncHandler(ctrl.deleteCategory));

export default router;
```

```js
// src/routes/index.js
import { Router } from 'express';
import postRoutes from './postRoutes.js';
import commentRoutes from './commentRoutes.js';
import categoryRoutes from './categoryRoutes.js';

const router = Router();
router.use('/posts', postRoutes);
router.use('/posts', commentRoutes);
router.use('/categories', categoryRoutes);

export default router;
```

---

## 🔧 Step 7: App & Server

```js
// src/app.js
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import compression from 'compression';

import { requestLogger } from './middleware/logger.js';
import { apiLimiter } from './middleware/rateLimiter.js';
import { notFound, errorHandler } from './middleware/errorHandler.js';
import apiRoutes from './routes/index.js';

const app = express();

app.use(helmet());
app.use(cors());
app.use(compression());
app.use(morgan(process.env.NODE_ENV === 'production' ? 'combined' : 'dev'));
app.use(requestLogger);
app.use(express.json({ limit: '50kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));
app.use('/uploads', express.static('uploads'));
app.use('/api', apiLimiter);
app.use('/api', apiRoutes);

app.get('/health', (req, res) => {
  res.json({ status: 'ok', uptime: `${Math.floor(process.uptime())}s`, timestamp: new Date().toISOString() });
});

app.use(notFound);
app.use(errorHandler);

export default app;
```

```js
// index.js
import 'dotenv/config';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;
const server = app.listen(PORT, () => {
  console.log(`📝 Blog API running on http://localhost:${PORT}`);
  console.log(`📚 API Docs: http://localhost:${PORT}/api-docs`);
});

process.on('SIGTERM', () => { server.close(() => process.exit(0)); });
process.on('SIGINT', () => { server.close(() => process.exit(0)); });
```

---

## 🧪 Test Your API

```bash
npm run dev
```

```bash
# Create a category
curl -X POST http://localhost:3000/api/categories \
  -H "Content-Type: application/json" \
  -d '{"name": "Technology", "description": "Tech articles"}'

# Create posts
curl -X POST http://localhost:3000/api/posts \
  -H "Content-Type: application/json" \
  -d '{"title": "Getting Started with Node.js", "content": "Node.js is a JavaScript runtime built on Chrome V8 engine...", "author": "Ali Khan", "categoryId": 1, "tags": ["node", "javascript"], "status": "published"}'

curl -X POST http://localhost:3000/api/posts \
  -H "Content-Type: application/json" \
  -d '{"title": "Express.js Best Practices", "content": "Learn how to build production-ready Express applications...", "author": "Sara Ahmed", "categoryId": 1, "tags": ["express", "api"], "status": "published"}'

# List posts with filters
curl "http://localhost:3000/api/posts?status=published&sortBy=createdAt&order=desc"
curl "http://localhost:3000/api/posts?search=node&tag=javascript"
curl "http://localhost:3000/api/posts?page=1&limit=5"

# Get single post (with comments and category)
curl http://localhost:3000/api/posts/1

# Add comments
curl -X POST http://localhost:3000/api/posts/1/comments \
  -H "Content-Type: application/json" \
  -d '{"author": "Reader", "content": "Great article! Very helpful."}'

# Get comments
curl http://localhost:3000/api/posts/1/comments

# Upload cover image
curl -X POST http://localhost:3000/api/posts/1/cover -F "cover=@image.jpg"

# Soft delete
curl -X DELETE http://localhost:3000/api/posts/2

# Restore
curl -X PATCH http://localhost:3000/api/posts/2/restore

# Stats
curl http://localhost:3000/api/posts/stats
```

---

## ✅ Complete API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Health check |
| `GET` | `/api/posts` | List posts (filter, sort, paginate) |
| `GET` | `/api/posts/stats` | Post statistics |
| `GET` | `/api/posts/:id` | Single post with comments & category |
| `POST` | `/api/posts` | Create post |
| `PATCH` | `/api/posts/:id` | Update post |
| `DELETE` | `/api/posts/:id` | Soft delete |
| `PATCH` | `/api/posts/:id/restore` | Restore from trash |
| `DELETE` | `/api/posts/:id/permanent` | Permanent delete |
| `POST` | `/api/posts/:id/cover` | Upload cover image |
| `GET` | `/api/posts/:id/comments` | List comments |
| `POST` | `/api/posts/:id/comments` | Add comment |
| `DELETE` | `/api/posts/:id/comments/:commentId` | Delete comment |
| `GET` | `/api/categories` | List categories |
| `POST` | `/api/categories` | Create category |
| `PATCH` | `/api/categories/:id` | Update category |
| `DELETE` | `/api/categories/:id` | Delete category |

---

## 💡 What You Built

- **3 resources** with full CRUD operations (Posts, Comments, Categories)
- **Data relationships** — posts belong to categories, comments belong to posts
- **File uploads** with Multer and file type validation
- **Production middleware** — security, CORS, compression, rate limiting, logging
- **Input validation** with descriptive error messages
- **Soft delete** with restore capability
- **Search, filter, sort, and pagination** on list endpoints
- **MVC architecture** with clean separation of concerns

---

## 🎯 Week 14 Complete!

You've mastered professional REST API development. Next week: **MongoDB & Mongoose** — replacing in-memory storage with a real database! 🚀

---

[← Day 5](day-05.md) | [Back to Week 14](README.md) | [Next Week →](../week-14/README.md)
