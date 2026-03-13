# 📋 Week 13 Assignments — Node.js & Express.js Fundamentals

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 📝 Daily Exercises

### Day 1 — Node.js Runtime
- [ ] Create a Node.js script that reads a text file and counts the number of words, lines, and characters
- [ ] Build a command-line tool that accepts arguments (`process.argv`) to perform basic math operations (add, subtract, multiply, divide)
- [ ] Create a simple HTTP server that returns different responses based on the URL path

### Day 2 — Modules & npm
- [ ] Build a modular "string utils" package with functions: `capitalize`, `slugify`, `truncate`, `countWords`
- [ ] Create a project that uses at least 3 npm packages of your choice (chalk, inquirer, figlet, etc.)
- [ ] Write a `package.json` with at least 3 custom npm scripts

### Day 3 — Express.js Basics
- [ ] Build an Express server with at least 5 different routes using GET, POST, PUT, DELETE
- [ ] Create a "Quotes API" — store quotes in an array, support full CRUD
- [ ] Test all endpoints using Postman or curl, screenshot the responses

### Day 4 — Routing & Controllers
- [ ] Refactor your Quotes API into separate route and controller files
- [ ] Add query parameter support: filter by author, search by keyword, sort alphabetically
- [ ] Implement pagination with `page` and `limit` query parameters

### Day 5 — Middleware & Error Handling
- [ ] Add `helmet`, `cors`, `morgan` to your API
- [ ] Create a custom validation middleware for the quote creation endpoint
- [ ] Implement `AppError` class and centralized error handling — test with invalid IDs and missing fields
- [ ] Create a rate-limiting middleware (bonus)

---

## 🏆 Weekly Project: REST API — To-Do Application

### Project Requirements

Build a **complete To-Do REST API** from scratch demonstrating all Week 13 concepts.

### Core Features

| Feature | Points |
|---------|--------|
| Project setup (package.json, ES modules, dotenv, .gitignore) | 10 |
| Express server with proper middleware pipeline (helmet, cors, morgan) | 10 |
| Full CRUD endpoints (GET all, GET by ID, POST, PUT, DELETE) | 20 |
| MVC architecture (controllers, routes, middleware separated) | 15 |
| Input validation middleware with descriptive errors | 10 |
| Custom error class (AppError) + centralized error handler | 10 |
| Filtering by status, priority, category | 10 |
| Sorting and pagination | 10 |
| Clean code, comments, and README documentation | 5 |

**Total: 100 points**

### Bonus Features (Extra Credit)

| Feature | Points |
|---------|--------|
| Toggle endpoint (`PATCH /:id/toggle`) | +5 |
| Stats endpoint with aggregated data | +5 |
| Rate-limiting middleware | +5 |
| Request ID middleware | +3 |
| Search by keyword in title | +2 |

### API Endpoints

Your API should support these endpoints:

```
GET    /health              → Health check
GET    /api/todos           → List all (with filters, sort, pagination)
GET    /api/todos/stats     → Statistics
GET    /api/todos/:id       → Get one
POST   /api/todos           → Create
PUT    /api/todos/:id       → Update
DELETE /api/todos/:id       → Delete
PATCH  /api/todos/:id/toggle → Toggle completion
```

### Grading Rubric

| Grade | Score | Description |
|-------|-------|-------------|
| A+ | 90-100+ | All core features + bonus, clean architecture |
| A | 80-89 | All core features, well-structured code |
| B | 70-79 | Most core features working, minor issues |
| C | 60-69 | Basic CRUD works, missing some features |
| F | < 60 | Incomplete or major issues |

### Submission

1. Push your project to a GitHub repository
2. Include a `README.md` with:
   - Project description
   - Setup instructions (npm install, .env setup)
   - API endpoints documentation
   - Example curl/Postman commands
3. Submit your GitHub repository link

---

## 📚 Resources

- [Node.js Documentation](https://nodejs.org/docs/latest/api/)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [MDN — HTTP Basics](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- [Postman Learning Center](https://learning.postman.com/)
- [npm Documentation](https://docs.npmjs.com/)

---

[Back to Week 13](README.md) | [Next Week →](../week-14/README.md)
