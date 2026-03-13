# 📅 Week 15 — Day 3: CRUD Operations with Mongoose 🔄

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Perform all CRUD operations with Mongoose
- Use query operators for filtering
- Handle Mongoose validation errors properly
- Integrate Mongoose with Express controllers
- Build a complete Notes API with database persistence

---

## 📖 Lesson Content

### 1. CREATE — Saving Documents

```js
import Note from '../models/Note.js';

// Method 1: new + save
const note = new Note({
  title: 'My First Note',
  content: 'Learning Mongoose is fun!',
  category: 'study',
  author: 'Ali Khan',
});
await note.save();  // Triggers validation + pre-save hooks

// Method 2: Model.create() — shorthand
const note = await Note.create({
  title: 'Quick Note',
  content: 'Created with Model.create()',
  category: 'ideas',
  author: 'Sara Ahmed',
});

// Method 3: Create many
const notes = await Note.insertMany([
  { title: 'Note 1', content: 'Content 1', author: 'Ali' },
  { title: 'Note 2', content: 'Content 2', author: 'Sara' },
]);
```

### 2. READ — Finding Documents

```js
// Find all
const allNotes = await Note.find();

// Find with filter
const workNotes = await Note.find({ category: 'work' });

// Find one document
const note = await Note.findById('65a1b2c3d4e5f6789012abcd');
const note = await Note.findOne({ title: 'My First Note' });

// Select specific fields (projection)
const titles = await Note.find().select('title category createdAt');
const minimal = await Note.find().select('-content -__v');  // Exclude fields

// Sort
const latest = await Note.find().sort({ createdAt: -1 });   // Newest first
const sorted = await Note.find().sort('-createdAt title');   // String syntax

// Limit & Skip (pagination)
const page1 = await Note.find().sort({ createdAt: -1 }).skip(0).limit(10);
const page2 = await Note.find().sort({ createdAt: -1 }).skip(10).limit(10);

// Count
const total = await Note.countDocuments();
const workCount = await Note.countDocuments({ category: 'work' });

// Check existence
const exists = await Note.exists({ title: 'My Note' });
```

### 3. Query Operators

```js
// Comparison
const recent = await Note.find({ createdAt: { $gte: new Date('2024-01-01') } });
const short = await Note.find({ wordCount: { $lt: 100 } });

// Logical
const filtered = await Note.find({
  $or: [
    { category: 'work' },
    { isPinned: true },
  ],
});

// Array operations
const tagged = await Note.find({ tags: 'javascript' });           // Contains value
const multiTag = await Note.find({ tags: { $all: ['js', 'node'] } }); // Contains all
const fewTags = await Note.find({ tags: { $size: 3 } });          // Exact array length

// Regex search
const matches = await Note.find({
  title: { $regex: 'express', $options: 'i' },  // Case-insensitive
});

// Exists
const withTags = await Note.find({ tags: { $exists: true, $ne: [] } });

// Not equal
const notPersonal = await Note.find({ category: { $ne: 'personal' } });
```

### 4. UPDATE — Modifying Documents

```js
// Method 1: findById + save (triggers validation & hooks)
const note = await Note.findById(id);
if (!note) throw new AppError('Note not found', 404);
note.title = 'Updated Title';
note.category = 'work';
await note.save();  // Validates and saves

// Method 2: findByIdAndUpdate (one database call)
const updated = await Note.findByIdAndUpdate(
  id,
  { title: 'Updated', category: 'work' },
  {
    new: true,              // Return updated document (not original)
    runValidators: true,    // Run schema validation on update
  }
);

// Method 3: findOneAndUpdate
const updated = await Note.findOneAndUpdate(
  { title: 'Old Title' },
  { $set: { title: 'New Title' } },
  { new: true, runValidators: true }
);

// Update operators
await Note.findByIdAndUpdate(id, {
  $set: { title: 'New Title' },          // Set field
  $push: { tags: 'important' },          // Add to array
  $pull: { tags: 'outdated' },           // Remove from array
  $addToSet: { tags: 'unique-tag' },     // Add only if not exists
  $inc: { views: 1 },                    // Increment number
});

// Update many
await Note.updateMany(
  { category: 'work' },
  { $set: { isPinned: true } }
);
```

> **Important**: Always use `{ runValidators: true }` with `findByIdAndUpdate` — otherwise schema validation is skipped!

### 5. DELETE — Removing Documents

```js
// Delete one by ID
const deleted = await Note.findByIdAndDelete(id);
if (!deleted) throw new AppError('Note not found', 404);

// Delete one by filter
await Note.findOneAndDelete({ title: 'Old Note' });

// Delete many
const result = await Note.deleteMany({ category: 'other' });
console.log(`Deleted ${result.deletedCount} notes`);
```

### 6. Handling Validation Errors

```js
// middleware/errorHandler.js
export function errorHandler(err, req, res, next) {
  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const errors = Object.values(err.errors).map(e => e.message);
    return res.status(400).json({
      status: 'error',
      message: 'Validation failed',
      errors,
    });
  }

  // Mongoose duplicate key error
  if (err.code === 11000) {
    const field = Object.keys(err.keyPattern)[0];
    return res.status(409).json({
      status: 'error',
      message: `Duplicate value for ${field}`,
    });
  }

  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    return res.status(400).json({
      status: 'error',
      message: `Invalid ${err.path}: ${err.value}`,
    });
  }

  // Default error
  const statusCode = err.statusCode || 500;
  res.status(statusCode).json({
    status: 'error',
    message: statusCode === 500 ? 'Internal Server Error' : err.message,
  });
}
```

