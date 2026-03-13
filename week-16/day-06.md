# 📅 Week 16 — Day 6: Project Day — Secure User Auth System 🔐

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goal

Build a **complete Secure User Authentication System** — the authentication foundation every real-world application needs. This integrates everything from Weeks 13–16.

---

## 🏗️ Project Overview

| Feature | Description |
|---------|-------------|
| **Registration** | Sign up with name, email, password, role (default: user) |
| **Login** | JWT issued on success, account lockout after 5 failed attempts |
| **Protected Routes** | JWT verify middleware on all private endpoints |
| **Profile Management** | View/update profile, change password |
| **Role-Based Access** | user / editor / admin with different permissions |
| **Admin Panel** | List users, change roles, activate/deactivate accounts |
| **Security** | bcrypt, helmet, rate limiting, NoSQL sanitization, XSS protection |

---

## 📁 Project Structure

```
auth-system/
├── src/
│   ├── config/
│   │   └── database.js
│   ├── controllers/
│   │   ├── authController.js
│   │   └── adminController.js
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── validate.js
│   │   └── errorHandler.js
│   ├── models/
│   │   └── User.js
│   ├── routes/
│   │   ├── index.js
│   │   ├── authRoutes.js
│   │   └── adminRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   └── jwt.js
│   └── app.js
├── scripts/
│   └── seed.js
├── .env
├── .gitignore
├── index.js
└── package.json
```

---

## 🔧 Setup

```bash
mkdir auth-system && cd auth-system
npm init -y
npm install express mongoose bcrypt jsonwebtoken dotenv cors helmet morgan \
            express-rate-limit express-mongo-sanitize hpp cookie-parser compression
npm install -D nodemon
```

```json
{
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "seed": "node scripts/seed.js"
  }
}
```

```env
# .env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/auth_system
JWT_SECRET=your-super-secret-key-at-least-32-chars-long-change-in-production-now
JWT_EXPIRES_IN=7d
BCRYPT_ROUNDS=12
```

---

## 🔧 Model

```js
// src/models/User.js
import mongoose from 'mongoose';
import bcrypt from 'bcrypt';

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Name is required'],
    trim: true,
    minlength: [2, 'Name must be at least 2 characters'],
    maxlength: [50, 'Name cannot exceed 50 characters'],
  },
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true,
    trim: true,
    match: [/^\S+@\S+\.\S+$/, 'Please provide a valid email'],
  },
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: [8, 'Password must be at least 8 characters'],
    select: false,
  },
  role: {
    type: String,
    enum: ['user', 'editor', 'admin'],
    default: 'user',
  },
  isActive: { type: Boolean, default: true },
  loginAttempts: { type: Number, default: 0, select: false },
  lockUntil: { type: Date, select: false },
  passwordChangedAt: { type: Date, select: false },
}, {
  timestamps: true,
});

// Hash password on save
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  const rounds = parseInt(process.env.BCRYPT_ROUNDS) || 12;
  this.password = await bcrypt.hash(this.password, rounds);
  if (!this.isNew) this.passwordChangedAt = new Date();
  next();
});

// Compare password
userSchema.methods.comparePassword = async function(candidate) {
  return bcrypt.compare(candidate, this.password);
};

// Handle failed login
userSchema.methods.handleFailedLogin = async function() {
  this.loginAttempts += 1;
  if (this.loginAttempts >= 5) {
    this.lockUntil = new Date(Date.now() + 30 * 60 * 1000);
    this.loginAttempts = 0;
  }
  await this.save({ validateBeforeSave: false });
};

// Reset after successful login
userSchema.methods.resetLoginAttempts = async function() {
  this.loginAttempts = 0;
  this.lockUntil = undefined;
  await this.save({ validateBeforeSave: false });
};

export default mongoose.model('User', userSchema);
```

---

## 🔧 Utilities

```js
// src/utils/AppError.js
export class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}

// src/utils/asyncHandler.js
export function asyncHandler(fn) {
  return (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);
}

// src/utils/jwt.js
import jwt from 'jsonwebtoken';
export function signToken(userId) {
  return jwt.sign({ id: userId }, process.env.JWT_SECRET, { expiresIn: process.env.JWT_EXPIRES_IN });
}
export function verifyToken(token) {
  return jwt.verify(token, process.env.JWT_SECRET);
}
```

---

## 🔧 Auth Middleware & Error Handler

