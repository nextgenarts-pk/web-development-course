# 📅 Week 15 — Day 6: Project Day — Database-Driven Notes App 📝

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goal

Build a **complete Database-Driven Notes App** with MongoDB and Mongoose — featuring notes, folders, tags, full-text search, aggregation statistics, and a production-ready API.

---

## 🏗️ Project Overview

| Feature | Description |
|---------|-------------|
| **Application** | Notes API with Folders & Tags |
| **Database** | MongoDB with Mongoose ODM |
| **Resources** | Notes, Folders, Tags |
| **Features** | CRUD, relationships, search, filtering, pagination, aggregation stats |
| **Architecture** | MVC with Mongoose models |

---

## 📁 Project Structure

```
notes-app/
├── src/
│   ├── config/
│   │   └── database.js
│   ├── controllers/
│   │   ├── noteController.js
│   │   ├── folderController.js
│   │   └── tagController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   └── validate.js
│   ├── models/
│   │   ├── Note.js
│   │   ├── Folder.js
│   │   └── Tag.js
│   ├── routes/
│   │   ├── index.js
│   │   ├── noteRoutes.js
│   │   ├── folderRoutes.js
│   │   └── tagRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── asyncHandler.js
│   │   └── APIFeatures.js
│   └── app.js
├── .env
├── .gitignore
├── index.js
└── package.json
```

---

## 🔧 Step 1: Setup

```bash
mkdir notes-app && cd notes-app
npm init -y
npm install express mongoose cors morgan helmet dotenv compression
npm install -D nodemon
```

```json
{
  "name": "notes-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  }
}
```

```env
# .env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/notes_app?retryWrites=true&w=majority
```

---

## 🔧 Step 2: Database Connection

```js
// src/config/database.js
import mongoose from 'mongoose';

export async function connectDB() {
  try {
    const conn = await mongoose.connect(process.env.MONGODB_URI);
    console.log(`MongoDB connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`MongoDB error: ${error.message}`);
    process.exit(1);
  }
}
```

---

## 🔧 Step 3: Models

```js
// src/models/Folder.js
import mongoose from 'mongoose';

const folderSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Folder name is required'],
    trim: true,
    unique: true,
    maxlength: [50, 'Folder name cannot exceed 50 characters'],
  },
  description: { type: String, default: '', trim: true },
  color: { type: String, default: '#6366f1' },
  icon: { type: String, default: '📁' },
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true },
});

folderSchema.virtual('notes', {
  ref: 'Note',
  localField: '_id',
  foreignField: 'folder',
});

export default mongoose.model('Folder', folderSchema);
```

```js
// src/models/Tag.js
import mongoose from 'mongoose';

const tagSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Tag name is required'],
    trim: true,
    unique: true,
    lowercase: true,
    maxlength: [30, 'Tag name cannot exceed 30 characters'],
  },
  color: { type: String, default: '#6b7280' },
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true },
});

tagSchema.virtual('noteCount', {
  ref: 'Note',
  localField: '_id',
  foreignField: 'tags',
  count: true,
});

export default mongoose.model('Tag', tagSchema);
```

```js
// src/models/Note.js
import mongoose from 'mongoose';

const noteSchema = new mongoose.Schema({
  title: {
    type: String,
    required: [true, 'Title is required'],
    trim: true,
    minlength: [2, 'Title must be at least 2 characters'],
    maxlength: [200, 'Title cannot exceed 200 characters'],
  },
  content: {
    type: String,
    required: [true, 'Content is required'],
    trim: true,
  },
  category: {
    type: String,
    enum: ['personal', 'work', 'study', 'ideas', 'other'],
    default: 'personal',
  },
  folder: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Folder',
    default: null,
  },
  tags: [{
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Tag',
  }],
  author: {
    type: String,
    required: [true, 'Author is required'],
    trim: true,
  },
  isPinned: { type: Boolean, default: false },
  isFavorite: { type: Boolean, default: false },
  color: { type: String, default: '#ffffff' },
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true },
});

// Virtuals
noteSchema.virtual('preview').get(function() {
  return this.content.length > 100 ? this.content.substring(0, 100) + '...' : this.content;
});

noteSchema.virtual('wordCount').get(function() {
  return this.content.split(/\s+/).length;
});

noteSchema.virtual('readTime').get(function() {
  return Math.ceil(this.content.split(/\s+/).length / 200);
});

// Indexes
noteSchema.index({ title: 'text', content: 'text' });
noteSchema.index({ category: 1, createdAt: -1 });
noteSchema.index({ folder: 1 });
noteSchema.index({ tags: 1 });
noteSchema.index({ isPinned: -1, createdAt: -1 });

