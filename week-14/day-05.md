# 📅 Week 14 — Day 5: API Versioning & Best Practices 🏆

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Implement API versioning strategies
- Add rate limiting to prevent abuse
- Enable response compression for performance
- Implement caching headers
- Apply production-ready security and performance best practices

---

## 📖 Lesson Content

### 1. Why Version Your API?

When your API changes, existing clients can break. Versioning lets you evolve your API without breaking existing users.

```
v1: GET /api/v1/posts → returns { title, content, author }
v2: GET /api/v2/posts → returns { title, body, author, readTime }
```

Both versions work simultaneously — old clients use v1, new clients use v2.

### 2. Versioning Strategies

| Strategy | Example | Pros | Cons |
|----------|---------|------|------|
| **URL Path** | `/api/v1/posts` | Simple, visible | URL changes |
| **Query Param** | `/api/posts?version=1` | No URL change | Easy to miss |
| **Header** | `Accept: application/vnd.blog.v1+json` | Clean URLs | Hidden |

**URL versioning** is the most common and recommended for beginners:

### 3. Implementing URL Versioning

```
src/
├── routes/
│   ├── v1/
│   │   ├── index.js
│   │   ├── postRoutes.js
│   │   └── categoryRoutes.js
│   ├── v2/
│   │   ├── index.js
│   │   └── postRoutes.js
│   └── index.js          ← Version router
```

```js
// src/routes/index.js
import { Router } from 'express';
import v1Routes from './v1/index.js';
import v2Routes from './v2/index.js';

const router = Router();

router.use('/v1', v1Routes);
router.use('/v2', v2Routes);

// Default version (latest)
router.use('/', v1Routes);

export default router;
```

```js
// src/routes/v1/index.js
import { Router } from 'express';
import postRoutes from './postRoutes.js';
import categoryRoutes from './categoryRoutes.js';

const router = Router();

router.use('/posts', postRoutes);
router.use('/categories', categoryRoutes);

export default router;
```

```js
// app.js
import apiRoutes from './routes/index.js';
app.use('/api', apiRoutes);

// Now supports:
// /api/v1/posts
// /api/v2/posts
// /api/posts (defaults to v1)
```

### 4. Rate Limiting

Prevent abuse and brute-force attacks:

```bash
npm install express-rate-limit
```

```js
// middleware/rateLimiter.js
import rateLimit from 'express-rate-limit';

// General API rate limit
export const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,   // 15 minutes
  max: 100,                    // 100 requests per window per IP
  standardHeaders: true,       // Return rate limit info in headers
  legacyHeaders: false,
  message: {
    status: 'error',
    message: 'Too many requests — please try again in 15 minutes',
  },
});

// Stricter limit for write operations
export const writeLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 30,                     // 30 writes per 15 minutes
  message: {
    status: 'error',
    message: 'Too many write requests — please slow down',
  },
});
```

```js
// app.js
import { apiLimiter, writeLimiter } from './middleware/rateLimiter.js';

// Apply general limit to all API routes
app.use('/api', apiLimiter);

// Apply stricter limit to POST/PUT/PATCH/DELETE
app.use('/api/posts', writeLimiter);
```

**Rate limit headers returned:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 97
X-RateLimit-Reset: 1700000000
```

### 5. Response Compression

Compress responses to reduce transfer size:

```bash
npm install compression
```

```js
// app.js
import compression from 'compression';

// Compress all responses > 1kb
app.use(compression({
  threshold: 1024,  // Only compress responses > 1KB
  level: 6,         // Compression level (1-9, 6 is default)
}));
```

**Impact:** JSON responses often compress by 60-80%, dramatically improving load times.

### 6. HTTP Caching

```js
// middleware/cache.js
export function cacheResponse(duration) {
  return (req, res, next) => {
    if (req.method === 'GET') {
      res.set('Cache-Control', `public, max-age=${duration}`);
    } else {
      res.set('Cache-Control', 'no-store');
    }
    next();
  };
}
```

```js
// routes/postRoutes.js
import { cacheResponse } from '../middleware/cache.js';

// Cache post listing for 5 minutes (300 seconds)
router.get('/', cacheResponse(300), asyncHandler(getAllPosts));

// Don't cache individual posts if they have view counters
router.get('/:id', asyncHandler(getOnePost));
```

### 7. ETag Support

ETags let clients check if data has changed without downloading it again:

```js
// middleware/etag.js
import crypto from 'crypto';

