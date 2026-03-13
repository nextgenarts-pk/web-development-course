# 📅 Week 15 — Day 5: Data Relationships & Schema Design 🔗

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand embedding vs referencing in MongoDB
- Model one-to-many and many-to-many relationships
- Use Mongoose `populate()` to join referenced documents
- Design schemas for the Notes App (Notes, Folders, Tags)
- Learn when to embed and when to reference

---

## 📖 Lesson Content

### 1. Two Approaches to Relationships

MongoDB gives you two ways to model relationships:

| Approach | How | Best For |
|----------|-----|----------|
| **Embedding** | Store related data inside the document | Small, tightly-coupled data that's always read together |
| **Referencing** | Store ObjectId and look up separately | Large or independently accessed data |

### 2. Embedding (Denormalization)

Store related data directly inside the parent document:

```js
// Embedded comment inside a post
{
  _id: ObjectId("..."),
  title: "My Blog Post",
  content: "...",
  comments: [
    {
      author: "Ali",
      text: "Great post!",
      createdAt: "2024-01-15"
    },
    {
      author: "Sara",
      text: "Very helpful.",
      createdAt: "2024-01-16"
    }
  ]
}
```

**Pros:** One query gets everything. Fast reads. Atomic updates.
**Cons:** Document size limit (16MB). Difficult to query embedded docs independently.

### 3. Referencing (Normalization)

Store related data in separate collections with ObjectId references:

```js
// Post document
{
  _id: ObjectId("post123"),
  title: "My Blog Post",
  content: "...",
  author: ObjectId("user456")  // Reference to User
}

// User document (separate collection)
{
  _id: ObjectId("user456"),
  name: "Ali Khan",
  email: "ali@example.com"
}
```

**Pros:** No size limits. Data is reusable. Easy to update independently.
**Cons:** Requires multiple queries or `populate()`.

### 4. When to Embed vs Reference

| Embed When... | Reference When... |
|---------------|-------------------|
| Data is always read together | Data is accessed independently |
| Related data is small | Related data is large or growing |
| Data changes infrequently | Data changes frequently |
| One-to-few relationship | One-to-many or many-to-many |
| You need atomic operations | You need to reuse data across documents |

**Rules of thumb:**
- **Embed**: comments (few per post), addresses (one per user), settings
- **Reference**: authors (shared), categories (reused), users (independent)

### 5. Mongoose References with ObjectId

```js
// models/Folder.js
import mongoose from 'mongoose';

const folderSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Folder name is required'],
    trim: true,
    unique: true,
  },
  description: { type: String, default: '' },
  color: { type: String, default: '#6366f1' },
  icon: { type: String, default: '📁' },
}, { timestamps: true, toJSON: { virtuals: true } });

// Virtual: get notes count for this folder
folderSchema.virtual('notes', {
  ref: 'Note',           // The model to use
  localField: '_id',     // Find notes where...
  foreignField: 'folder', // ...folder field matches this _id
});

const Folder = mongoose.model('Folder', folderSchema);
export default Folder;
```

```js
// models/Note.js — add folder reference
const noteSchema = new mongoose.Schema({
  title: { type: String, required: true, trim: true },
  content: { type: String, required: true },
  category: {
    type: String,
    enum: ['personal', 'work', 'study', 'ideas', 'other'],
    default: 'personal',
  },
  folder: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Folder',     // Reference to Folder model
    default: null,
  },
  tags: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Tag',        // Reference to Tag model
  }],
  author: { type: String, required: true, trim: true },
  isPinned: { type: Boolean, default: false },
}, { timestamps: true, toJSON: { virtuals: true } });
```

### 6. Populate — Joining Referenced Data

`populate()` replaces ObjectId references with actual documents:

```js
// Get a note with its folder details
const note = await Note
  .findById(id)
  .populate('folder', 'name color icon')   // Only include name, color, icon
  .populate('tags', 'name color');          // Populate tags too

// Response:
{
  _id: "...",
  title: "My Note",
  folder: {
    _id: "...",
    name: "Work",
    color: "#6366f1",
    icon: "💼"
  },
  tags: [
    { _id: "...", name: "important", color: "#ef4444" },
    { _id: "...", name: "urgent", color: "#f59e0b" }
  ]
}
```

Without `populate()`, you'd just see:
```js
{ folder: "65a1b2c3d4e5f6789012abcd", tags: ["65a...", "65b..."] }
```

### 7. Advanced Populate

```js
// Populate with filtering
const notes = await Note
  .find({ category: 'work' })
  .populate({
    path: 'folder',
    select: 'name color',
    // Only populate if folder matches condition
    match: { name: { $ne: 'Archive' } },
  })
  .populate('tags', 'name');

// Populate in controller
export async function getNoteById(req, res) {
  const note = await Note.findById(req.params.id)
    .populate('folder', 'name color icon')
    .populate('tags', 'name color')
    .select('-__v');

  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', data: note });
}

// Populate virtual field (get all notes in a folder)
const folder = await Folder.findById(id).populate('notes', 'title category');
```

### 8. Tag Model — Many-to-Many Relationship

A note can have many tags. A tag can belong to many notes.

```js
// models/Tag.js
import mongoose from 'mongoose';

const tagSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Tag name is required'],
    trim: true,
    unique: true,
    lowercase: true,
  },
  color: { type: String, default: '#6b7280' },
}, { timestamps: true, toJSON: { virtuals: true } });

// Virtual: count notes with this tag
tagSchema.virtual('noteCount', {
  ref: 'Note',
  localField: '_id',
  foreignField: 'tags',
  count: true,
});

const Tag = mongoose.model('Tag', tagSchema);
export default Tag;
```

