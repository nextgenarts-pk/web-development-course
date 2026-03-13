# 📅 Week 15 — Day 2: Mongoose Schema & Models 📐

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand why we use Mongoose over the native driver
- Define schemas with data types, validation, and defaults
- Create Models and use them to interact with MongoDB
- Add virtual fields and timestamps
- Implement custom instance and static methods

---

## 📖 Lesson Content

### 1. Why Mongoose?

| Native MongoDB Driver | Mongoose |
|----------------------|----------|
| Raw queries — flexible but verbose | Schema-based — structured and validated |
| No built-in validation | Built-in + custom validation |
| Manual type casting | Automatic type casting |
| No middleware hooks | Pre/post middleware (hooks) |
| Good for simple scripts | Perfect for Express APIs |

### 2. Setting Up Mongoose

```bash
npm install mongoose
```

```js
// config/database.js
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

// Handle connection events
mongoose.connection.on('disconnected', () => {
  console.log('MongoDB disconnected');
});

process.on('SIGINT', async () => {
  await mongoose.connection.close();
  process.exit(0);
});
```

```js
// index.js
import 'dotenv/config';
import { connectDB } from './config/database.js';
import app from './src/app.js';

const PORT = process.env.PORT || 3000;

connectDB().then(() => {
  app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
});
```

### 3. Defining a Schema

A schema maps to a MongoDB collection and defines the shape of documents:

```js
// models/Note.js
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
    enum: {
      values: ['personal', 'work', 'study', 'ideas', 'other'],
      message: '{VALUE} is not a valid category',
    },
    default: 'personal',
  },
  tags: {
    type: [String],
    default: [],
  },
  isPinned: {
    type: Boolean,
    default: false,
  },
  color: {
    type: String,
    default: '#ffffff',
    match: [/^#[0-9a-fA-F]{6}$/, 'Color must be a valid hex code'],
  },
  author: {
    type: String,
    required: [true, 'Author is required'],
    trim: true,
  },
}, {
  timestamps: true,  // Adds createdAt and updatedAt automatically
});

const Note = mongoose.model('Note', noteSchema);
export default Note;
```

### 4. Schema Types Reference

| Type | Example | Description |
|------|---------|-------------|
| `String` | `{ type: String }` | Text data |
| `Number` | `{ type: Number }` | Integer or float |
| `Boolean` | `{ type: Boolean }` | true/false |
| `Date` | `{ type: Date }` | Date object |
| `Array` | `{ type: [String] }` | Array of values |
| `ObjectId` | `{ type: mongoose.Schema.Types.ObjectId }` | Reference to another document |
| `Mixed` | `{ type: mongoose.Schema.Types.Mixed }` | Any type (use sparingly) |
| `Buffer` | `{ type: Buffer }` | Binary data |

### 5. Validation Options

```js
const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Name is required'],        // Required with message
    trim: true,                                    // Remove whitespace
    minlength: [2, 'Min 2 characters'],           // Minimum length
    maxlength: [100, 'Max 100 characters'],       // Maximum length
  },
  email: {
    type: String,
    required: true,
    unique: true,                                  // Unique index
    lowercase: true,                               // Convert to lowercase
    match: [/^\S+@\S+\.\S+$/, 'Invalid email'],  // Regex pattern
  },
  age: {
    type: Number,
    min: [0, 'Age cannot be negative'],
    max: [150, 'Age seems unrealistic'],
  },
  role: {
    type: String,
    enum: ['user', 'admin', 'editor'],             // Only these values
    default: 'user',
  },
  website: {
    type: String,
    validate: {
      validator: function(v) {
        return v === '' || /^https?:\/\/.+/.test(v);
      },
      message: 'Website must be a valid URL',
    },
  },
});
```

### 6. Custom Validation

```js
const noteSchema = new mongoose.Schema({
  tags: {
    type: [String],
    validate: {
      validator: function(tags) {
        return tags.length <= 10;
      },
      message: 'A note can have at most 10 tags',
    },
  },
  priority: {
    type: Number,
    validate: {
      validator: Number.isInteger,
      message: 'Priority must be an integer',
    },
    min: 1,
    max: 5,
  },
});
```

### 7. Virtual Fields

Virtuals are computed properties that don't get stored in the database:

```js
const noteSchema = new mongoose.Schema({
  title: String,
  content: String,
  author: String,
}, { timestamps: true, toJSON: { virtuals: true }, toObject: { virtuals: true } });

// Virtual: content preview (first 100 chars)
noteSchema.virtual('preview').get(function() {
  return this.content.length > 100
    ? this.content.substring(0, 100) + '...'
    : this.content;
});

// Virtual: reading time (estimated)
noteSchema.virtual('readTime').get(function() {
  const wordsPerMinute = 200;
  const wordCount = this.content.split(/\s+/).length;
  return Math.ceil(wordCount / wordsPerMinute);
});

// Virtual: word count
noteSchema.virtual('wordCount').get(function() {
  return this.content.split(/\s+/).length;
});
```

