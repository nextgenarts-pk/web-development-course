# 📋 Week 14 Assignments — RESTful API Design & Development

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 📝 Daily Exercises

### Day 1 — REST Principles & API Design
- [ ] Design a complete API for an e-commerce system (Products, Orders, Customers) — endpoints, methods, status codes
- [ ] Set up the Blog API project scaffold with MVC folder structure
- [ ] Implement `GET /api/posts` with sample data returning the consistent response format

### Day 2 — Advanced CRUD Patterns
- [ ] Implement PUT (full replace) and PATCH (partial update) for posts
- [ ] Add soft delete with `DELETE /api/posts/:id` and `PATCH /api/posts/:id/restore`
- [ ] Create Comment model and nested routes: `GET/POST /api/posts/:id/comments`
- [ ] Add `commentCount` to post list responses

### Day 3 — File Uploads & Static Files
- [ ] Set up Multer with disk storage and file type validation (JPEG/PNG only, 5MB max)
- [ ] Add `POST /api/posts/:id/cover` for cover image uploads
- [ ] Serve uploaded files via `express.static`
- [ ] Handle Multer errors with descriptive messages

### Day 4 — API Documentation & Testing
- [ ] Set up Swagger with `swagger-jsdoc` and `swagger-ui-express`
- [ ] Document at least 4 endpoints with `@swagger` annotations
- [ ] Write at least 8 tests with Supertest (CRUD + validation + error cases)
- [ ] Run tests and ensure all pass

### Day 5 — API Versioning & Best Practices
- [ ] Add rate limiting (100 req/15min)
- [ ] Enable response compression
- [ ] Add `Cache-Control` headers for GET requests
- [ ] Implement graceful shutdown handling

---

## 🏆 Weekly Project: Blog API with Full CRUD

### Project Requirements

Build a **complete Blog REST API** with posts, comments, and categories.

### Core Features

| Feature | Points |
|---------|--------|
| Project setup (ES modules, dotenv, proper dependencies) | 5 |
| MVC architecture (models, controllers, routes separated) | 10 |
| Post CRUD — Create, Read (list + single), Update (PATCH), Delete | 20 |
| Comment CRUD — nested under posts (GET/POST/DELETE) | 15 |
| Category CRUD (GET/POST/PATCH/DELETE) | 10 |
| Input validation with descriptive error messages | 10 |
| Error handling (AppError, 404, centralized handler) | 10 |
| Filtering, sorting, pagination on post listing | 10 |
| Production middleware (helmet, cors, morgan, compression) | 5 |
| Clean code, README with API documentation | 5 |

**Total: 100 points**

### Bonus Features (Extra Credit)

| Feature | Points |
|---------|--------|
| File uploads — cover image for posts (Multer) | +10 |
| Swagger API documentation | +10 |
| Automated tests with Supertest (8+ tests) | +10 |
| Soft delete with restore endpoint | +5 |
| Rate limiting | +3 |
| Post statistics endpoint | +2 |

### Grading Rubric

| Grade | Score | Description |
|-------|-------|-------------|
| A+ | 90-100+ | All core + multiple bonus features, clean architecture |
| A | 80-89 | All core features, well-organized code |
| B | 70-79 | Most core features working |
| C | 60-69 | Basic post CRUD works, missing relationships |
| F | < 60 | Incomplete or broken |

### Submission

1. Push to GitHub repository
2. Include `README.md` with:
   - Project overview and features
   - Setup instructions
   - Complete API endpoint table
   - Example requests (curl or Postman screenshots)
3. Include Postman collection export (if available)
4. Submit repository link

---

## 📚 Resources

- [RESTful API Design Guide](https://restfulapi.net/)
- [Express.js Documentation](https://expressjs.com/)
- [Multer Documentation](https://github.com/expressjs/multer)
- [Swagger/OpenAPI Docs](https://swagger.io/docs/)
- [Supertest GitHub](https://github.com/ladjs/supertest)
- [HTTP Status Codes](https://httpstatuses.com/)

---

[Back to Week 14](README.md) | [Next Week →](../week-15/README.md)
