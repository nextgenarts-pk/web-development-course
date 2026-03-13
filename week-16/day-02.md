# 📅 Week 16 — Day 2: JWT Authentication 🎫

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand **JSON Web Tokens** (JWT) and how they work
- Build **login** and **signup** endpoints that return tokens
- Store and send tokens from the client
- Verify tokens on the server

---

## 🎫 What is JWT?

A **JSON Web Token** is a compact, self-contained way to represent claims between two parties.

### JWT Structure

```
eyJhbGciOiJIUzI1NiJ9.eyJpZCI6IjY1YTEyMyIsInJvbGUiOiJ1c2VyIn0.s8Kf2xG9mP3qR7nL5jY1w
│─── Header ────│──────── Payload ─────────│──── Signature ─────│
```

| Part | Contains | Example |
|------|----------|---------|
| **Header** | Algorithm & token type | `{ "alg": "HS256", "typ": "JWT" }` |
| **Payload** | User data (claims) | `{ "id": "65a123", "role": "user" }` |
| **Signature** | Verification hash | `HMACSHA256(header + payload, secret)` |

### How JWT Auth Works

```
1. Client → POST /login { email, password }
2. Server → Verifies credentials → Signs JWT with secret
3. Server → Returns { token: "eyJ..." }
4. Client → Stores token (localStorage / cookie)
5. Client → GET /api/notes  Headers: { Authorization: "Bearer eyJ..." }
6. Server → Verifies token → Returns data
```

---

## 📦 Setup

```bash
npm install jsonwebtoken
```

```env
# .env
JWT_SECRET=your-super-secret-key-at-least-32-chars-long-change-in-production
JWT_EXPIRES_IN=7d
```

> ⚠️ **Never hardcode secrets!** Always use environment variables.

---

## 🔧 Token Helper

```js
// src/utils/jwt.js
import jwt from 'jsonwebtoken';

export function signToken(userId) {
  return jwt.sign(
    { id: userId },
    process.env.JWT_SECRET,
    { expiresIn: process.env.JWT_EXPIRES_IN }
  );
}

export function verifyToken(token) {
  return jwt.verify(token, process.env.JWT_SECRET);
}
```

---

## 🔐 Complete Auth Controller

```js
// src/controllers/authController.js
import User from '../models/User.js';
import { signToken } from '../utils/jwt.js';
import { AppError } from '../utils/AppError.js';

// Helper: create token and send response
function sendTokenResponse(user, statusCode, res) {
  const token = signToken(user._id);

  // Remove password from output
  const userData = {
    id: user._id,
    name: user.name,
    email: user.email,
    role: user.role,
  };

  res.status(statusCode).json({
    status: 'success',
    token,
    data: userData,
  });
}

// POST /api/auth/signup
export async function signup(req, res) {
  const { name, email, password, confirmPassword } = req.body;

  if (password !== confirmPassword) {
    throw new AppError('Passwords do not match', 400);
  }

  const user = await User.create({ name, email, password });
  sendTokenResponse(user, 201, res);
}

// POST /api/auth/login
export async function login(req, res) {
  const { email, password } = req.body;

  // 1. Check if email and password provided
  if (!email || !password) {
    throw new AppError('Please provide email and password', 400);
  }

  // 2. Find user and explicitly select password (it's excluded by default)
  const user = await User.findOne({ email }).select('+password');

  // 3. Check if user exists AND password is correct
  if (!user || !(await user.comparePassword(password))) {
    throw new AppError('Invalid email or password', 401);
  }

  // 4. Check if account is active
  if (!user.isActive) {
    throw new AppError('Your account has been deactivated', 403);
  }

  // 5. Send token
  sendTokenResponse(user, 200, res);
}

// GET /api/auth/me (protected — requires auth middleware from Day 3)
export async function getMe(req, res) {
  const user = await User.findById(req.user.id);
  res.json({
    status: 'success',
    data: {
      id: user._id,
      name: user.name,
      email: user.email,
      role: user.role,
      createdAt: user.createdAt,
    },
  });
}
```

### Security Notes