### 8. Instance Methods

Methods available on individual document instances:

```js
// Check if note is long
noteSchema.methods.isLong = function() {
  return this.content.split(/\s+/).length > 500;
};

// Get formatted date
noteSchema.methods.getFormattedDate = function() {
  return this.createdAt.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
};

// Usage:
const note = await Note.findById(id);
console.log(note.isLong());         // false
console.log(note.getFormattedDate()); // "January 15, 2024"
```

### 9. Static Methods

Methods available on the Model itself (for collection-wide operations):

```js
// Find notes by category
noteSchema.statics.findByCategory = function(category) {
  return this.find({ category }).sort({ createdAt: -1 });
};

// Find pinned notes
noteSchema.statics.findPinned = function() {
  return this.find({ isPinned: true }).sort({ updatedAt: -1 });
};

// Search notes
noteSchema.statics.search = function(query) {
  return this.find({
    $or: [
      { title: { $regex: query, $options: 'i' } },
      { content: { $regex: query, $options: 'i' } },
      { tags: { $in: [new RegExp(query, 'i')] } },
    ],
  });
};

// Get statistics
noteSchema.statics.getStats = async function() {
  const total = await this.countDocuments();
  const pinned = await this.countDocuments({ isPinned: true });
  const byCategory = await this.aggregate([
    { $group: { _id: '$category', count: { $sum: 1 } } },
  ]);

  return { total, pinned, byCategory };
};

// Usage:
const workNotes = await Note.findByCategory('work');
const stats = await Note.getStats();
```

### 10. Middleware (Hooks)

Run code before or after certain operations:

```js
// Pre-save: run before saving
noteSchema.pre('save', function(next) {
  // Auto-generate tags from content (basic)
  if (this.isModified('content') && this.tags.length === 0) {
    const words = this.content.toLowerCase().split(/\s+/);
    const commonWords = new Set(['the', 'a', 'is', 'in', 'to', 'and', 'of', 'for']);
    this.tags = [...new Set(words.filter(w => w.length > 4 && !commonWords.has(w)))].slice(0, 5);
  }
  next();
});

// Pre-find: always exclude soft-deleted
noteSchema.pre(/^find/, function(next) {
  // this refers to the query
  if (!this.getOptions().includeDeleted) {
    this.where({ isDeleted: { $ne: true } });
  }
  next();
});

// Post-save: run after saving
noteSchema.post('save', function(doc) {
  console.log(`Note saved: ${doc.title} (${doc._id})`);
});
```

### 11. Complete Note Model

```js
// models/Note.js
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
  tags: { type: [String], default: [] },
  isPinned: { type: Boolean, default: false },
  color: { type: String, default: '#ffffff' },
  author: { type: String, required: true, trim: true },
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

// Statics
noteSchema.statics.findByCategory = function(category) {
  return this.find({ category }).sort({ createdAt: -1 });
};

noteSchema.statics.search = function(query) {
  return this.find({
    $or: [
      { title: { $regex: query, $options: 'i' } },
      { content: { $regex: query, $options: 'i' } },
    ],
  });
};

// Index for search performance
noteSchema.index({ title: 'text', content: 'text' });
noteSchema.index({ category: 1, createdAt: -1 });

const Note = mongoose.model('Note', noteSchema);
export default Note;
```

---

## 💪 Hands-On Exercise

### Build the Note Model

1. Set up Mongoose and connect to your MongoDB database
2. Create a `Note` schema with: title, content, category (enum), tags (array), isPinned, color, author
3. Add validation: required fields, minlength, maxlength, enum
4. Add a `preview` virtual that returns first 100 characters
5. Add a `findByCategory` static method
6. Add a `search` static method
7. Test by creating, reading, and querying notes

---

## 📝 Homework

1. Create a `Folder` model with: name (required, unique), description, color, noteCount (virtual)
2. Add a custom validator to ensure `color` is a valid hex code
3. Create a pre-save hook that trims whitespace from the title
4. Add a `readTime` virtual (words / 200, rounded up)
5. Create a static method `getStats()` that returns total notes, notes per category, and pinned count

---

## 💡 Pro Tips

- Always add `{ timestamps: true }` — you almost always need createdAt/updatedAt
- Enable virtuals in JSON: `{ toJSON: { virtuals: true } }` — otherwise they won't appear in API responses
- Use `trim: true` on all String fields to prevent whitespace issues
- Custom validation messages use `{VALUE}` to include the rejected value
- Schemas are defined once, but Models are what you use in controllers — `const Note = mongoose.model('Note', noteSchema)`
- Add indexes on fields you frequently query or sort by

---

[← Day 1](day-01.md) | [Back to Week 15](README.md) | [Day 3 →](day-03.md)
