# 📅 Week 16 — Day 5: Security Best Practices 🔒

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand the **OWASP Top 10** threats relevant to APIs
- Apply **security headers** with Helmet
- Prevent **brute-force attacks** with rate limiting
- Sanitize input against **NoSQL injection** and **XSS**
- Implement a **security checklist** for production

---

## ⚠️ Top API Security Threats

| Threat | Description | Defense |
|--------|-------------|---------|
| **Broken Authentication** | Weak passwords, no rate limiting | bcrypt + JWT + rate limit |
| **Injection** | NoSQL injection, command injection | Input validation + sanitization |
| **XSS** | Injecting scripts into responses | Sanitize inputs + security headers |
| **Broken Access Control** | Accessing others' data | protect + canModify + role checks |
| **Security Misconfiguration** | Default settings, verbose errors | Helmet + NODE_ENV check |
| **Sensitive Data Exposure** | Passwords, tokens in responses | `select: false` + no logging secrets |
| **Rate Limiting Missing** | DDoS, brute force | express-rate-limit |

---

## 📦 Security Packages

```bash
npm install helmet express-rate-limit express-mongo-sanitize hpp xss
```

| Package | Purpose |
|---------|---------|
| `helmet` | Sets secure HTTP headers |
| `express-rate-limit` | Limit repeated requests |
| `express-mongo-sanitize` | Remove `$` and `.` from req.body (NoSQL injection) |
| `hpp` | HTTP Parameter Pollution prevention |
| `xss` | Sanitize HTML input to prevent XSS |

---

## 🔒 Security Middleware Setup

```js
// src/app.js — Complete secure setup
import express from 'express';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import mongoSanitize from 'express-mongo-sanitize';
import hpp from 'hpp';
import cors from 'cors';
import morgan from 'morgan';
import compression from 'compression';

const app = express();

// 1. Security Headers (must be first)
app.use(helmet());

// 2. CORS — allow only trusted origins
app.use(cors({
  origin: process.env.NODE_ENV === 'production'
    ? ['https://myapp.com', 'https://www.myapp.com']
    : '*',
  methods: ['GET', 'POST', 'PATCH', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,
}));

// 3. Global rate limiter — 100 requests per 15 minutes per IP
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: {
    status: 'error',
    message: 'Too many requests from this IP, please try again in 15 minutes.',
  },
  standardHeaders: true,
  legacyHeaders: false,
});
app.use('/api', globalLimiter);

// 4. Auth-specific rate limiter — 10 attempts per hour
const authLimiter = rateLimit({
  windowMs: 60 * 60 * 1000, // 1 hour
  max: 10,
  message: {
    status: 'error',
    message: 'Too many login attempts, please try again in an hour.',
  },
  skipSuccessfulRequests: true, // Don't count successful logins
});
app.use('/api/auth/login', authLimiter);
app.use('/api/auth/signup', authLimiter);

// 5. Logging (only in development)
if (process.env.NODE_ENV === 'development') {
  app.use(morgan('dev'));
}

// 6. Body parser — limit payload size
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// 7. NoSQL injection prevention
// Sanitizes: {"email": {"$gt": ""}} → {"email": {}}
app.use(mongoSanitize());

// 8. HTTP Parameter Pollution prevention
// Prevents: ?sort=price&sort=name → uses last sort value
app.use(hpp({
  whitelist: ['price', 'rating', 'category'], // Allow these to have multiple values
}));

// 9. Compression
app.use(compression());

export default app;
```

---

## 🛡️ Helmet Headers Explained

```js
// What helmet sets:
// Content-Security-Policy: default-src 'self'
// X-Content-Type-Options: nosniff
// X-Frame-Options: SAMEORIGIN
// X-XSS-Protection: 0 (modern browsers ignore this, use CSP instead)
// Strict-Transport-Security: max-age=15552000 (HTTPS only)
// Referrer-Policy: no-referrer

// Custom helmet config for APIs
app.use(helmet({
  contentSecurityPolicy: false, // Disable for API-only apps
  crossOriginEmbedderPolicy: false,
}));
```