### 7. Express Controller with Mongoose

```js
// controllers/noteController.js
import Note from '../models/Note.js';
import { AppError } from '../utils/AppError.js';

// GET /api/notes
export async function getAllNotes(req, res) {
  // Build query
  const queryObj = {};

  if (req.query.category) queryObj.category = req.query.category;
  if (req.query.isPinned) queryObj.isPinned = req.query.isPinned === 'true';
  if (req.query.author) queryObj.author = req.query.author;

  // Search
  if (req.query.search) {
    queryObj.$or = [
      { title: { $regex: req.query.search, $options: 'i' } },
      { content: { $regex: req.query.search, $options: 'i' } },
    ];
  }

  // Execute query with sort & pagination
  const page = Math.max(1, parseInt(req.query.page) || 1);
  const limit = Math.min(100, Math.max(1, parseInt(req.query.limit) || 10));
  const skip = (page - 1) * limit;
  const sortBy = req.query.sortBy || '-createdAt';

  const [notes, total] = await Promise.all([
    Note.find(queryObj).sort(sortBy).skip(skip).limit(limit).select('-__v'),
    Note.countDocuments(queryObj),
  ]);

  res.json({
    status: 'success',
    count: notes.length,
    total,
    page,
    totalPages: Math.ceil(total / limit),
    data: notes,
  });
}

// GET /api/notes/:id
export async function getNoteById(req, res) {
  const note = await Note.findById(req.params.id).select('-__v');
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', data: note });
}

// POST /api/notes
export async function createNote(req, res) {
  const note = await Note.create(req.body);
  res.status(201).json({ status: 'success', data: note });
}

// PATCH /api/notes/:id
export async function updateNote(req, res) {
  const note = await Note.findByIdAndUpdate(
    req.params.id,
    req.body,
    { new: true, runValidators: true }
  );
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', data: note });
}

// DELETE /api/notes/:id
export async function deleteNote(req, res) {
  const note = await Note.findByIdAndDelete(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', message: `Note "${note.title}" deleted` });
}

// PATCH /api/notes/:id/pin
export async function togglePin(req, res) {
  const note = await Note.findById(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  note.isPinned = !note.isPinned;
  await note.save();
  res.json({ status: 'success', data: note });
}

// GET /api/notes/stats
export async function getNoteStats(req, res) {
  const stats = await Note.getStats();
  res.json({ status: 'success', data: stats });
}
```

### 8. Routes

```js
// routes/noteRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/noteController.js';

const router = Router();

router.get('/stats', asyncHandler(ctrl.getNoteStats));

router.route('/')
  .get(asyncHandler(ctrl.getAllNotes))
  .post(asyncHandler(ctrl.createNote));

router.route('/:id')
  .get(asyncHandler(ctrl.getNoteById))
  .patch(asyncHandler(ctrl.updateNote))
  .delete(asyncHandler(ctrl.deleteNote));

router.patch('/:id/pin', asyncHandler(ctrl.togglePin));

export default router;
```

### 9. Method Comparison

| Task | Method | Validation | Hooks | Returns |
|------|--------|-----------|-------|---------|
| Create | `Model.create()` | ✅ | ✅ | New doc |
| Create | `new Model() + save()` | ✅ | ✅ | New doc |
| Read | `Model.find()` | — | ✅ (pre find) | Array |
| Read | `Model.findById()` | — | ✅ | Doc or null |
| Update | `findById + save()` | ✅ | ✅ | Updated doc |
| Update | `findByIdAndUpdate()` | Only with `runValidators` | ❌ | Updated doc |
| Delete | `findByIdAndDelete()` | — | ✅ (pre/post) | Deleted doc |

---

## 💪 Hands-On Exercise

### Build the Notes API CRUD

1. Connect your Express app to MongoDB with Mongoose
2. Create the `Note` model from Day 2
3. Implement all 6 controller functions (getAllNotes, getNoteById, createNote, updateNote, deleteNote, togglePin)
4. Set up routes in `noteRoutes.js`
5. Update the error handler to handle Mongoose errors (ValidationError, CastError, 11000)
6. Test all CRUD operations:
   - Create 5 notes with different categories
   - List all notes, filter by category
   - Search by title keyword
   - Update a note's title
   - Toggle pin status
   - Delete a note

---

## 📝 Homework

1. Add a `GET /api/notes/stats` endpoint using the static method from Day 2
2. Implement pagination — verify `page=1&limit=3` returns correct results
3. Add sorting: `?sortBy=title` (ascending) and `?sortBy=-title` (descending)
4. Handle all Mongoose error types in your error handler (validation, cast, duplicate key)
5. Test edge cases: invalid ObjectId, missing required fields, duplicate unique fields

---

## 💡 Pro Tips

- Use `Model.create()` for simple creation — it's cleaner than `new + save()`
- Use `findById + save()` when you need validation and hooks on updates
- Always pass `{ new: true, runValidators: true }` to `findByIdAndUpdate`
- Use `Promise.all()` to run `find()` and `countDocuments()` in parallel for efficient pagination
- The `select('-__v')` removes the version key from responses — cleaner JSON
- Mongoose automatically pluralizes and lowercases model names: `Note` → `notes` collection

---

[← Day 2](day-02.md) | [Back to Week 15](README.md) | [Day 4 →](day-04.md)
