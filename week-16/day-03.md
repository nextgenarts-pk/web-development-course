# 📅 Week 16 — Day 3: Protected Routes & Auth Middleware 🛡️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Build **authentication middleware** to protect routes
- Extract and verify JWT from request headers
- Access **current user** in protected routes
- Implement **resource ownership** checks

---

## 🛡️ The Protect Middleware

The core of route protection — runs before any protected controller:

```js
// src/middleware/auth.js
import { verifyToken } from '../utils/jwt.js';
import User from '../models/User.js';
import { AppError } from '../utils/AppError.js';

export async function protect(req, res, next) {
  // 1. Get token from header
  let token;
  if (req.headers.authorization?.startsWith('Bearer')) {
    token = req.headers.authorization.split(' ')[1];
  }

  if (!token) {
    throw new AppError('Not logged in. Please provide a token.', 401);
  }

  // 2. Verify token
  let decoded;
  try {
    decoded = verifyToken(token);
  } catch (err) {
    if (err.name === 'TokenExpiredError') {
      throw new AppError('Token expired. Please log in again.', 401);
    }
    throw new AppError('Invalid token. Please log in again.', 401);
  }

  // 3. Check if user still exists
  const user = await User.findById(decoded.id);
  if (!user) {
    throw new AppError('User belonging to this token no longer exists.', 401);
  }

  // 4. Check if user is active
  if (!user.isActive) {
    throw new AppError('Your account has been deactivated.', 403);
  }

  // 5. Check if password changed after token was issued
  if (user.passwordChangedAt) {
    const changedTimestamp = Math.floor(user.passwordChangedAt.getTime() / 1000);
    if (decoded.iat < changedTimestamp) {
      throw new AppError('Password recently changed. Please log in again.', 401);
    }
  }

  // 6. Grant access — attach user to request
  req.user = user;
  next();
}
```

### What This Middleware Does

| Step | Purpose |
|------|---------|
| Extract token | Get JWT from `Authorization: Bearer <token>` header |
| Verify token | Ensure token is valid and not expired |
| User exists | User might have been deleted after token was issued |
| Account active | Deactivated users can't access routes |
| Password changed | Invalidate old tokens after password change |
| Attach user | Make `req.user` available to all subsequent middleware/controllers |

---

## 🔒 Protecting Routes

```js
// src/routes/noteRoutes.js
import { Router } from 'express';
import { protect } from '../middleware/auth.js';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/noteController.js';

const router = Router();

// All routes below require authentication
router.use(protect);

router.route('/')
  .get(asyncHandler(ctrl.getAllNotes))
  .post(asyncHandler(ctrl.createNote));

router.route('/:id')
  .get(asyncHandler(ctrl.getNoteById))
  .patch(asyncHandler(ctrl.updateNote))
  .delete(asyncHandler(ctrl.deleteNote));

export default router;
```

```js
// Or protect individual routes
router.get('/public', asyncHandler(ctrl.getPublicNotes));           // No auth
router.post('/', protect, asyncHandler(ctrl.createNote));            // Auth required
router.delete('/:id', protect, asyncHandler(ctrl.deleteNote));       // Auth required
```

---

## 👤 Using the Current User

Now every protected controller has access to `req.user`:

```js
// src/controllers/noteController.js

// Create note — automatically set author to logged-in user
export async function createNote(req, res) {
  const note = await Note.create({
    ...req.body,
    author: req.user._id,  // From protect middleware
  });
  res.status(201).json({ status: 'success', data: note });
}

// Get my notes only
export async function getMyNotes(req, res) {
  const notes = await Note.find({ author: req.user._id })
    .populate('tags', 'name color')
    .sort('-isPinned -createdAt');
  res.json({ status: 'success', count: notes.length, data: notes });
}
```

---

## 🔐 Resource Ownership

Ensure users can only modify **their own** resources:

```js
// src/middleware/auth.js

export function isOwner(Model) {
  return async (req, res, next) => {
    const resource = await Model.findById(req.params.id);

    if (!resource) {
      throw new AppError('Resource not found', 404);
    }

    // Admin can access anything
    if (req.user.role === 'admin') {
      req.resource = resource;
      return next();
    }

    // Check ownership
    if (resource.author.toString() !== req.user._id.toString()) {
      throw new AppError('You can only modify your own resources', 403);
    }

    req.resource = resource;
    next();
  };
}
```

