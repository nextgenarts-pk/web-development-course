# 📅 Week 16 — Day 4: Role-Based Access Control 👑

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Implement **role-based access control** (RBAC)
- Create **restrict** middleware for role checking
- Build **admin-only** routes
- Handle **resource ownership** with roles

---

## 👑 Understanding RBAC

| Role | Permissions |
|------|------------|
| **user** | CRUD own resources, view public content |
| **editor** | All user permissions + edit any resource |
| **admin** | Full access — manage users, roles, all resources |

```
User Request → protect (auth) → restrictTo (role check) → Controller
```

---

## 🔒 The RestrictTo Middleware

```js
// src/middleware/auth.js (add to existing file)

export function restrictTo(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      throw new AppError('You do not have permission to perform this action', 403);
    }
    next();
  };
}
```

### Usage

```js
import { protect, restrictTo } from '../middleware/auth.js';

// Only admin
router.delete('/users/:id', protect, restrictTo('admin'), asyncHandler(deleteUser));

// Admin or editor
router.patch('/posts/:id', protect, restrictTo('admin', 'editor'), asyncHandler(updatePost));

// Any authenticated user
router.get('/notes', protect, asyncHandler(getAllNotes));
```

---

## 👤 Admin User Management

```js
// src/controllers/adminController.js
import User from '../models/User.js';
import { AppError } from '../utils/AppError.js';

// GET /api/admin/users — List all users
export async function getAllUsers(req, res) {
  const users = await User.find().select('name email role isActive createdAt').sort('-createdAt');
  res.json({ status: 'success', count: users.length, data: users });
}

// GET /api/admin/users/:id — Single user
export async function getUser(req, res) {
  const user = await User.findById(req.params.id).select('name email role isActive createdAt');
  if (!user) throw new AppError('User not found', 404);
  res.json({ status: 'success', data: user });
}

// PATCH /api/admin/users/:id/role — Change user role
export async function updateUserRole(req, res) {
  const { role } = req.body;
  if (!['user', 'editor', 'admin'].includes(role)) {
    throw new AppError('Invalid role', 400);
  }

  // Prevent admin from changing their own role
  if (req.params.id === req.user._id.toString()) {
    throw new AppError('You cannot change your own role', 400);
  }

  const user = await User.findByIdAndUpdate(
    req.params.id,
    { role },
    { new: true, runValidators: true }
  ).select('name email role');

  if (!user) throw new AppError('User not found', 404);
  res.json({ status: 'success', data: user });
}

// PATCH /api/admin/users/:id/deactivate — Deactivate user
export async function deactivateUser(req, res) {
  if (req.params.id === req.user._id.toString()) {
    throw new AppError('You cannot deactivate yourself', 400);
  }

  const user = await User.findByIdAndUpdate(
    req.params.id,
    { isActive: false },
    { new: true }
  ).select('name email role isActive');

  if (!user) throw new AppError('User not found', 404);
  res.json({ status: 'success', message: `User "${user.name}" deactivated`, data: user });
}

// PATCH /api/admin/users/:id/activate — Reactivate user
export async function activateUser(req, res) {
  const user = await User.findByIdAndUpdate(
    req.params.id,
    { isActive: true },
    { new: true }
  ).select('name email role isActive');

  if (!user) throw new AppError('User not found', 404);
  res.json({ status: 'success', message: `User "${user.name}" activated`, data: user });
}

// DELETE /api/admin/users/:id — Permanently delete user
export async function deleteUser(req, res) {
  if (req.params.id === req.user._id.toString()) {
    throw new AppError('You cannot delete yourself', 400);
  }

  const user = await User.findByIdAndDelete(req.params.id);
  if (!user) throw new AppError('User not found', 404);
  res.json({ status: 'success', message: `User "${user.name}" permanently deleted` });
}
```

---

## 🛣️ Admin Routes

```js
// src/routes/adminRoutes.js
import { Router } from 'express';
import { protect, restrictTo } from '../middleware/auth.js';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/adminController.js';

const router = Router();

// All admin routes require authentication + admin role
router.use(protect, restrictTo('admin'));

router.get('/users', asyncHandler(ctrl.getAllUsers));
router.get('/users/:id', asyncHandler(ctrl.getUser));
router.patch('/users/:id/role', asyncHandler(ctrl.updateUserRole));
router.patch('/users/:id/deactivate', asyncHandler(ctrl.deactivateUser));
router.patch('/users/:id/activate', asyncHandler(ctrl.activateUser));
router.delete('/users/:id', asyncHandler(ctrl.deleteUser));

export default router;
```

