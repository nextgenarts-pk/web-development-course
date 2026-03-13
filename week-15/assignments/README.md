# 📋 Week 15 Assignments — MongoDB & Mongoose

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 📝 Daily Exercises

### Day 1 — MongoDB Basics
1. Set up a **MongoDB Atlas** cluster and connect via `mongosh`
2. Create a `students` collection and insert **10 documents** with name, age, grade, subjects array
3. Write queries using: `$gt`, `$in`, `$and`, `$or`, `$regex`
4. Practice `updateOne`, `updateMany` with `$set`, `$push`, `$pull`

### Day 2 — Mongoose Schema & Models
1. Create a **Book model** with: title (required, 3–200 chars), author (required), genre (enum: fiction, non-fiction, sci-fi, mystery, biography), pages (min 1), rating (0–5), published (Date), virtual `isLong` (pages > 300)
2. Add a **static method** `findByGenre(genre)` and **instance method** `getSummary()`
3. Add a `pre('save')` hook that logs "Saving book: {title}"

### Day 3 — CRUD Operations
1. Build a **Books API** with Express + Mongoose CRUD endpoints
2. Implement proper **validation error handling** (return field-level errors)
3. Add **query string filtering** by genre, rating, page count

### Day 4 — Advanced Queries
1. Add **pagination** (`?page=2&limit=5`) to your Books API
2. Write an **aggregation pipeline** that returns:
   - Average rating per genre
   - Total pages per genre
   - Count of books per genre
3. Create **compound indexes** for common queries

### Day 5 — Relationships
1. Add **Author** and **Publisher** models with relationships to Book
2. Implement `populate()` to include author and publisher details
3. Add **cascade delete** — when an author is deleted, update their books

---

## 🏆 Weekly Project: Database-Driven Notes App

Build the complete **Notes App** from Day 6, then add **3 extra features** from the bonus list.

### Requirements

| Category | Requirement |
|----------|-------------|
| **Models** | Note, Folder, Tag with proper schemas and validation |
| **Relationships** | Folder → Notes (one-to-many), Notes ↔ Tags (many-to-many) |
| **CRUD** | Full create, read, update, delete for all 3 resources |
| **Queries** | Filtering by category, search by title/content, sorting, pagination |
| **Virtuals** | At least 2 virtual fields on Note model |
| **Indexes** | Text index for search, compound index for common queries |
| **Aggregation** | Stats endpoint with aggregation pipeline |
| **Populate** | Folder and tags populated on note queries |
| **Error Handling** | Handle ValidationError, CastError, duplicate key |
| **Code Quality** | MVC architecture, clean code, ES Modules |

### Bonus Features (pick 3+)

- [ ] Full-text search with `$text` and relevance scoring
- [ ] Bulk operations (create/delete multiple notes)
- [ ] Note archiving & restore (soft delete)
- [ ] Folder nesting (parent folder references)
- [ ] Tag merge (combine two tags into one)
- [ ] Note export endpoint (JSON download)
- [ ] Database seeding script with sample data
- [ ] Aggregation: notes created per week chart data

---

## 📊 Grading Rubric

| Criteria | Points |
|----------|--------|
| **Models & Validation** — Schemas with types, required, enum, min/max, custom validators | 20 |
| **Relationships** — Correct references, populate, cascade cleanup | 15 |
| **CRUD Operations** — All endpoints work correctly for notes, folders, tags | 20 |
| **Advanced Queries** — Filtering, search, sort, pagination via query strings | 15 |
| **Aggregation** — Stats endpoint with meaningful aggregation pipeline | 10 |
| **Error Handling** — Mongoose-specific errors handled with clear messages | 10 |
| **Code Quality** — MVC, clean separation, consistent patterns, ES Modules | 10 |
| **Bonus Features** — 3+ extras from the list above | +15 |
| **Total** | **100 + 15 bonus** |

---

## 📅 Submission

- **Due**: End of Week 15
- **Format**: GitHub repository link
- **Include**: README with setup instructions and API endpoint documentation
- **Test**: Ensure all endpoints work with sample curl commands or Postman collection

---

[← Back to Week 15](README.md) | [Next Week →](../week-16/README.md)
