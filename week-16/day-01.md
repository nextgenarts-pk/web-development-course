# 📅 Week 16 — Day 1: User Model & Password Hashing 🔑

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand **why** we hash passwords
- Set up **bcrypt** for secure password hashing
- Build a **User model** with Mongoose
- Create **registration** with validation

---

## ⚠️ Why Hash Passwords?

```
❌ NEVER store passwords as plain text!

Database breach → All passwords exposed → Users reuse passwords → Multiple accounts compromised
```

### How Hashing Works

| Concept | Description |
|---------|-------------|
| **Hash** | One-way function: password → fixed-length string (irreversible) |
| **Salt** | Random data added before hashing (prevents rainbow table attacks) |
| **bcrypt** | Adaptive hashing algorithm — intentionally slow, salt built-in |
| **Rounds** | Work factor — higher = slower = more secure (12 recommended) |

```
password123  →  bcrypt(12 rounds)  →  $2b$12$LJ3m4ys3Gl...X7e9H2K
password123  →  bcrypt(12 rounds)  →  $2b$12$9Kp2wR5Tq...M8n3J6Y  (different salt = different hash!)
```

---

## 📦 Setup

```bash
mkdir auth-app && cd auth-app
npm init -y
npm install express mongoose bcrypt dotenv cors helmet morgan
npm install -D nodemon
```

```json
{
  "type": "module",
  "scripts": {
    "dev": "nodemon index.js"
  }
}
```

---

## 👤 User Model

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
    select: false, // Never return password by default
  },
  role: {
    type: String,
    enum: ['user', 'editor', 'admin'],
    default: 'user',
  },
  isActive: {
    type: Boolean,
    default: true,
  },
}, {
  timestamps: true,
});

// Hash password before saving
userSchema.pre('save', async function(next) {
  // Only hash if password was modified
  if (!this.isModified('password')) return next();
  
  // Hash with 12 salt rounds
  this.password = await bcrypt.hash(this.password, 12);
  next();
});

// Compare passwords — instance method
userSchema.methods.comparePassword = async function(candidatePassword) {
  return bcrypt.compare(candidatePassword, this.password);
};

export default mongoose.model('User', userSchema);
```

### Key Points

1. **`select: false`** — Password is excluded from all queries by default
2. **`pre('save')`** — Hash runs automatically before every save
3. **`isModified('password')`** — Only re-hash when password actually changes
4. **12 salt rounds** — Good balance of security and performance (~250ms)

---

## 🔐 Password Hashing Deep Dive

```js
// Understanding bcrypt
import bcrypt from 'bcrypt';

// Manual hashing (for understanding)
const password = 'mySecurePass123';

// Generate salt, then hash
const salt = await bcrypt.genSalt(12);
console.log('Salt:', salt);
// Salt: $2b$12$LJ3m4ys3GlPyX9rK5Q2wRe

const hash = await bcrypt.hash(password, salt);
console.log('Hash:', hash);
// Hash: $2b$12$LJ3m4ys3GlPyX9rK5Q2wRe.X7e9H2KpM8n3J6YzWq4v1Rt5u0

// Shorthand — generate salt + hash in one step (recommended)
const hash2 = await bcrypt.hash(password, 12);

// Comparing
const isMatch = await bcrypt.compare('mySecurePass123', hash);
console.log(isMatch); // true

const isWrong = await bcrypt.compare('wrongPassword', hash);
console.log(isWrong); // false
```

### Salt Rounds Performance

| Rounds | Time (approx) | Use Case |
|--------|--------------|----------|
| 10 | ~65ms | Development / testing |
| 12 | ~250ms | **Production (recommended)** |
| 14 | ~1s | High-security systems |
| 16 | ~4s | Too slow for most APIs |

---

## 📝 Registration Endpoint

```js
// src/controllers/authController.js
import User from '../models/User.js';
import { AppError } from '../utils/AppError.js';

export async function register(req, res) {
  const { name, email, password, confirmPassword } = req.body;

  // Confirm passwords match
  if (password !== confirmPassword) {
    throw new AppError('Passwords do not match', 400);
  }

  // Create user (password hashed by pre-save hook)
  const user = await User.create({ name, email, password });

  // Return user WITHOUT password
  res.status(201).json({
    status: 'success',
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
// src/routes/authRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { register } from '../controllers/authController.js';

const router = Router();
router.post('/register', asyncHandler(register));
export default router;
```

---

## 🧪 Testing Registration

```bash
# Register a new user
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "Ali Khan", "email": "ali@example.com", "password": "securePass123", "confirmPassword": "securePass123"}'

# Response:
# {
#   "status": "success",
#   "data": {
#     "id": "...",
#     "name": "Ali Khan",
#     "email": "ali@example.com",
#     "role": "user"
#   }
# }

# Try duplicate email
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name": "Ali Again", "email": "ali@example.com", "password": "anotherPass123", "confirmPassword": "anotherPass123"}'

# Response: 409 — Duplicate value for email
```

---

## 🏋️ Hands-On Exercise

1. Add a **phone** field to the User model (optional, validated with regex)
2. Add a **passwordChangedAt** field that updates whenever the password changes
3. Create a `GET /api/auth/users` endpoint that returns all users (name, email, role only)

---

## 📚 Homework

1. Research: What are **rainbow table attacks** and how do salts prevent them?
2. Experiment: Try different salt rounds (8, 10, 12, 14) and measure the time each takes
3. Add a **username** field (unique, lowercase, alphanumeric only, 3-20 chars)

---

## 💡 Pro Tips

- **Never** log or return passwords — even hashed ones
- **`select: false`** is your best friend for sensitive fields
- Use **`isModified()`** to avoid re-hashing on every save
- Store **12 salt rounds** in an environment variable for easy tuning
- Add **rate limiting** on registration to prevent abuse (covered Day 5)

---

[← Back to Week 16](README.md) | [Day 2 →](day-02.md)