```js
// src/middleware/auth.js
import { verifyToken } from '../utils/jwt.js';
import User from '../models/User.js';
import { AppError } from '../utils/AppError.js';

export async function protect(req, res, next) {
  let token;
  if (req.headers.authorization?.startsWith('Bearer')) {
    token = req.headers.authorization.split(' ')[1];
  } else if (req.cookies?.jwt) {
    token = req.cookies.jwt;
  }
  if (!token) throw new AppError('Not logged in. Please provide a token.', 401);

  let decoded;
  try {
    decoded = verifyToken(token);
  } catch (err) {
    throw new AppError(
      err.name === 'TokenExpiredError' ? 'Token expired. Please log in again.' : 'Invalid token.',
      401
    );
  }

  const user = await User.findById(decoded.id).select('+passwordChangedAt');
  if (!user) throw new AppError('User no longer exists.', 401);
  if (!user.isActive) throw new AppError('Account deactivated.', 403);

  if (user.passwordChangedAt) {
    const changedAt = Math.floor(user.passwordChangedAt.getTime() / 1000);
    if (decoded.iat < changedAt) throw new AppError('Password changed. Please log in again.', 401);
  }

  req.user = user;
  next();
}

export function restrictTo(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      throw new AppError('You do not have permission to perform this action', 403);
    }
    next();
  };
}
```

```js
// src/middleware/errorHandler.js
export function errorHandler(err, req, res, next) {
  console.error(err.name, err.message);

  if (err.statusCode) {
    return res.status(err.statusCode).json({ status: 'error', message: err.message });
  }
  if (err.name === 'ValidationError') {
    return res.status(400).json({ status: 'error', message: 'Validation failed', errors: Object.values(err.errors).map(e => e.message) });
  }
  if (err.code === 11000) {
    const field = Object.keys(err.keyPattern)[0];
    return res.status(409).json({ status: 'error', message: `That ${field} is already registered` });
  }
  if (err.name === 'CastError') {
    return res.status(400).json({ status: 'error', message: `Invalid ${err.path}: "${err.value}"` });
  }

  if (process.env.NODE_ENV === 'production') {
    return res.status(500).json({ status: 'error', message: 'Something went wrong' });
  }
  res.status(500).json({ status: 'error', message: err.message, stack: err.stack });
}
```

---

## 🔧 Controllers

```js
// src/controllers/authController.js
import User from '../models/User.js';
import { signToken } from '../utils/jwt.js';
import { AppError } from '../utils/AppError.js';

function sendTokenResponse(user, statusCode, res) {
  const token = signToken(user._id);
  res.cookie('jwt', token, {
    expires: new Date(Date.now() + 7 * 24 * 60 * 60 * 1000),
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict',
  });
  res.status(statusCode).json({
    status: 'success', token,
    data: { id: user._id, name: user.name, email: user.email, role: user.role },
  });
}

export async function signup(req, res) {
  const { name, email, password, confirmPassword } = req.body;
  if (password !== confirmPassword) throw new AppError('Passwords do not match', 400);
  const user = await User.create({ name, email, password });
  sendTokenResponse(user, 201, res);
}

export async function login(req, res) {
  const { email, password } = req.body;
  if (!email || !password) throw new AppError('Please provide email and password', 400);

  const user = await User.findOne({ email }).select('+password +loginAttempts +lockUntil');
  if (!user) throw new AppError('Invalid email or password', 401);

  if (user.lockUntil && user.lockUntil > new Date()) {
    const mins = Math.ceil((user.lockUntil - new Date()) / 60000);
    throw new AppError(`Account locked. Try again in ${mins} minutes.`, 429);
  }

  if (!(await user.comparePassword(password))) {
    await user.handleFailedLogin();
    throw new AppError('Invalid email or password', 401);
  }

  if (!user.isActive) throw new AppError('Account deactivated.', 403);
  await user.resetLoginAttempts();
  sendTokenResponse(user, 200, res);
}

export async function logout(req, res) {
  res.cookie('jwt', 'logged_out', { expires: new Date(Date.now() + 1000), httpOnly: true });
  res.json({ status: 'success', message: 'Logged out successfully' });
}

export async function getMe(req, res) {
  const user = await User.findById(req.user.id);
  res.json({ status: 'success', data: user });
}

export async function updateMe(req, res) {
  if (req.body.password) throw new AppError('Use /update-password to change password', 400);
  const { name, email } = req.body;
  const user = await User.findByIdAndUpdate(req.user.id, { name, email }, { new: true, runValidators: true });
  res.json({ status: 'success', data: user });
}

export async function updatePassword(req, res) {
  const { currentPassword, newPassword, confirmPassword } = req.body;
  if (newPassword !== confirmPassword) throw new AppError('Passwords do not match', 400);
  const user = await User.findById(req.user.id).select('+password');
  if (!(await user.comparePassword(currentPassword))) throw new AppError('Current password is incorrect', 401);
  user.password = newPassword;
  await user.save();
  sendTokenResponse(user, 200, res);
}

export async function deleteMe(req, res) {
  await User.findByIdAndUpdate(req.user.id, { isActive: false });
  res.cookie('jwt', 'logged_out', { expires: new Date(Date.now() + 1000), httpOnly: true });
  res.json({ status: 'success', message: 'Account deactivated successfully' });
}
```

---

## 🔧 App & Server

