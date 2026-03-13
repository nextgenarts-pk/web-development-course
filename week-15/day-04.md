# 📅 Week 15 — Day 4: Advanced Queries & Indexes 🔍

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Build advanced queries with the Mongoose Query API
- Use the aggregation pipeline for complex data analysis
- Create and use indexes for query performance
- Implement full-text search
- Build an advanced API query builder

---

## 📖 Lesson Content

### 1. Chaining Query Methods

Mongoose queries are chainable — build complex queries step by step:

```js
const notes = await Note
  .find({ category: 'work' })           // Filter
  .where('isPinned').equals(true)        // Additional filter
  .sort('-createdAt')                    // Sort descending
  .select('title category createdAt')    // Only these fields
  .skip(0)                               // Offset
  .limit(10)                             // Max results
  .lean();                               // Plain objects (faster)
```

`.lean()` returns plain JavaScript objects instead of Mongoose documents — about **5x faster** for read-only operations.

### 2. Advanced Filtering

```js
// Date ranges
const thisMonth = await Note.find({
  createdAt: {
    $gte: new Date('2024-01-01'),
    $lt: new Date('2024-02-01'),
  },
});

// Text matching
const matches = await Note.find({
  title: { $regex: 'express', $options: 'i' },
});

// Array queries
const withTag = await Note.find({ tags: 'javascript' });   // Has this tag
const allTags = await Note.find({ tags: { $all: ['js', 'node'] } });  // Has ALL tags
const anyTag = await Note.find({ tags: { $in: ['react', 'vue'] } });  // Has ANY tag

// Nested field queries (if using embedded documents)
const byCity = await User.find({ 'address.city': 'Karachi' });

// Not null / not empty
const withContent = await Note.find({
  content: { $exists: true, $ne: '' },
});
```

### 3. The Aggregation Pipeline

The aggregation pipeline processes documents through stages — like a data assembly line:

```js
// Basic aggregation: count notes per category
const stats = await Note.aggregate([
  { $match: { isPinned: false } },               // Stage 1: filter
  { $group: {                                     // Stage 2: group
      _id: '$category',
      count: { $sum: 1 },
      avgWordCount: { $avg: { $size: { $split: ['$content', ' '] } } },
    }
  },
  { $sort: { count: -1 } },                      // Stage 3: sort
]);

// Result:
// [
//   { _id: 'work', count: 15, avgWordCount: 120 },
//   { _id: 'personal', count: 12, avgWordCount: 85 },
//   { _id: 'study', count: 8, avgWordCount: 200 },
// ]
```

### 4. Aggregation Stages Reference

| Stage | Purpose | Example |
|-------|---------|---------|
| `$match` | Filter documents | `{ $match: { status: 'active' } }` |
| `$group` | Group by field | `{ $group: { _id: '$category', count: { $sum: 1 } } }` |
| `$sort` | Sort results | `{ $sort: { count: -1 } }` |
| `$project` | Reshape documents | `{ $project: { title: 1, year: { $year: '$createdAt' } } }` |
| `$limit` | Limit results | `{ $limit: 10 }` |
| `$skip` | Skip results | `{ $skip: 20 }` |
| `$unwind` | Flatten arrays | `{ $unwind: '$tags' }` |
| `$lookup` | Join collections | Foreign key join (like SQL JOIN) |
| `$addFields` | Add computed fields | `{ $addFields: { fullName: { $concat: ['$first', ' ', '$last'] } } }` |
| `$count` | Count documents | `{ $count: 'total' }` |

### 5. Real-World Aggregation Examples

```js
// Notes created per month
const monthlyStats = await Note.aggregate([
  {
    $group: {
      _id: {
        year: { $year: '$createdAt' },
        month: { $month: '$createdAt' },
      },
      count: { $sum: 1 },
    },
  },
  { $sort: { '_id.year': -1, '_id.month': -1 } },
  { $limit: 12 },
]);

// Top 10 most used tags
const topTags = await Note.aggregate([
  { $unwind: '$tags' },                         // Flatten tags array
  { $group: { _id: '$tags', count: { $sum: 1 } } },
  { $sort: { count: -1 } },
  { $limit: 10 },
]);

// Author statistics
const authorStats = await Note.aggregate([
  {
    $group: {
      _id: '$author',
      totalNotes: { $sum: 1 },
      pinnedNotes: {
        $sum: { $cond: ['$isPinned', 1, 0] },
      },
      categories: { $addToSet: '$category' },
    },
  },
  { $sort: { totalNotes: -1 } },
]);
```

### 6. Indexes — Why They Matter

Without an index, MongoDB scans **every document** (collection scan). With an index, it jumps directly to matching documents:

```
Without index: Scan 1,000,000 docs → 500ms
With index:    Lookup in B-tree    → 2ms
```

### 7. Creating Indexes

```js
// In the schema
const noteSchema = new mongoose.Schema({ ... });

// Single field index
noteSchema.index({ category: 1 });           // 1 = ascending, -1 = descending

// Compound index (multiple fields)
noteSchema.index({ category: 1, createdAt: -1 });

// Unique index
noteSchema.index({ title: 1 }, { unique: true });

// Text index (for full-text search)
noteSchema.index({ title: 'text', content: 'text' });

// TTL index (auto-delete after time)
noteSchema.index({ deletedAt: 1 }, { expireAfterSeconds: 2592000 }); // 30 days
```