---

## 🧹 Input Sanitization

```js
// src/middleware/sanitize.js
import xss from 'xss';

// Recursively sanitize all string fields in an object
export function sanitizeObject(obj) {
  for (const key in obj) {
    if (typeof obj[key] === 'string') {
      obj[key] = xss(obj[key].trim());
    } else if (typeof obj[key] === 'object' && obj[key] !== null) {
      sanitizeObject(obj[key]);
    }
  }
  return obj;
}

// Middleware to sanitize req.body, req.query, req.params
export function sanitizeInput(req, res, next) {
  if (req.body) sanitizeObject(req.body);
  if (req.query) sanitizeObject(req.query);
  if (req.params) sanitizeObject(req.params);
  next();
}
```

```js
// Apply globally in app.js (after express.json)
import { sanitizeInput } from './middleware/sanitize.js';
app.use(sanitizeInput);
```

---

## 🔑 Secure JWT with Cookies

For browser clients, **httpOnly cookies** are more secure than localStorage:

```js
// src/controllers/authController.js

function sendTokenResponse(user, statusCode, res) {
  const token = signToken(user._id);

  const cookieOptions = {
    expires: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000), // 7 days
    httpOnly: true,   // Cannot be accessed by JavaScript
    secure: process.env.NODE_ENV === 'production', // HTTPS only in production
    sameSite: 'strict', // CSRF protection
  };

  // Set cookie AND return token (supports both cookie and header auth)
  res.cookie('jwt', token, cookieOptions);

  res.status(statusCode).json({
    status: 'success',
    token,
    data: {
      id: user._id,
      name: user.name,
      email: user.email,
      role: user.role,
    },
  });
}
```

```js
// Update protect middleware to also read from cookies
import cookieParser from 'cookie-parser'; // npm install cookie-parser
app.use(cookieParser());

// In protect middleware
let token;
if (req.headers.authorization?.startsWith('Bearer')) {
  token = req.headers.authorization.split(' ')[1];
} else if (req.cookies?.jwt) {
  token = req.cookies.jwt;  // Cookie-based auth
}
```

---

## 🔐 Password Policy Validation

```js
// src/middleware/validate.js

export function validatePassword(req, res, next) {
  const { password } = req.body;
  if (!password) return next();

  const checks = [
    { test: password.length >= 8, msg: 'At least 8 characters' },
    { test: /[A-Z]/.test(password), msg: 'At least one uppercase letter' },
    { test: /[a-z]/.test(password), msg: 'At least one lowercase letter' },
    { test: /\d/.test(password), msg: 'At least one number' },
    { test: !/\s/.test(password), msg: 'No spaces allowed' },
  ];

  const failed = checks.filter(c => !c.test).map(c => c.msg);
  if (failed.length > 0) {
    return res.status(400).json({
      status: 'error',
      message: 'Password does not meet requirements',
      requirements: failed,
    });
  }

  next();
}
```

```js
// Apply to auth routes
router.post('/signup', validatePassword, asyncHandler(signup));
router.patch('/update-password', protect, validatePassword, asyncHandler(updatePassword));
```

---

## 🚫 Account Lockout

```js
// Add to User model
loginAttempts: { type: Number, default: 0 },
lockUntil: { type: Date },

// Add method to check/update lockout
userSchema.methods.handleFailedLogin = async function() {
  this.loginAttempts += 1;
  if (this.loginAttempts >= 5) {
    // Lock for 30 minutes
    this.lockUntil = new Date(Date.now() + 30 * 60 * 1000);
    this.loginAttempts = 0;
  }
  await this.save();
};

userSchema.methods.resetLoginAttempts = async function() {
  this.loginAttempts = 0;
  this.lockUntil = undefined;
  await this.save();
};
```