```js
// src/app.js
import express from 'express';
import helmet from 'helmet';
import rateLimit from 'express-rate-limit';
import mongoSanitize from 'express-mongo-sanitize';
import hpp from 'hpp';
import cors from 'cors';
import morgan from 'morgan';
import compression from 'compression';
import cookieParser from 'cookie-parser';
import apiRoutes from './routes/index.js';
import { errorHandler } from './middleware/errorHandler.js';

const app = express();

app.use(helmet());
app.use(cors({ origin: process.env.NODE_ENV === 'production' ? process.env.CLIENT_URL : '*', credentials: true }));
app.use(compression());
app.use(cookieParser());
if (process.env.NODE_ENV === 'development') app.use(morgan('dev'));
app.use(express.json({ limit: '10kb' }));
app.use(mongoSanitize());
app.use(hpp());

app.use('/api', rateLimit({ windowMs: 15 * 60 * 1000, max: 100, message: { status: 'error', message: 'Too many requests' } }));
app.use(['/api/auth/login', '/api/auth/signup'],
  rateLimit({ windowMs: 60 * 60 * 1000, max: 10, skipSuccessfulRequests: true, message: { status: 'error', message: 'Too many auth attempts' } })
);

app.get('/health', (req, res) => res.json({ status: 'ok', env: process.env.NODE_ENV, timestamp: new Date().toISOString() }));
app.use('/api', apiRoutes);
app.use((req, res) => res.status(404).json({ status: 'error', message: `Route ${req.method} ${req.originalUrl} not found` }));
app.use(errorHandler);

export default app;
```

```js
// index.js
import 'dotenv/config';
import mongoose from 'mongoose';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;

mongoose.connect(process.env.MONGODB_URI).then(() => {
  console.log('MongoDB connected');
  app.listen(PORT, () => console.log(`🔐 Auth System running on http://localhost:${PORT}`));
}).catch(err => {
  console.error('DB connection failed:', err);
  process.exit(1);
});
```

---

## ✅ Complete API Endpoints

| Method | Endpoint | Access | Description |
|--------|----------|--------|-------------|
| `POST` | `/api/auth/signup` | Public | Register new user |
| `POST` | `/api/auth/login` | Public | Login, receive JWT |
| `GET` | `/api/auth/logout` | Any | Clear JWT cookie |
| `GET` | `/api/auth/me` | Auth | Get my profile |
| `PATCH` | `/api/auth/update-me` | Auth | Update name/email |
| `PATCH` | `/api/auth/update-password` | Auth | Change password |
| `DELETE` | `/api/auth/delete-me` | Auth | Deactivate account |
| `GET` | `/api/admin/users` | Admin | List all users |
| `GET` | `/api/admin/users/:id` | Admin | Get user by ID |
| `PATCH` | `/api/admin/users/:id/role` | Admin | Change user role |
| `PATCH` | `/api/admin/users/:id/deactivate` | Admin | Deactivate user |
| `PATCH` | `/api/admin/users/:id/activate` | Admin | Activate user |
| `DELETE` | `/api/admin/users/:id` | Admin | Delete user |

---

## 🧪 Testing the System

```bash
# Signup
curl -X POST http://localhost:3000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"name":"Ali Khan","email":"ali@example.com","password":"Test1234","confirmPassword":"Test1234"}'

# Login — save token
TOKEN=$(curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ali@example.com","password":"Test1234"}' | jq -r '.token')

# Get my profile
curl http://localhost:3000/api/auth/me -H "Authorization: Bearer $TOKEN"

# Update profile
curl -X PATCH http://localhost:3000/api/auth/update-me \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name":"Ali Ahmed"}'

# Change password → old token invalidated
curl -X PATCH http://localhost:3000/api/auth/update-password \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"currentPassword":"Test1234","newPassword":"NewPass456","confirmPassword":"NewPass456"}'

# Test account lockout (5 wrong attempts)
for i in {1..6}; do
  curl -X POST http://localhost:3000/api/auth/login \
    -H "Content-Type: application/json" \
    -d '{"email":"ali@example.com","password":"wrongPassword"}'
done

# Test NoSQL injection (should be blocked by mongoSanitize)
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":{"$gt":""},"password":"anything"}'
```

---

## 💡 What You Built

- **Secure registration and login** with bcrypt + JWT
- **httpOnly cookie** auth + Bearer token (supports both)
- **Account lockout** after 5 failed login attempts
- **Password change invalidates** old JWT tokens
- **Role-based access control** (user/editor/admin)
- **Admin user management** panel
- **Protection against**: NoSQL injection, XSS, brute force, weak passwords
- **Production-ready error handling** with no leaked details

---

## 🎉 Phase 4 Complete!

You've built the **full backend stack**:

| Week | Topic | Project |
|------|-------|---------|
| 13 | Node.js & Express.js | REST API To-Do |
| 14 | RESTful API Design | Blog API with CRUD |
| 15 | MongoDB & Mongoose | Database-Driven Notes App |
| **16** | **Authentication & Security** | **Secure User Auth System** |

**Next up**: Phase 5 — Full Stack Integration (React + Express = Complete Web Apps) 🚀

---

[← Day 5](day-05.md) | [Back to Week 16](README.md) | [Next Phase →](../week-17/README.md)
