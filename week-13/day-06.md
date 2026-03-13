# 📅 Week 13 — Day 6: Project Day — REST API (To-Do App) 🚀

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goal

Build a **production-quality REST API for a To-Do application** using everything learned this week: Node.js, Express.js, routing, controllers, middleware, validation, and error handling.

---

## 🏗️ Project Overview

| Feature | Description |
|---------|-------------|
| **Application** | To-Do REST API |
| **Tech Stack** | Node.js, Express.js |
| **Architecture** | MVC (Model-View-Controller without View) |
| **Features** | Full CRUD, filtering, sorting, pagination, validation, error handling |
| **Middleware** | Logger, security headers, CORS, validation, error handler |

---

## 📁 Project Structure

```
todo-api/
├── src/
│   ├── controllers/
│   │   └── todoController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   ├── logger.js
│   │   └── validate.js
│   ├── routes/
│   │   └── todoRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   └── asyncHandler.js
│   └── app.js
├── .env
├── .gitignore
├── index.js
├── package.json
└── README.md
```

---

## 🔧 Step 1: Project Setup

```bash
mkdir todo-api && cd todo-api
npm init -y
npm install express cors morgan helmet dotenv
npm install -D nodemon
```

**package.json** — add module type and scripts:

```json
{
  "name": "todo-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
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
```

---

## 🔧 Step 2: Utility Classes

### AppError.js — Custom Error Class

```js
// src/utils/AppError.js
export class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

### asyncHandler.js — Async Wrapper

```js
// src/utils/asyncHandler.js
export function asyncHandler(fn) {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}
```

---

## 🔧 Step 3: Middleware

### logger.js

```js
// src/middleware/logger.js
export function requestLogger(req, res, next) {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    const timestamp = new Date().toISOString();
    console.log(
      `[${timestamp}] ${req.method} ${req.originalUrl} ${res.statusCode} — ${duration}ms`
    );
  });

  next();
}
```

### validate.js

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
        if (rules.type && typeof value !== rules.type) {
          errors.push(`${field} must be a ${rules.type}`);
        }
        if (rules.minLength && typeof value === 'string' && value.trim().length < rules.minLength) {
          errors.push(`${field} must be at least ${rules.minLength} characters`);
        }
        if (rules.maxLength && typeof value === 'string' && value.length > rules.maxLength) {
          errors.push(`${field} must be at most ${rules.maxLength} characters`);
        }
        if (rules.enum && !rules.enum.includes(value)) {
          errors.push(`${field} must be one of: ${rules.enum.join(', ')}`);
        }
      }
    }

    if (errors.length > 0) {
      return res.status(400).json({
        status: 'error',
        errors,
      });
    }

    next();
  };
}

// Schemas
export const createTodoSchema = {
  title: { required: true, type: 'string', minLength: 2, maxLength: 200 },
  description: { type: 'string', maxLength: 1000 },
  priority: { type: 'string', enum: ['low', 'medium', 'high'] },
  category: { type: 'string', maxLength: 50 },
};

export const updateTodoSchema = {
  title: { type: 'string', minLength: 2, maxLength: 200 },
  description: { type: 'string', maxLength: 1000 },
  priority: { type: 'string', enum: ['low', 'medium', 'high'] },
  category: { type: 'string', maxLength: 50 },
  completed: { type: 'boolean' },
};
```

### errorHandler.js

```js
// src/middleware/errorHandler.js
export function notFound(req, res, next) {
  res.status(404).json({
    status: 'error',
    message: `Route ${req.method} ${req.originalUrl} not found`,
  });
}

export function errorHandler(err, req, res, next) {
  const statusCode = err.statusCode || 500;
  const message = statusCode === 500 ? 'Internal Server Error' : err.message;

  console.error(`[ERROR] ${err.message}`);
  if (process.env.NODE_ENV === 'development') {
    console.error(err.stack);
  }

  res.status(statusCode).json({
    status: 'error',
    message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
}
```

---

## 🔧 Step 4: Controller — Todo Logic