// Statics
noteSchema.statics.getStats = async function() {
  const [totals, byCategory, topTags, recentActivity] = await Promise.all([
    this.aggregate([
      {
        $group: {
          _id: null,
          total: { $sum: 1 },
          pinned: { $sum: { $cond: ['$isPinned', 1, 0] } },
          favorites: { $sum: { $cond: ['$isFavorite', 1, 0] } },
        },
      },
    ]),
    this.aggregate([
      { $group: { _id: '$category', count: { $sum: 1 } } },
      { $sort: { count: -1 } },
    ]),
    this.aggregate([
      { $unwind: '$tags' },
      { $group: { _id: '$tags', count: { $sum: 1 } } },
      { $sort: { count: -1 } },
      { $limit: 10 },
      {
        $lookup: {
          from: 'tags',
          localField: '_id',
          foreignField: '_id',
          as: 'tag',
        },
      },
      { $unwind: '$tag' },
      { $project: { name: '$tag.name', color: '$tag.color', count: 1 } },
    ]),
    this.aggregate([
      {
        $group: {
          _id: { $dateToString: { format: '%Y-%m-%d', date: '$createdAt' } },
          count: { $sum: 1 },
        },
      },
      { $sort: { _id: -1 } },
      { $limit: 7 },
    ]),
  ]);

  return {
    ...(totals[0] || { total: 0, pinned: 0, favorites: 0 }),
    byCategory,
    topTags,
    recentActivity,
  };
};

export default mongoose.model('Note', noteSchema);
```

---

## 🔧 Step 4: Utilities

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
  return (req, res, next) => {
    Promise.resolve(fn(req, res, next)).catch(next);
  };
}

// src/utils/APIFeatures.js
export class APIFeatures {
  constructor(query, queryString) {
    this.query = query;
    this.queryString = queryString;
  }

  filter() {
    const queryObj = { ...this.queryString };
    ['page', 'sort', 'limit', 'fields', 'search'].forEach(f => delete queryObj[f]);
    let str = JSON.stringify(queryObj);
    str = str.replace(/\b(gte|gt|lte|lt|ne)\b/g, m => `$${m}`);
    this.query = this.query.find(JSON.parse(str));
    return this;
  }

  search() {
    if (this.queryString.search) {
      this.query = this.query.find({
        $or: [
          { title: { $regex: this.queryString.search, $options: 'i' } },
          { content: { $regex: this.queryString.search, $options: 'i' } },
        ],
      });
    }
    return this;
  }

  sort() {
    this.query = this.query.sort(
      this.queryString.sort?.split(',').join(' ') || '-isPinned -createdAt'
    );
    return this;
  }

  selectFields() {
    this.query = this.query.select(
      this.queryString.fields?.split(',').join(' ') || '-__v'
    );
    return this;
  }

  paginate() {
    this.page = Math.max(1, parseInt(this.queryString.page) || 1);
    this.limit = Math.min(100, Math.max(1, parseInt(this.queryString.limit) || 20));
    this.query = this.query.skip((this.page - 1) * this.limit).limit(this.limit);
    return this;
  }
}
```

---

## 🔧 Step 5: Controllers

```js
// src/controllers/noteController.js
import Note from '../models/Note.js';
import { AppError } from '../utils/AppError.js';
import { APIFeatures } from '../utils/APIFeatures.js';

export async function getAllNotes(req, res) {
  const features = new APIFeatures(Note.find(), req.query)
    .filter().search().sort().selectFields().paginate();

  const [notes, total] = await Promise.all([
    features.query.populate('folder', 'name color icon').populate('tags', 'name color'),
    Note.countDocuments(features.query.getFilter()),
  ]);

  res.json({
    status: 'success', count: notes.length, total,
    page: features.page, totalPages: Math.ceil(total / features.limit),
    data: notes,
  });
}

export async function getNoteById(req, res) {
  const note = await Note.findById(req.params.id)
    .populate('folder', 'name color icon')
    .populate('tags', 'name color')
    .select('-__v');
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', data: note });
}

export async function createNote(req, res) {
  const note = await Note.create(req.body);
  await note.populate([
    { path: 'folder', select: 'name color' },
    { path: 'tags', select: 'name color' },
  ]);
  res.status(201).json({ status: 'success', data: note });
}

export async function updateNote(req, res) {
  const note = await Note.findByIdAndUpdate(req.params.id, req.body, {
    new: true, runValidators: true,
  }).populate('folder', 'name color').populate('tags', 'name color');
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', data: note });
}

export async function deleteNote(req, res) {
  const note = await Note.findByIdAndDelete(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  res.json({ status: 'success', message: `Note "${note.title}" deleted` });
}

export async function togglePin(req, res) {
  const note = await Note.findById(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  note.isPinned = !note.isPinned;
  await note.save();
  res.json({ status: 'success', data: note });
}

export async function toggleFavorite(req, res) {
  const note = await Note.findById(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  note.isFavorite = !note.isFavorite;
  await note.save();
  res.json({ status: 'success', data: note });
}

export async function addTags(req, res) {
  const note = await Note.findById(req.params.id);
  if (!note) throw new AppError('Note not found', 404);
  const { tagIds } = req.body;
  for (const id of tagIds) {
    if (!note.tags.map(t => t.toString()).includes(id)) note.tags.push(id);
  }
  await note.save();
  await note.populate('tags', 'name color');
  res.json({ status: 'success', data: note });
}

export async function getStats(req, res) {
  const stats = await Note.getStats();
  res.json({ status: 'success', data: stats });
}
```

