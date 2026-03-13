# 📅 Week 13 — Day 5: Middleware & Error Handling ⚙️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand what middleware is and how the middleware pipeline works
- Use built-in and third-party middleware
- Write custom middleware (logging, auth, validation)
- Implement centralized error handling
- Use environment variables with `dotenv`

---

## 📖 Lesson Content

### 1. What Is Middleware?

Middleware are functions that run **between** receiving a request and sending a response. They have access to `req`, `res`, and `next()`:

```
Request → Middleware 1 → Middleware 2 → Route Handler → Response
```

```js
function myMiddleware(req, res, next) {
  // Do something with req/res
  console.log('Middleware ran!');
  next();  // Pass control to the next middleware/route
}
```

If you don't call `next()`, the request **hangs** — the client gets no response.

### 2. Types of Middleware

| Type | Example | Purpose |
|------|---------|---------|
| **Application-level** | `app.use(fn)` | Runs on every request |
| **Route-level** | `router.use(fn)` | Runs on specific router |
| **Built-in** | `express.json()` | Parses JSON bodies |
| **Third-party** | `cors()`, `morgan()` | Add features |
| **Error-handling** | `(err, req, res, next)` | Catch errors |

### 3. Built-in Middleware

```js
import express from 'express';

const app = express();

// Parse JSON request bodies
app.use(express.json());

// Parse URL-encoded form data
app.use(express.urlencoded({ extended: true }));

// Serve static files from the "public" folder
app.use(express.static('public'));
```

### 4. Third-Party Middleware

```bash
npm install cors morgan helmet
```

```js
import cors from 'cors';
import morgan from 'morgan';
import helmet from 'helmet';

// Security headers
app.use(helmet());

// CORS — allow cross-origin requests
app.use(cors());

// HTTP request logging
app.use(morgan('dev'));
// Output: GET /api/todos 200 12ms
```

### 5. Custom Middleware: Request Logger

```js
// middleware/logger.js
export function requestLogger(req, res, next) {
  const start = Date.now();

  // This runs AFTER the response is sent
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(
      `${req.method} ${req.originalUrl} ${res.statusCode} — ${duration}ms`
    );
  });

  next();
}
```

```js
// index.js
import { requestLogger } from './middleware/logger.js';

app.use(requestLogger);
```

### 6. Custom Middleware: Request ID

```js
// middleware/requestId.js
import { randomUUID } from 'crypto';

export function requestId(req, res, next) {
  req.id = randomUUID();
  res.set('X-Request-Id', req.id);
  next();
}
```

### 7. Route-Level Middleware

Apply middleware to specific routes only:

```js
function requireApiKey(req, res, next) {
  const apiKey = req.headers['x-api-key'];

  if (!apiKey || apiKey !== process.env.API_KEY) {
    return res.status(401).json({ error: 'Invalid API key' });
  }

  next();
}

// Only this route requires the API key
router.post('/', requireApiKey, createTodo);

// Or protect all routes in a router
router.use(requireApiKey);
```

### 8. Validation Middleware

Create reusable validation middleware:

```js
// middleware/validate.js
export function validateBody(schema) {
  return (req, res, next) => {
    const errors = [];

    for (const [field, rules] of Object.entries(schema)) {
      const value = req.body[field];

      if (rules.required && (value === undefined || value === null || value === '')) {
        errors.push(`${field} is required`);
        continue;
      }

      if (value !== undefined) {
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
      return res.status(400).json({ errors });
    }

    next();
  };
}
```

```js
// routes/todoRoutes.js
import { validateBody } from '../middleware/validate.js';

const todoSchema = {
  title: { required: true, type: 'string', minLength: 2, maxLength: 200 },
  priority: { type: 'string', enum: ['low', 'medium', 'high'] },
};

router.post('/', validateBody(todoSchema), createTodo);
```

### 9. Error Handling Middleware

Express error handlers have **4 parameters** — `(err, req, res, next)`:

```js
// middleware/errorHandler.js
export function notFound(req, res, next) {
  res.status(404).json({
    error: 'Not Found',
    message: `Route ${req.method} ${req.originalUrl} not found`,
  });
}

export function errorHandler(err, req, res, next) {
  console.error(`[ERROR] ${err.message}`);
  console.error(err.stack);

  const statusCode = err.statusCode || 500;

  res.status(statusCode).json({
    error: statusCode === 500 ? 'Internal Server Error' : err.message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack }),
  });
}
```