```js
// src/controllers/todoController.js
import { AppError } from '../utils/AppError.js';

// In-memory data store
let todos = [];
let nextId = 1;

// GET /api/todos
export function getAllTodos(req, res) {
  let result = [...todos];

  // Filter by completed status
  if (req.query.completed !== undefined) {
    const completed = req.query.completed === 'true';
    result = result.filter(t => t.completed === completed);
  }

  // Filter by priority
  if (req.query.priority) {
    result = result.filter(t => t.priority === req.query.priority);
  }

  // Filter by category
  if (req.query.category) {
    result = result.filter(t => t.category === req.query.category);
  }

  // Search by title
  if (req.query.search) {
    const search = req.query.search.toLowerCase();
    result = result.filter(t => t.title.toLowerCase().includes(search));
  }

  // Sort
  const sortBy = req.query.sortBy || 'createdAt';
  const order = req.query.order === 'asc' ? 1 : -1;
  result.sort((a, b) => {
    if (a[sortBy] < b[sortBy]) return -1 * order;
    if (a[sortBy] > b[sortBy]) return 1 * order;
    return 0;
  });

  // Pagination
  const page = Math.max(1, parseInt(req.query.page) || 1);
  const limit = Math.min(100, Math.max(1, parseInt(req.query.limit) || 10));
  const startIndex = (page - 1) * limit;
  const paginatedResult = result.slice(startIndex, startIndex + limit);

  res.json({
    status: 'success',
    count: paginatedResult.length,
    total: result.length,
    page,
    totalPages: Math.ceil(result.length / limit),
    data: paginatedResult,
  });
}

// GET /api/todos/:id
export function getTodoById(req, res) {
  const todo = findTodoOrThrow(req.params.id);
  res.json({ status: 'success', data: todo });
}

// POST /api/todos
export function createTodo(req, res) {
  const { title, description, priority, category } = req.body;

  const todo = {
    id: nextId++,
    title: title.trim(),
    description: description?.trim() || '',
    completed: false,
    priority: priority || 'medium',
    category: category?.trim() || 'general',
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };

  todos.push(todo);

  res.status(201).json({ status: 'success', data: todo });
}

// PUT /api/todos/:id
export function updateTodo(req, res) {
  const todo = findTodoOrThrow(req.params.id);
  const { title, description, priority, category, completed } = req.body;

  if (title !== undefined) todo.title = title.trim();
  if (description !== undefined) todo.description = description.trim();
  if (priority !== undefined) todo.priority = priority;
  if (category !== undefined) todo.category = category.trim();
  if (completed !== undefined) todo.completed = completed;
  todo.updatedAt = new Date().toISOString();

  res.json({ status: 'success', data: todo });
}

// DELETE /api/todos/:id
export function deleteTodo(req, res) {
  const todo = findTodoOrThrow(req.params.id);
  todos = todos.filter(t => t.id !== todo.id);

  res.json({
    status: 'success',
    message: `Todo "${todo.title}" deleted`,
  });
}

// PATCH /api/todos/:id/toggle
export function toggleTodo(req, res) {
  const todo = findTodoOrThrow(req.params.id);
  todo.completed = !todo.completed;
  todo.updatedAt = new Date().toISOString();

  res.json({ status: 'success', data: todo });
}

// GET /api/todos/stats
export function getTodoStats(req, res) {
  const total = todos.length;
  const completed = todos.filter(t => t.completed).length;
  const pending = total - completed;

  const byPriority = {
    high: todos.filter(t => t.priority === 'high').length,
    medium: todos.filter(t => t.priority === 'medium').length,
    low: todos.filter(t => t.priority === 'low').length,
  };

  const categories = [...new Set(todos.map(t => t.category))];

  res.json({
    status: 'success',
    data: { total, completed, pending, byPriority, categories },
  });
}

// Helper: find todo or throw 404
function findTodoOrThrow(id) {
  const numId = Number(id);
  if (isNaN(numId)) {
    throw new AppError('Invalid ID format — must be a number', 400);
  }

  const todo = todos.find(t => t.id === numId);
  if (!todo) {
    throw new AppError(`Todo with ID ${numId} not found`, 404);
  }

  return todo;
}
```

---

## 🔧 Step 5: Routes

```js
// src/routes/todoRoutes.js
import { Router } from 'express';
import { validateBody, createTodoSchema, updateTodoSchema } from '../middleware/validate.js';
import { asyncHandler } from '../utils/asyncHandler.js';
import {
  getAllTodos,
  getTodoById,
  createTodo,
  updateTodo,
  deleteTodo,
  toggleTodo,
  getTodoStats,
} from '../controllers/todoController.js';

const router = Router();

// Stats route (must be before /:id)
router.get('/stats', asyncHandler(getTodoStats));

// CRUD routes
router
  .route('/')
  .get(asyncHandler(getAllTodos))
  .post(validateBody(createTodoSchema), asyncHandler(createTodo));

router
  .route('/:id')
  .get(asyncHandler(getTodoById))
  .put(validateBody(updateTodoSchema), asyncHandler(updateTodo))
  .delete(asyncHandler(deleteTodo));

// Toggle completion
router.patch('/:id/toggle', asyncHandler(toggleTodo));

export default router;
```