### 8. Full-Text Search

```js
// Requires a text index on the fields
noteSchema.index({ title: 'text', content: 'text' });

// Search using $text
const results = await Note.find(
  { $text: { $search: 'javascript express' } },  // Search for these words
  { score: { $meta: 'textScore' } }               // Include relevance score
).sort({ score: { $meta: 'textScore' } });        // Sort by relevance

// Phrase search (exact match)
const exact = await Note.find({
  $text: { $search: '"express middleware"' },
});

// Exclude words
const filtered = await Note.find({
  $text: { $search: 'javascript -react' },  // Has "javascript" but NOT "react"
});
```

### 9. Advanced API Query Builder

Build a reusable class for API queries:

```js
// utils/APIFeatures.js
export class APIFeatures {
  constructor(query, queryString) {
    this.query = query;
    this.queryString = queryString;
  }

  filter() {
    const queryObj = { ...this.queryString };
    const excludedFields = ['page', 'sort', 'limit', 'fields', 'search'];
    excludedFields.forEach(field => delete queryObj[field]);

    // Advanced filtering: ?price[gte]=100 → { price: { $gte: 100 } }
    let queryStr = JSON.stringify(queryObj);
    queryStr = queryStr.replace(/\b(gte|gt|lte|lt|ne)\b/g, match => `$${match}`);

    this.query = this.query.find(JSON.parse(queryStr));
    return this;  // Enable chaining
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
    if (this.queryString.sort) {
      const sortBy = this.queryString.sort.split(',').join(' ');
      this.query = this.query.sort(sortBy);
    } else {
      this.query = this.query.sort('-createdAt');
    }
    return this;
  }

  selectFields() {
    if (this.queryString.fields) {
      const fields = this.queryString.fields.split(',').join(' ');
      this.query = this.query.select(fields);
    } else {
      this.query = this.query.select('-__v');
    }
    return this;
  }

  paginate() {
    const page = Math.max(1, parseInt(this.queryString.page) || 1);
    const limit = Math.min(100, Math.max(1, parseInt(this.queryString.limit) || 10));
    const skip = (page - 1) * limit;

    this.query = this.query.skip(skip).limit(limit);
    this.page = page;
    this.limit = limit;
    return this;
  }
}
```

```js
// controllers/noteController.js — using APIFeatures
import { APIFeatures } from '../utils/APIFeatures.js';

export async function getAllNotes(req, res) {
  const features = new APIFeatures(Note.find(), req.query)
    .filter()
    .search()
    .sort()
    .selectFields()
    .paginate();

  const [notes, total] = await Promise.all([
    features.query,
    Note.countDocuments(features.query.getFilter()),
  ]);

  res.json({
    status: 'success',
    count: notes.length,
    total,
    page: features.page,
    totalPages: Math.ceil(total / features.limit),
    data: notes,
  });
}

// Now supports queries like:
// GET /api/notes?category=work&isPinned=true&sort=-createdAt&page=2&limit=5
// GET /api/notes?search=javascript&fields=title,category
// GET /api/notes?createdAt[gte]=2024-01-01&sort=title
```

### 10. Checking Index Usage

```js
// In mongosh — see what indexes exist
db.notes.getIndexes();

// Explain a query — see if an index was used
db.notes.find({ category: 'work' }).explain('executionStats');

// Look for:
// - "IXSCAN" = index used ✅
// - "COLLSCAN" = full collection scan ❌
```

---

## 💪 Hands-On Exercise

### Advanced Notes Queries

1. Create the `APIFeatures` class from section 9
2. Implement the aggregation stats endpoint:
   - Notes per category
   - Top 5 most used tags
   - Notes created per month
3. Add a text index on `title` and `content`
4. Implement full-text search: `GET /api/notes?search=javascript`
5. Add compound index on `{ category: 1, createdAt: -1 }`
6. Test filtering: `GET /api/notes?category=work&isPinned=true&sort=-createdAt&page=1&limit=5`

---

## 📝 Homework

1. Create an aggregation pipeline that shows the author with the most notes
2. Implement a `GET /api/notes/tags` endpoint that returns all unique tags with counts (using aggregation)
3. Add a `GET /api/notes/recent` endpoint that returns notes from the last 7 days
4. Test with 100+ notes — measure query time with and without indexes using `explain()`
5. Add a date range filter: `GET /api/notes?from=2024-01-01&to=2024-01-31`

---

## 💡 Pro Tips

- Use `.lean()` for read-only responses — 5x faster than full Mongoose documents
- Always add indexes for fields you filter or sort by — check with `.explain()`
- Compound indexes follow the **ESR Rule**: Equality → Sort → Range
- Text indexes are powerful but only one per collection — plan carefully
- The aggregation pipeline is MongoDB's most powerful feature — master it
- Use `APIFeatures` class in every project — saves hours of repetitive query code
- TTL indexes auto-delete expired documents — perfect for soft-delete cleanup

---

[← Day 3](day-03.md) | [Back to Week 15](README.md) | [Day 5 →](day-05.md)