- **Generic error message** on login failure: `"Invalid email or password"` — never reveal whether the email or password was wrong
- **`select('+password')`** — explicitly include the password field for comparison
- Check `isActive` after verifying credentials

---

## 🛣️ Auth Routes

```js
// src/routes/authRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { signup, login, getMe } from '../controllers/authController.js';

const router = Router();

router.post('/signup', asyncHandler(signup));
router.post('/login', asyncHandler(login));
// router.get('/me', protect, asyncHandler(getMe)); // Day 3

export default router;
```

```js
// Register routes in app.js
import authRoutes from './routes/authRoutes.js';
app.use('/api/auth', authRoutes);
```

---

## 🔍 Understanding JWT in Practice

```js
// What's inside a token
import jwt from 'jsonwebtoken';

const token = signToken('65a1b2c3d4e5f6a7b8c9d0e1');
console.log(token);
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjY1YTFi...

// Decode without verification (anyone can read payload!)
const decoded = jwt.decode(token);
console.log(decoded);
// { id: '65a1b2c3d4e5f6a7b8c9d0e1', iat: 1705123456, exp: 1705728256 }

// Verify with secret (only server can do this)
const verified = jwt.verify(token, process.env.JWT_SECRET);
console.log(verified);
// { id: '65a1b2c3d4e5f6a7b8c9d0e1', iat: 1705123456, exp: 1705728256 }

// Tampered token → throws error
jwt.verify(token + 'tampered', process.env.JWT_SECRET);
// JsonWebTokenError: invalid signature
```

> ⚠️ **JWT payload is NOT encrypted** — it's only Base64 encoded. Never put sensitive data (password, credit card) in the payload.

---

## ⏱️ Token Expiration

| Value | Duration | Use Case |
|-------|----------|----------|
| `15m` | 15 minutes | Access tokens (with refresh token) |
| `1h` | 1 hour | Short-lived sessions |
| `7d` | 7 days | **Standard web apps** |
| `30d` | 30 days | "Remember me" sessions |

```js
// Handling expired tokens
try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
} catch (err) {
  if (err.name === 'TokenExpiredError') {
    // Token was valid but expired
    console.log('Token expired at:', err.expiredAt);
  }
  if (err.name === 'JsonWebTokenError') {
    // Token is invalid/tampered
    console.log('Invalid token');
  }
}
```

---

## 🧪 Testing Auth

```bash
# Signup
curl -X POST http://localhost:3000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"name":"Ali Khan","email":"ali@example.com","password":"securePass123","confirmPassword":"securePass123"}'

# Response:
# {
#   "status": "success",
#   "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
#   "data": { "id": "...", "name": "Ali Khan", "email": "ali@example.com", "role": "user" }
# }

# Login
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ali@example.com","password":"securePass123"}'

# Wrong password
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ali@example.com","password":"wrongPassword"}'
# → 401: "Invalid email or password"
```

---

## 🏋️ Hands-On Exercise

1. Sign up **3 different users** and verify each receives a unique token
2. Decode one token at [jwt.io](https://jwt.io) and inspect the payload
3. Try changing part of the token string and verify it fails on `jwt.verify()`
4. Add a `lastLogin` field to User model, updated on every successful login

---

## 📚 Homework

1. Research: What's the difference between **symmetric** (HS256) and **asymmetric** (RS256) JWT signing?
2. Research: What are **refresh tokens** and why do apps use access + refresh token pairs?
3. Add a `PATCH /api/auth/update-password` endpoint that requires the current password and sets a new one

---

## 💡 Pro Tips

- **JWT secret** should be at least 32 characters and cryptographically random
- **Never store JWTs in `localStorage`** for high-security apps (XSS vulnerable) — use `httpOnly` cookies
- **Payload is readable** — only put non-sensitive identifiers (user ID, role)
- **Short expiry + refresh tokens** is the production standard
- **One function** (`sendTokenResponse`) ensures consistent token delivery

---

[← Day 1](day-01.md) | [Back to Week 16](README.md) | [Day 3 →](day-03.md)