```js
// src/controllers/folderController.js
import Folder from '../models/Folder.js';
import Note from '../models/Note.js';
import { AppError } from '../utils/AppError.js';

export async function getAllFolders(req, res) {
  const folders = await Folder.find().sort('name');
  const data = await Promise.all(folders.map(async (f) => ({
    ...f.toObject(),
    noteCount: await Note.countDocuments({ folder: f._id }),
  })));
  res.json({ status: 'success', data });
}

export async function createFolder(req, res) {
  const folder = await Folder.create(req.body);
  res.status(201).json({ status: 'success', data: { ...folder.toObject(), noteCount: 0 } });
}

export async function getFolderNotes(req, res) {
  const folder = await Folder.findById(req.params.id);
  if (!folder) throw new AppError('Folder not found', 404);
  const notes = await Note.find({ folder: folder._id })
    .populate('tags', 'name color').sort('-isPinned -createdAt').select('-__v');
  res.json({ status: 'success', folder: { name: folder.name, color: folder.color }, count: notes.length, data: notes });
}

export async function updateFolder(req, res) {
  const folder = await Folder.findByIdAndUpdate(req.params.id, req.body, { new: true, runValidators: true });
  if (!folder) throw new AppError('Folder not found', 404);
  res.json({ status: 'success', data: folder });
}

export async function deleteFolder(req, res) {
  const folder = await Folder.findByIdAndDelete(req.params.id);
  if (!folder) throw new AppError('Folder not found', 404);
  await Note.updateMany({ folder: folder._id }, { $set: { folder: null } });
  res.json({ status: 'success', message: `Folder "${folder.name}" deleted` });
}
```

```js
// src/controllers/tagController.js
import Tag from '../models/Tag.js';
import Note from '../models/Note.js';
import { AppError } from '../utils/AppError.js';

export async function getAllTags(req, res) {
  const tags = await Tag.find().populate('noteCount').sort('name');
  res.json({ status: 'success', data: tags });
}

export async function createTag(req, res) {
  const tag = await Tag.create(req.body);
  res.status(201).json({ status: 'success', data: tag });
}

export async function deleteTag(req, res) {
  const tag = await Tag.findByIdAndDelete(req.params.id);
  if (!tag) throw new AppError('Tag not found', 404);
  await Note.updateMany({ tags: tag._id }, { $pull: { tags: tag._id } });
  res.json({ status: 'success', message: `Tag "${tag.name}" deleted` });
}
```

---

## 🔧 Step 6: Routes

```js
// src/routes/noteRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/noteController.js';
const router = Router();

router.get('/stats', asyncHandler(ctrl.getStats));
router.route('/').get(asyncHandler(ctrl.getAllNotes)).post(asyncHandler(ctrl.createNote));
router.route('/:id').get(asyncHandler(ctrl.getNoteById)).patch(asyncHandler(ctrl.updateNote)).delete(asyncHandler(ctrl.deleteNote));
router.patch('/:id/pin', asyncHandler(ctrl.togglePin));
router.patch('/:id/favorite', asyncHandler(ctrl.toggleFavorite));
router.patch('/:id/tags', asyncHandler(ctrl.addTags));
export default router;

// src/routes/folderRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/folderController.js';
const router = Router();
router.route('/').get(asyncHandler(ctrl.getAllFolders)).post(asyncHandler(ctrl.createFolder));
router.route('/:id').patch(asyncHandler(ctrl.updateFolder)).delete(asyncHandler(ctrl.deleteFolder));
router.get('/:id/notes', asyncHandler(ctrl.getFolderNotes));
export default router;

// src/routes/tagRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import * as ctrl from '../controllers/tagController.js';
const router = Router();
router.route('/').get(asyncHandler(ctrl.getAllTags)).post(asyncHandler(ctrl.createTag));
router.delete('/:id', asyncHandler(ctrl.deleteTag));
export default router;

// src/routes/index.js
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

---

## 🔧 Step 7: App & Server

```js
// src/app.js
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import compression from 'compression';
import apiRoutes from './routes/index.js';