---

## 🔧 Step 6: App & Server

```js
// src/app.js
import express from 'express';
import cors from 'cors';
import morgan from 'morgan';
import helmet from 'helmet';

import { requestLogger } from './middleware/logger.js';
import { notFound, errorHandler } from './middleware/errorHandler.js';
import todoRoutes from './routes/todoRoutes.js';

const app = express();

// Security & utility middleware
app.use(helmet());
app.use(cors());
app.use(morgan('dev'));
app.use(requestLogger);

// Body parsing
app.use(express.json({ limit: '10kb' }));

// Health check
app.get('/health', (req, res) => {
  res.json({
    status: 'ok',
    uptime: `${Math.floor(process.uptime())}s`,
    timestamp: new Date().toISOString(),
  });
});

// API routes
app.use('/api/todos', todoRoutes);

// Error handling (must be last)
app.use(notFound);
app.use(errorHandler);

export default app;
```

```js
// index.js
import 'dotenv/config';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`🚀 Todo API running on http://localhost:${PORT}`);
  console.log(`   Environment: ${process.env.NODE_ENV || 'development'}`);
});
```

---

## 🧪 Step 7: Test Your API

Start the server:

```bash
npm run dev
```

Test with **curl** or **Postman**:

```bash
# Health check
curl http://localhost:3000/health

# Create todos
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn Express.js", "priority": "high", "category": "study"}'

curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Build a REST API", "priority": "high", "category": "project"}'

curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Buy groceries", "priority": "low", "category": "personal"}'

# Get all todos
curl http://localhost:3000/api/todos

# Get with filters
curl "http://localhost:3000/api/todos?priority=high"
curl "http://localhost:3000/api/todos?search=express"
curl "http://localhost:3000/api/todos?sortBy=priority&order=asc"
curl "http://localhost:3000/api/todos?page=1&limit=2"

# Get one todo
curl http://localhost:3000/api/todos/1

# Update a todo
curl -X PUT http://localhost:3000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Master Express.js", "completed": true}'

# Toggle completion
curl -X PATCH http://localhost:3000/api/todos/2/toggle

# Get stats
curl http://localhost:3000/api/todos/stats

# Delete a todo
curl -X DELETE http://localhost:3000/api/todos/3

# Test 404
curl http://localhost:3000/api/todos/999

# Test validation
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "x"}'
```

---

## ✅ API Endpoints Summary

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/health` | Health check |
| `GET` | `/api/todos` | List all todos (with filters, sort, pagination) |
| `GET` | `/api/todos/stats` | Get todo statistics |
| `GET` | `/api/todos/:id` | Get single todo |
| `POST` | `/api/todos` | Create a new todo |
| `PUT` | `/api/todos/:id` | Update a todo |
| `PATCH` | `/api/todos/:id/toggle` | Toggle todo completion |
| `DELETE` | `/api/todos/:id` | Delete a todo |

**Query Parameters:**
| Parameter | Example | Description |
|-----------|---------|-------------|
| `completed` | `?completed=true` | Filter by status |
| `priority` | `?priority=high` | Filter by priority |
| `category` | `?category=study` | Filter by category |
| `search` | `?search=express` | Search titles |
| `sortBy` | `?sortBy=priority` | Sort field |
| `order` | `?order=asc` | Sort direction |
| `page` | `?page=2` | Page number |
| `limit` | `?limit=5` | Items per page |

---

## 💡 What You Built Today

- Professional REST API with **full CRUD** operations
- **Middleware pipeline**: helmet, CORS, morgan, custom logger, validation, error handler
- **MVC architecture** — clean separation of routes, controllers, and middleware
- **Input validation** with descriptive error messages
- **Advanced filtering**, search, sorting, and pagination
- **Custom error class** (`AppError`) with centralized error handling
- **Async error wrapper** eliminating try/catch boilerplate

---

## 🎯 Week 13 Complete!

You now understand:
- **Node.js** runtime, modules, npm ecosystem
- **Express.js** servers, routing, middleware pipeline
- **REST API design** with CRUD operations
- **Error handling** patterns for production apps
- **Project architecture** with MVC pattern

Next week: **RESTful API Design & Development** — building a Blog API with advanced patterns! 🚀

---

[← Day 5](day-05.md) | [Back to Week 13](README.md) | [Next Week →](../week-14/README.md)
