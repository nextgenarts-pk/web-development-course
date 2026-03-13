# 📅 Week 15 — Day 1: Introduction to MongoDB 🍃

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand NoSQL databases and how MongoDB differs from SQL
- Learn MongoDB's document-based architecture
- Set up MongoDB locally and on MongoDB Atlas (cloud)
- Perform basic CRUD operations using `mongosh`
- Connect a Node.js application to MongoDB

---

## 📖 Lesson Content

### 1. SQL vs NoSQL

| Feature | SQL (MySQL, PostgreSQL) | NoSQL (MongoDB) |
|---------|------------------------|-----------------|
| **Data Format** | Tables with rows & columns | Collections with documents (JSON) |
| **Schema** | Fixed — must define columns first | Flexible — documents can vary |
| **Relationships** | JOINs across tables | Embedded documents or references |
| **Scaling** | Vertical (bigger server) | Horizontal (more servers) |
| **Query Language** | SQL | MongoDB Query Language (MQL) |
| **Best For** | Structured, relational data | Flexible, evolving data |

### 2. MongoDB Architecture

```
Database Server (mongod)
├── Database: "blog_app"
│   ├── Collection: "posts"
│   │   ├── Document: { _id: ..., title: "...", content: "..." }
│   │   ├── Document: { _id: ..., title: "...", content: "..." }
│   ├── Collection: "users"
│   │   ├── Document: { _id: ..., name: "...", email: "..." }
│   └── Collection: "comments"
```

- **Database** = a set of collections (like a SQL database)
- **Collection** = a group of documents (like a SQL table)
- **Document** = a JSON-like record (like a SQL row)

### 3. BSON & Document Structure

MongoDB stores data as **BSON** (Binary JSON):

```json
{
  "_id": "507f1f77bcf86cd799439011",
  "title": "Learning MongoDB",
  "content": "MongoDB is a document database...",
  "author": {
    "name": "Ali Khan",
    "email": "ali@example.com"
  },
  "tags": ["mongodb", "database", "nosql"],
  "views": 142,
  "published": true,
  "createdAt": "2024-01-15T10:30:00Z"
}
```

**Key features:**
- `_id` — auto-generated unique ObjectId
- Nested objects (author) — embedded documents
- Arrays (tags) — first-class support
- Flexible — not all documents need the same fields

### 4. Setting Up MongoDB

#### Option A: MongoDB Atlas (Cloud — Recommended)