const app = express();
app.use(helmet());
app.use(cors());
app.use(compression());
app.use(morgan('dev'));
app.use(express.json({ limit: '10kb' }));

app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

app.use('/api', apiRoutes);

// Error handling
app.use((req, res) => {
  res.status(404).json({ status: 'error', message: `Route ${req.method} ${req.originalUrl} not found` });
});

app.use((err, req, res, next) => {
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
  const statusCode = err.statusCode || 500;
  res.status(statusCode).json({ status: 'error', message: statusCode === 500 ? 'Internal Server Error' : err.message });
});

export default app;
```

```js
// index.js
import 'dotenv/config';
import { connectDB } from './src/config/database.js';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;

connectDB().then(() => {
  app.listen(PORT, () => console.log(`📝 Notes App running on http://localhost:${PORT}`));
});
```

---

## 🧪 Test Your API

```bash
# Create tags
curl -X POST http://localhost:3000/api/tags -H "Content-Type: application/json" -d '{"name": "important", "color": "#ef4444"}'
curl -X POST http://localhost:3000/api/tags -H "Content-Type: application/json" -d '{"name": "javascript", "color": "#f7df1e"}'

# Create folders
curl -X POST http://localhost:3000/api/folders -H "Content-Type: application/json" -d '{"name": "Work", "icon": "💼", "color": "#3b82f6"}'
curl -X POST http://localhost:3000/api/folders -H "Content-Type: application/json" -d '{"name": "Study", "icon": "📚", "color": "#10b981"}'

# Create notes
curl -X POST http://localhost:3000/api/notes -H "Content-Type: application/json" \
  -d '{"title": "Learn MongoDB", "content": "MongoDB is a document database...", "author": "Ali", "category": "study", "folder": "<FOLDER_ID>"}'

# List notes with search & filter
curl "http://localhost:3000/api/notes?search=mongodb&category=study&sort=-createdAt&limit=5"

# Pin/favorite
curl -X PATCH http://localhost:3000/api/notes/<NOTE_ID>/pin
curl -X PATCH http://localhost:3000/api/notes/<NOTE_ID>/favorite

# Add tags
curl -X PATCH http://localhost:3000/api/notes/<NOTE_ID>/tags \
  -H "Content-Type: application/json" -d '{"tagIds": ["<TAG_ID_1>", "<TAG_ID_2>"]}'

# Get folder notes
curl http://localhost:3000/api/folders/<FOLDER_ID>/notes

# Stats
curl http://localhost:3000/api/notes/stats
```

---

## ✅ Complete API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/notes` | List all notes (filter, search, sort, paginate) |
| `GET` | `/api/notes/stats` | Aggregation statistics |
| `GET` | `/api/notes/:id` | Single note with folder & tags |
| `POST` | `/api/notes` | Create note |
| `PATCH` | `/api/notes/:id` | Update note |
| `DELETE` | `/api/notes/:id` | Delete note |
| `PATCH` | `/api/notes/:id/pin` | Toggle pin |
| `PATCH` | `/api/notes/:id/favorite` | Toggle favorite |
| `PATCH` | `/api/notes/:id/tags` | Add tags |
| `GET` | `/api/folders` | List folders with note counts |
| `POST` | `/api/folders` | Create folder |
| `PATCH` | `/api/folders/:id` | Update folder |
| `DELETE` | `/api/folders/:id` | Delete folder |
| `GET` | `/api/folders/:id/notes` | Notes in folder |
| `GET` | `/api/tags` | List tags with note counts |
| `POST` | `/api/tags` | Create tag |
| `DELETE` | `/api/tags/:id` | Delete tag |

---

## 💡 What You Built

- **MongoDB-powered Notes API** with 3 related collections
- **Mongoose models** with validation, virtuals, indexes, and statics
- **Data relationships** — folders (one-to-many), tags (many-to-many)
- **Advanced queries** — filtering, search, sorting, pagination with `APIFeatures`
- **Aggregation pipeline** — real-time statistics dashboard
- **Full-text search** across titles and content
- **Production patterns** — error handling for all Mongoose error types

---

## 🎯 Week 15 Complete!

You've mastered MongoDB & Mongoose! Next week: **Authentication & Security** — adding user accounts and protecting your APIs! 🔐

---

[← Day 5](day-05.md) | [Back to Week 15](README.md) | [Next Week →](../week-16/README.md)