Register them **after** all routes:

```js
// index.js
import { notFound, errorHandler } from './middleware/errorHandler.js';

// ... all routes above

// 404 handler — catches unmatched routes
app.use(notFound);

// Global error handler — catches all thrown errors
app.use(errorHandler);
```

### 10. Throwing Errors in Controllers

```js
// utils/AppError.js
export class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

```js
// controllers/todoController.js
import { AppError } from '../utils/AppError.js';

export function getTodoById(req, res, next) {
  try {
    const id = Number(req.params.id);
    if (isNaN(id)) {
      throw new AppError('Invalid ID format', 400);
    }

    const todo = todos.find(t => t.id === id);
    if (!todo) {
      throw new AppError('Todo not found', 404);
    }

    res.json(todo);
  } catch (err) {
    next(err);  // Pass to error handler
  }
}
```

### 11. Async Error Wrapper

Avoid repeating `try/catch` in every async controller:

```js
// utils/asyncHandler.js
export function asyncHandler(fn) {
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}
```

```js
// routes/todoRoutes.js
import { asyncHandler } from '../utils/asyncHandler.js';
import { getAllTodos, createTodo } from '../controllers/todoController.js';

router.get('/', asyncHandler(getAllTodos));
router.post('/', validateBody(todoSchema), asyncHandler(createTodo));
```

### 12. Environment Variables with dotenv

```bash
npm install dotenv
```

```env
# .env
PORT=3000
NODE_ENV=development
API_KEY=your-secret-key-here
```

```js
// index.js (at the very top)
import 'dotenv/config';

const PORT = process.env.PORT || 3000;
const NODE_ENV = process.env.NODE_ENV || 'development';

app.listen(PORT, () => {
  console.log(`Server running in ${NODE_ENV} on port ${PORT}`);
});
```

> **Never commit `.env`** — add it to `.gitignore`.

### 13. Complete Middleware Pipeline

```js
// app.js
import express from 'express';
import cors from 'cors';
import morgan from 'morgan';
import helmet from 'helmet';
import 'dotenv/config';

import { requestId } from './middleware/requestId.js';
import { notFound, errorHandler } from './middleware/errorHandler.js';
import todoRoutes from './routes/todoRoutes.js';

const app = express();

// Security & utilities
app.use(helmet());
app.use(cors());
app.use(morgan('dev'));
app.use(requestId);

// Body parsing
app.use(express.json({ limit: '10kb' }));

// Routes
app.use('/api/todos', todoRoutes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok', uptime: process.uptime() });
});

// Error handling (must be last)
app.use(notFound);
app.use(errorHandler);

export default app;
```

---

## 💪 Hands-On Exercise

### Build a Middleware-Rich API

1. Set up `dotenv` with `PORT`, `NODE_ENV`, `API_KEY` variables
2. Add `helmet`, `cors`, `morgan` middleware
3. Create a custom `requestLogger` middleware that logs method, URL, status, and duration
4. Create a `validateBody` middleware for your todo schema
5. Create `AppError` class and centralized error handler
6. Create `asyncHandler` wrapper utility
7. Add a `notFound` catch-all middleware
8. Test that errors return proper JSON (not HTML stack traces)

---

## 📝 Homework

1. Create a rate-limiting middleware that allows max 100 requests per IP per minute
2. Add a `responseTime` middleware that sets an `X-Response-Time` header
3. Create a middleware that validates `Content-Type: application/json` on POST/PATCH requests
4. Custom error: throw `AppError('Todo not found', 404)` and verify the error handler catches it

---

## 💡 Pro Tips

- Middleware order matters — `express.json()` must come before routes that read `req.body`
- Error middleware must have exactly **4 parameters** — `(err, req, res, next)` — or Express won't recognize it
- Use `app.use(express.json({ limit: '10kb' }))` to prevent oversized payloads
- `asyncHandler` eliminates try/catch boilerplate — use it for every async controller
- `helmet()` sets 15+ security headers with one line

---

[← Day 4](day-04.md) | [Back to Week 13](README.md) | [Day 6 →](day-06.md)