1. Go to [mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account → Build a Cluster → **M0 Free Tier**
3. Set up database user (username/password)
4. Allow network access (0.0.0.0/0 for development)
5. Click **Connect** → **Connect your application** → Copy connection string

```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/mydb?retryWrites=true&w=majority
```

#### Option B: Local Installation

- **Windows**: Download from [mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)
- **Mac**: `brew install mongodb-community`
- **Linux**: Follow [official docs](https://www.mongodb.com/docs/manual/installation/)

### 5. MongoDB Shell (mongosh)

```bash
# Connect to local MongoDB
mongosh

# Connect to Atlas
mongosh "mongodb+srv://cluster0.xxxxx.mongodb.net/" --username youruser
```

### 6. Basic Operations in mongosh

```js
// Show databases
show dbs

// Use/create a database
use blog_app

// Show collections
show collections

// ── CREATE ──
// Insert one document
db.posts.insertOne({
  title: "My First Post",
  content: "Hello MongoDB!",
  author: "Ali",
  tags: ["intro", "mongodb"],
  views: 0,
  createdAt: new Date()
})

// Insert many documents
db.posts.insertMany([
  { title: "Express Guide", content: "Learn Express...", author: "Sara", tags: ["express"] },
  { title: "React Basics", content: "React is...", author: "Ali", tags: ["react"] },
])

// ── READ ──
// Find all documents
db.posts.find()

// Find with filter
db.posts.find({ author: "Ali" })

// Find one
db.posts.findOne({ title: "My First Post" })

// Query operators
db.posts.find({ views: { $gte: 100 } })          // views >= 100
db.posts.find({ tags: { $in: ["react", "node"] } }) // has any of these tags
db.posts.find({ author: "Ali", views: { $gt: 0 } }) // AND condition

// Projection — select specific fields
db.posts.find({}, { title: 1, author: 1, _id: 0 })

// Sort, limit, skip
db.posts.find().sort({ createdAt: -1 }).limit(10).skip(0)

// ── UPDATE ──
// Update one
db.posts.updateOne(
  { title: "My First Post" },
  { $set: { views: 50, updatedAt: new Date() } }
)

// Increment a field
db.posts.updateOne(
  { title: "My First Post" },
  { $inc: { views: 1 } }
)

// Add to array
db.posts.updateOne(
  { title: "My First Post" },
  { $push: { tags: "beginner" } }
)

// Update many
db.posts.updateMany(
  { author: "Ali" },
  { $set: { status: "published" } }
)

// ── DELETE ──
// Delete one
db.posts.deleteOne({ title: "My First Post" })

// Delete many
db.posts.deleteMany({ author: "Sara" })

// ── COUNT ──
db.posts.countDocuments()
db.posts.countDocuments({ author: "Ali" })

// Drop collection
db.posts.drop()
```

### 7. MongoDB Query Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `$eq` | Equal | `{ age: { $eq: 25 } }` |
| `$ne` | Not equal | `{ status: { $ne: "draft" } }` |
| `$gt` | Greater than | `{ views: { $gt: 100 } }` |
| `$gte` | Greater or equal | `{ views: { $gte: 100 } }` |
| `$lt` | Less than | `{ price: { $lt: 50 } }` |
| `$lte` | Less or equal | `{ price: { $lte: 50 } }` |
| `$in` | In array | `{ status: { $in: ["active", "pending"] } }` |
| `$nin` | Not in array | `{ role: { $nin: ["admin"] } }` |
| `$exists` | Field exists | `{ email: { $exists: true } }` |
| `$regex` | Pattern match | `{ title: { $regex: /mongo/i } }` |
| `$and` | Logical AND | `{ $and: [{ age: { $gt: 18 } }, { status: "active" }] }` |
| `$or` | Logical OR | `{ $or: [{ author: "Ali" }, { author: "Sara" }] }` |

### 8. Connecting Node.js to MongoDB

```bash
npm install mongodb
```

```js
// db.js — Native MongoDB driver
import { MongoClient } from 'mongodb';

const uri = process.env.MONGODB_URI || 'mongodb://localhost:27017';
const client = new MongoClient(uri);

let db;

export async function connectDB() {
  try {
    await client.connect();
    db = client.db('blog_app');
    console.log('Connected to MongoDB');
    return db;
  } catch (error) {
    console.error('MongoDB connection error:', error.message);
    process.exit(1);
  }
}

export function getDB() {
  if (!db) throw new Error('Database not connected');
  return db;
}
```

```js
// index.js
import { connectDB } from './db.js';

async function start() {
  const db = await connectDB();

  // Insert a document
  const result = await db.collection('posts').insertOne({
    title: 'Hello from Node.js',
    content: 'Connected successfully!',
    createdAt: new Date(),
  });

  console.log('Inserted:', result.insertedId);

  // Find documents
  const posts = await db.collection('posts').find().toArray();
  console.log('Posts:', posts);
}

start();
```

### 9. Environment Variables for Database

```env
# .env
MONGODB_URI=mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/blog_app?retryWrites=true&w=majority
```

> **Never hardcode database credentials** — always use environment variables.

---

## 💪 Hands-On Exercise

### Explore MongoDB

1. Set up MongoDB Atlas (free tier) OR install locally
2. Connect using `mongosh`
3. Create a `practice` database
4. Insert 10 documents into a `students` collection with: name, age, grade, subjects (array), enrolled (boolean)
5. Practice queries:
   - Find all students with grade > 80
   - Find students enrolled in "math"
   - Update a student's grade
   - Delete students with grade < 50
   - Sort by grade descending, limit to 5

---

## 📝 Homework

1. Create a `products` collection with 15 products (name, price, category, inStock, rating)
2. Write queries to:
   - Find all products under $50
   - Find products in the "electronics" category with rating > 4
   - Count products in stock
   - Find the most expensive product
   - Update all products in "clothing" category to add a 10% discount field
3. Connect a Node.js script to your MongoDB and perform the same queries programmatically

---

## 💡 Pro Tips

- Use MongoDB Atlas for cloud hosting — the free tier is generous (512MB)
- MongoDB Compass (GUI) is excellent for visualizing data — download it from mongodb.com
- `_id` is automatically created as an ObjectId — don't manually set it unless needed
- Use `$inc` for counters instead of read-then-write — it's atomic
- Prefer Atlas for production — it handles backups, monitoring, and scaling
- The connection string is sensitive — always store in `.env`

---

[Back to Week 15](README.md) | [Day 2 →](day-02.md)