```js
// In login controller
// Check if account is locked
if (user.lockUntil && user.lockUntil > new Date()) {
  const minutesLeft = Math.ceil((user.lockUntil - new Date()) / 60000);
  throw new AppError(`Account locked. Try again in ${minutesLeft} minutes.`, 429);
}

if (!(await user.comparePassword(password))) {
  await user.handleFailedLogin();
  throw new AppError('Invalid email or password', 401);
}

// Successful login — reset attempts
await user.resetLoginAttempts();
```

---

## 🌐 Error Handling in Production

```js
// Secure error handler — don't leak details in production
export function errorHandler(err, req, res, next) {
  console.error(err); // Log full error server-side

  // Known/operational errors (our AppError) — safe to show
  if (err.statusCode) {
    return res.status(err.statusCode).json({
      status: 'error',
      message: err.message,
    });
  }

  // Mongoose errors — safe to remap
  if (err.name === 'ValidationError') {
    return res.status(400).json({ status: 'error', message: 'Validation failed', errors: Object.values(err.errors).map(e => e.message) });
  }
  if (err.code === 11000) {
    const field = Object.keys(err.keyPattern)[0];
    return res.status(409).json({ status: 'error', message: `Duplicate value for ${field}` });
  }
  if (err.name === 'CastError') {
    return res.status(400).json({ status: 'error', message: `Invalid ${err.path}: ${err.value}` });
  }

  // Unknown errors — never show details in production
  if (process.env.NODE_ENV === 'production') {
    return res.status(500).json({ status: 'error', message: 'Something went wrong' });
  }

  // Development — show full error
  res.status(500).json({ status: 'error', message: err.message, stack: err.stack });
}
```

---

## ✅ Security Checklist

```
Authentication
  ☑ Passwords hashed with bcrypt (12+ rounds)
  ☑ JWT with expiration (7d)
  ☑ httpOnly cookies in browser apps
  ☑ JWT secret in .env (32+ chars)

Rate Limiting
  ☑ Global rate limit: 100 req / 15 min
  ☑ Auth rate limit: 10 attempts / hour
  ☑ Account lockout after 5 failed logins

Input Validation
  ☑ mongoSanitize (NoSQL injection)
  ☑ XSS sanitization on strings
  ☑ HPP (Parameter Pollution)
  ☑ Body size limit (10kb)

Access Control
  ☑ Protect middleware on all private routes
  ☑ Role checks with restrictTo
  ☑ Ownership checks with canModify

Response Security
  ☑ Security headers (Helmet)
  ☑ No passwords in responses (select: false)
  ☑ Generic error messages in production
  ☑ No stack traces in production

CORS
  ☑ Whitelist allowed origins in production
  ☑ Only allow required HTTP methods
```

---

## 🏋️ Hands-On Exercise

1. Add all security middleware to your auth app
2. Test NoSQL injection: `{"email": {"$gt": ""}, "password": "any"}` — verify it's blocked
3. Test rate limiting: make 11 login attempts → verify lockout
4. Run your app through a free security scanner like [observatory.mozilla.org](https://observatory.mozilla.org)

---

## 📚 Homework

1. Research: What is **CSRF** and when do `httpOnly` cookies need CSRF tokens?
2. Add a `/api/auth/logout` endpoint that clears the JWT cookie
3. Research **HTTPS** setup for Node.js in production (Let's Encrypt with Nginx)

---

## 💡 Pro Tips

- **`mongoSanitize`** removes `$` and `.` — stops `{"$gt": ""}` injection attacks
- **Order matters** — body parser must come before `mongoSanitize` and `hpp`
- **Never expose `process.env`** in API responses
- **Log security events** server-side (failed logins, blocked IPs) for monitoring
- **Test with OWASP ZAP** or Burp Suite before going to production

---

[← Day 4](day-04.md) | [Back to Week 16](README.md) | [Day 6 →](day-06.md)