```js
// Register in app.js
import adminRoutes from './routes/adminRoutes.js';
app.use('/api/admin', adminRoutes);
```

---

## 🔐 Ownership + Roles Combined

A pattern where users can modify their own resources, and admins/editors can modify any:

```js
// src/middleware/auth.js

export function canModify(Model, ownerField = 'author') {
  return async (req, res, next) => {
    const resource = await Model.findById(req.params.id);
    if (!resource) throw new AppError('Resource not found', 404);

    const isOwner = resource[ownerField].toString() === req.user._id.toString();
    const isPrivileged = ['admin', 'editor'].includes(req.user.role);

    if (!isOwner && !isPrivileged) {
      throw new AppError('You do not have permission to modify this resource', 403);
    }

    req.resource = resource;
    next();
  };
}
```

```js
// Routes
import Note from '../models/Note.js';

// User can update/delete own notes, editors/admins can update/delete any
router.patch('/:id', protect, asyncHandler(canModify(Note)), asyncHandler(ctrl.updateNote));
router.delete('/:id', protect, asyncHandler(canModify(Note)), asyncHandler(ctrl.deleteNote));
```

---

## 📊 Complete Route Permissions Map

```js
// Public routes (no auth)
POST   /api/auth/signup          // Anyone
POST   /api/auth/login           // Anyone

// Authenticated routes (any role)
GET    /api/auth/me              // protect
PATCH  /api/auth/update-me       // protect
PATCH  /api/auth/update-password // protect

// Resource routes (authenticated + ownership)
GET    /api/notes                // protect — see own notes
POST   /api/notes                // protect — create own note
PATCH  /api/notes/:id            // protect + canModify — own or admin/editor
DELETE /api/notes/:id            // protect + canModify — own or admin/editor

// Admin-only routes
GET    /api/admin/users          // protect + restrictTo('admin')
PATCH  /api/admin/users/:id/role // protect + restrictTo('admin')
DELETE /api/admin/users/:id      // protect + restrictTo('admin')
```

---

## 🌱 Seeding an Admin User

```js
// scripts/createAdmin.js
import 'dotenv/config';
import mongoose from 'mongoose';
import User from '../src/models/User.js';

async function createAdmin() {
  await mongoose.connect(process.env.MONGODB_URI);

  const admin = await User.create({
    name: 'Admin',
    email: 'admin@example.com',
    password: 'adminSecure123',
    role: 'admin',
  });

  console.log(`Admin created: ${admin.email}`);
  await mongoose.disconnect();
}

createAdmin();
```

```bash
node scripts/createAdmin.js
```

---

## 🧪 Testing RBAC

```bash
# Login as admin
ADMIN_TOKEN=$(curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@example.com","password":"adminSecure123"}' | jq -r '.token')

# Login as regular user
USER_TOKEN=$(curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ali@example.com","password":"securePass123"}' | jq -r '.token')

# Admin: list all users ✅
curl http://localhost:3000/api/admin/users \
  -H "Authorization: Bearer $ADMIN_TOKEN"

# User: try admin route ❌
curl http://localhost:3000/api/admin/users \
  -H "Authorization: Bearer $USER_TOKEN"
# → 403: "You do not have permission to perform this action"

# Admin: change user role
curl -X PATCH http://localhost:3000/api/admin/users/<USER_ID>/role \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"role": "editor"}'

# Admin: deactivate user
curl -X PATCH http://localhost:3000/api/admin/users/<USER_ID>/deactivate \
  -H "Authorization: Bearer $ADMIN_TOKEN"
```

---

## 🏋️ Hands-On Exercise

1. Add `protect` and `canModify(Note)` to note update/delete routes
2. Create a seeder script that creates 1 admin + 2 regular users
3. Test that a regular user cannot delete another user's note
4. Test that an admin CAN delete any user's note

---

## 📚 Homework

1. Add an `editor` role — editors can update (but not delete) any note
2. Build a `GET /api/admin/stats` endpoint that returns user count by role
3. Research: What is the **Principle of Least Privilege** and how does it apply to RBAC?

---

## 💡 Pro Tips

- **Middleware order matters**: `protect` must come before `restrictTo`
- **Self-protection**: Prevent admins from deleting/demoting themselves
- **Use `router.use()`** to apply auth to all routes in a file
- **`canModify`** is more flexible than separate "isOwner" checks
- Keep **role definitions centralized** — avoid scattering role strings everywhere

---

[← Day 3](day-03.md) | [Back to Week 16](README.md) | [Day 5 →](day-05.md)