```js
// Usage in routes
import Note from '../models/Note.js';

router.patch('/:id', protect, asyncHandler(isOwner(Note)), asyncHandler(ctrl.updateNote));
router.delete('/:id', protect, asyncHandler(isOwner(Note)), asyncHandler(ctrl.deleteNote));
```

---

## 🔄 Update User Model

Add `passwordChangedAt` to track password changes:

```js
// Add to User model schema
passwordChangedAt: {
  type: Date,
  select: false,
},

// Update the pre-save hook
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 12);

  // Set passwordChangedAt (skip on new documents)
  if (!this.isNew) {
    this.passwordChangedAt = new Date();
  }

  next();
});
```

---

## 🔄 Update Author Reference

Change the Note model's `author` from a string to a User reference:

```js
// In Note model
author: {
  type: mongoose.Schema.Types.ObjectId,
  ref: 'User',
  required: [true, 'Author is required'],
},
```

Now you can populate author info:

```js
const notes = await Note.find()
  .populate('author', 'name email')
  .populate('tags', 'name color');
```

---

## ✏️ Profile Endpoints

```js
// src/controllers/authController.js

// GET /api/auth/me
export async function getMe(req, res) {
  const user = await User.findById(req.user.id);
  res.json({ status: 'success', data: user });
}

// PATCH /api/auth/update-me
export async function updateMe(req, res) {
  // Don't allow password updates through this route
  if (req.body.password) {
    throw new AppError('Use /update-password to change password', 400);
  }

  // Only allow specific fields
  const { name, email } = req.body;
  const user = await User.findByIdAndUpdate(
    req.user.id,
    { name, email },
    { new: true, runValidators: true }
  );

  res.json({ status: 'success', data: user });
}

// PATCH /api/auth/update-password
export async function updatePassword(req, res) {
  const { currentPassword, newPassword, confirmPassword } = req.body;

  if (newPassword !== confirmPassword) {
    throw new AppError('Passwords do not match', 400);
  }

  // Get user with password
  const user = await User.findById(req.user.id).select('+password');

  // Verify current password
  if (!(await user.comparePassword(currentPassword))) {
    throw new AppError('Current password is incorrect', 401);
  }

  // Update password (pre-save hook will hash it + set passwordChangedAt)
  user.password = newPassword;
  await user.save();

  // Issue new token (old ones invalidated by passwordChangedAt)
  sendTokenResponse(user, 200, res);
}
```

```js
// Updated auth routes
router.get('/me', protect, asyncHandler(getMe));
router.patch('/update-me', protect, asyncHandler(updateMe));
router.patch('/update-password', protect, asyncHandler(updatePassword));
```

---

## 🧪 Testing Protected Routes

```bash
# Login and save token
TOKEN=$(curl -s -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"ali@example.com","password":"securePass123"}' | jq -r '.token')

# Access protected route WITH token
curl http://localhost:3000/api/notes \
  -H "Authorization: Bearer $TOKEN"

# Access protected route WITHOUT token
curl http://localhost:3000/api/notes
# → 401: "Not logged in. Please provide a token."

# Get my profile
curl http://localhost:3000/api/auth/me \
  -H "Authorization: Bearer $TOKEN"

# Update profile
curl -X PATCH http://localhost:3000/api/auth/update-me \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "Ali Ahmed Khan"}'

# Change password
curl -X PATCH http://localhost:3000/api/auth/update-password \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"currentPassword":"securePass123","newPassword":"newSecure456","confirmPassword":"newSecure456"}'
```

---

## 🏋️ Hands-On Exercise

1. Add `protect` middleware to all note routes
2. Automatically set `author: req.user._id` when creating notes
3. Add a `GET /api/notes/mine` route that returns only the current user's notes
4. Implement the `isOwner` middleware on update and delete routes

---

## 📚 Homework

1. Add a `DELETE /api/auth/delete-me` endpoint that **deactivates** (not deletes) the account
2. Test: Login → Change password → Use old token → Verify it fails
3. Research: What are **httpOnly** cookies and why are they more secure than `localStorage` for tokens?

---

## 💡 Pro Tips

- **`router.use(protect)`** protects all routes defined after it
- **`req.user`** is your gateway to the current user in any controller
- **Ownership checks** prevent horizontal privilege escalation
- **Password change** should invalidate all existing tokens
- **Never trust client data** for author/role — always use `req.user`

---

[← Day 2](day-02.md) | [Back to Week 16](README.md) | [Day 4 →](day-04.md)