### 9. Working with Tags in Controllers

```js
// controllers/noteController.js
export async function addTagsToNote(req, res) {
  const note = await Note.findById(req.params.id);
  if (!note) throw new AppError('Note not found', 404);

  const { tagIds } = req.body;

  // Add tags without duplicates
  for (const tagId of tagIds) {
    if (!note.tags.includes(tagId)) {
      note.tags.push(tagId);
    }
  }

  await note.save();
  await note.populate('tags', 'name color');

  res.json({ status: 'success', data: note });
}

export async function removeTagFromNote(req, res) {
  const note = await Note.findById(req.params.noteId);
  if (!note) throw new AppError('Note not found', 404);

  note.tags = note.tags.filter(t => t.toString() !== req.params.tagId);
  await note.save();

  res.json({ status: 'success', data: note });
}
```

```js
// controllers/tagController.js
import Tag from '../models/Tag.js';
import Note from '../models/Note.js';

export async function getAllTags(req, res) {
  const tags = await Tag.find()
    .populate('noteCount')    // Include virtual count
    .sort('name');

  res.json({ status: 'success', data: tags });
}

export async function createTag(req, res) {
  const tag = await Tag.create(req.body);
  res.status(201).json({ status: 'success', data: tag });
}

export async function deleteTag(req, res) {
  const tag = await Tag.findByIdAndDelete(req.params.id);
  if (!tag) throw new AppError('Tag not found', 404);

  // Remove this tag from all notes
  await Note.updateMany(
    { tags: tag._id },
    { $pull: { tags: tag._id } }
  );

  res.json({ status: 'success', message: `Tag "${tag.name}" deleted` });
}
```

### 10. Folder Controller

```js
// controllers/folderController.js
import Folder from '../models/Folder.js';
import Note from '../models/Note.js';
import { AppError } from '../utils/AppError.js';

export async function getAllFolders(req, res) {
  const folders = await Folder.find().sort('name');

  // Get note counts for each folder
  const foldersWithCounts = await Promise.all(
    folders.map(async (folder) => {
      const noteCount = await Note.countDocuments({ folder: folder._id });
      return { ...folder.toObject(), noteCount };
    })
  );

  res.json({ status: 'success', data: foldersWithCounts });
}

export async function createFolder(req, res) {
  const folder = await Folder.create(req.body);
  res.status(201).json({ status: 'success', data: folder });
}

export async function getFolderNotes(req, res) {
  const folder = await Folder.findById(req.params.id);
  if (!folder) throw new AppError('Folder not found', 404);

  const notes = await Note.find({ folder: folder._id })
    .populate('tags', 'name color')
    .sort('-createdAt')
    .select('-__v');

  res.json({
    status: 'success',
    folder: { name: folder.name, color: folder.color },
    count: notes.length,
    data: notes,
  });
}

export async function deleteFolder(req, res) {
  const folder = await Folder.findByIdAndDelete(req.params.id);
  if (!folder) throw new AppError('Folder not found', 404);

  // Remove folder reference from all notes (don't delete notes)
  await Note.updateMany(
    { folder: folder._id },
    { $set: { folder: null } }
  );

  res.json({ status: 'success', message: `Folder "${folder.name}" deleted` });
}
```

### 11. Routes for All Resources

```js
// routes/index.js
import { Router } from 'express';
import noteRoutes from './noteRoutes.js';
import folderRoutes from './folderRoutes.js';
import tagRoutes from './tagRoutes.js';

const router = Router();
router.use('/notes', noteRoutes);
router.use('/folders', folderRoutes);
router.use('/tags', tagRoutes);

export default router;
```

### 12. Schema Design for the Notes App

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Folder     │     │    Note      │     │    Tag       │
├──────────────┤     ├──────────────┤     ├──────────────┤
│ name         │◄────│ folder (ref) │     │ name         │
│ description  │     │ title        │     │ color        │
│ color        │     │ content      │     └──────────────┘
│ icon         │     │ category     │            ▲
└──────────────┘     │ tags[] (ref) │────────────┘
                     │ author       │     Many-to-Many
                     │ isPinned     │
                     │ color        │
                     └──────────────┘
```

---

## 💪 Hands-On Exercise

### Build the Complete Data Model

1. Create the `Folder` model with virtuals
2. Create the `Tag` model with virtual noteCount
3. Update the `Note` model with `folder` (ObjectId ref) and `tags` (ObjectId array ref)
4. Create Folder CRUD controller and routes
5. Create Tag CRUD controller and routes
6. Implement `populate()` in getNoteById to include folder and tags
7. Test: create folders, tags, notes → assign notes to folders → add tags to notes → verify `populate()` works

---

## 📝 Homework

1. Implement `GET /api/folders/:id/notes` — get all notes in a specific folder
2. Implement `PATCH /api/notes/:id/tags` — add/remove tags from a note
3. When deleting a folder, set all its notes' `folder` field to `null` (don't delete the notes)
4. When deleting a tag, remove it from all notes' `tags` arrays using `$pull`
5. Add a `GET /api/tags` endpoint that includes note counts (using the virtual)

---

## 💡 Pro Tips

- **Embed** when the data is small, always read together, and rarely queried alone (e.g., address)
- **Reference** when data is shared, large, or independently queried (e.g., users, categories)
- Use `populate('field', 'name color')` to select only needed fields — don't populate everything
- Virtual fields with `{ ref, localField, foreignField }` are great for reverse lookups
- When deleting a parent, always clean up references in children (cascade cleanup)
- Don't over-populate — each populate is an additional database query

---

[← Day 4](day-04.md) | [Back to Week 15](README.md) | [Day 6 →](day-06.md)