export function generateETag(req, res, next) {
  const originalJson = res.json.bind(res);

  res.json = function (body) {
    const content = JSON.stringify(body);
    const hash = crypto.createHash('md5').update(content).digest('hex');
    const etag = `"${hash}"`;

    // Check if client has the same version
    if (req.headers['if-none-match'] === etag) {
      return res.status(304).end();
    }

    res.set('ETag', etag);
    originalJson(body);
  };

  next();
}
```

### 8. CORS Configuration

Fine-tune cross-origin access:

```js
// config/cors.js
import cors from 'cors';

const allowedOrigins = [
  'http://localhost:3000',
  'http://localhost:5173',  // Vite dev server
  'https://yourdomain.com',
];

export const corsOptions = cors({
  origin: (origin, callback) => {
    // Allow requests with no origin (Postman, curl, server-side)
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-API-Key'],
  credentials: true,
  maxAge: 86400,  // Cache preflight for 24 hours
});
```

```js
// app.js
import { corsOptions } from './config/cors.js';
app.use(corsOptions);
```

### 9. Request Size Limits

```js
// Limit JSON body size
app.use(express.json({ limit: '10kb' }));

// Limit URL-encoded form data
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// Custom limit for specific routes (e.g., blog content)
app.use('/api/posts', express.json({ limit: '50kb' }));
```

### 10. Graceful Shutdown

```js
// index.js
import 'dotenv/config';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;

const server = app.listen(PORT, () => {
  console.log(`Blog API running on port ${PORT}`);
});

// Handle graceful shutdown
function shutdown(signal) {
  console.log(`\n${signal} received — shutting down gracefully`);

  server.close(() => {
    console.log('Server closed');
    process.exit(0);
  });

  // Force shutdown after 10 seconds
  setTimeout(() => {
    console.error('Forced shutdown');
    process.exit(1);
  }, 10000);
}

process.on('SIGTERM', () => shutdown('SIGTERM'));
process.on('SIGINT', () => shutdown('SIGINT'));
```

### 11. Production Checklist

```js
// app.js — Production-ready setup
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import compression from 'compression';
import 'dotenv/config';

import { apiLimiter } from './middleware/rateLimiter.js';
import { notFound, errorHandler } from './middleware/errorHandler.js';
import apiRoutes from './routes/index.js';

const app = express();

// ── Security ──
app.use(helmet());
app.use(cors());

// ── Performance ──
app.use(compression());

// ── Logging ──
app.use(morgan(process.env.NODE_ENV === 'production' ? 'combined' : 'dev'));

// ── Body Parsing ──
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// ── Rate Limiting ──
app.use('/api', apiLimiter);

// ── Static Files ──
app.use('/uploads', express.static('uploads'));

// ── Routes ──
app.use('/api', apiRoutes);

// ── Health Check ──
app.get('/health', (req, res) => {
  res.json({
    status: 'ok',
    environment: process.env.NODE_ENV,
    uptime: `${Math.floor(process.uptime())}s`,
    timestamp: new Date().toISOString(),
  });
});

// ── Error Handling ──
app.use(notFound);
app.use(errorHandler);

export default app;
```

---

## 💪 Hands-On Exercise

### Production-Harden the Blog API

1. Implement URL-based API versioning (`/api/v1/posts`)
2. Add rate limiting: 100 req/15min general, 30 req/15min for writes
3. Enable response compression
4. Add `Cache-Control` headers to `GET /api/posts` (5 minute cache)
5. Configure CORS to allow only specific origins
6. Add graceful shutdown handling
7. Test rate limiting by sending 101 requests quickly

---

## 📝 Homework

1. Create a v2 of the posts endpoint that returns `readTime` (estimated minutes to read based on content length)
2. Test that both `/api/v1/posts` and `/api/v2/posts` work simultaneously
3. Add `X-Response-Time` header middleware that shows how long each request took
4. Implement a custom rate limiter (without the npm package) using a `Map` to track IP request counts
5. Review the production checklist — does your API have all items implemented?

---

## 💡 Pro Tips

- Start with URL versioning — it's the simplest and most explicit
- Rate limiting is **essential** for any public API — prevent abuse from day one
- Compression can reduce response sizes by 60-80% with minimal CPU cost
- Use `Cache-Control: no-store` for user-specific data (profiles, dashboards)
- In production, use `morgan('combined')` for detailed access logs
- Graceful shutdown prevents in-flight requests from being dropped during deployments
- Set request body size limits to prevent denial-of-service via huge payloads

---

[← Day 4](day-04.md) | [Back to Week 14](README.md) | [Day 6 →](day-06.md)
