# 📅 Week 13 — Day 3: Introduction to Express.js 🌐

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand what Express.js is and why we use it
- Create an Express application
- Define routes for different HTTP methods
- Send various response types (JSON, HTML, status codes)
- Use `nodemon` for automatic restarts
- Understand the request-response cycle

---

## 📖 Lesson Content

### 1. What Is Express.js?

Express is a **minimal, fast web framework** for Node.js. It wraps the built-in `http` module with a clean API for:

- Routing (mapping URLs to handlers)
- Middleware (reusable request processing)
- Request/response helpers
- Static file serving

```bash
npm install express
```

### 2. Your First Express Server

```js
// index.js
import express from 'express';

const app = express();
const PORT = 3000;

app.get('/', (req, res) => {
  res.json({ message: 'Welcome to our API!' });
});

app.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

Run it:

```bash
node index.js
# or with auto-restart:
npx nodemon index.js
```

Visit `http://localhost:3000` in your browser or use:

```bash
curl http://localhost:3000
```

### 3. The Request-Response Cycle

```
Client (Browser/Postman)
    │
    │  HTTP Request (GET /api/todos)
    ▼
Express Server
    │
    ├── Finds matching route
    ├── Runs route handler
    │
    │  HTTP Response (200 OK + JSON)
    ▼
Client receives response
```

### 4. HTTP Methods & Routes

```js
// GET — retrieve data
app.get('/api/todos', (req, res) => {
  res.json({ todos: [] });
});

// POST — create data
app.post('/api/todos', (req, res) => {
  res.status(201).json({ message: 'Todo created' });
});

// PUT — replace entire resource
app.put('/api/todos/:id', (req, res) => {
  res.json({ message: `Todo ${req.params.id} replaced` });
});

// PATCH — partial update
app.patch('/api/todos/:id', (req, res) => {
  res.json({ message: `Todo ${req.params.id} updated` });
});

// DELETE — remove data
app.delete('/api/todos/:id', (req, res) => {
  res.json({ message: `Todo ${req.params.id} deleted` });
});
```

### 5. The Request Object (`req`)

```js
app.get('/api/search', (req, res) => {
  // Query parameters: /api/search?q=node&limit=10
  console.log(req.query);          // { q: 'node', limit: '10' }
  console.log(req.query.q);        // 'node'

  // HTTP method
  console.log(req.method);         // 'GET'

  // Full URL
  console.log(req.originalUrl);    // '/api/search?q=node&limit=10'

  // Headers
  console.log(req.headers['user-agent']);

  res.json({ query: req.query });
});

app.get('/api/users/:userId', (req, res) => {
  // Route parameters: /api/users/42
  console.log(req.params);         // { userId: '42' }
  console.log(req.params.userId);  // '42'

  res.json({ userId: req.params.userId });
});
```

### 6. The Response Object (`res`)

```js
// JSON response
app.get('/api/data', (req, res) => {
  res.json({ name: 'Ali', role: 'developer' });
});

// Status code + JSON
app.post('/api/items', (req, res) => {
  res.status(201).json({ message: 'Created', id: 1 });
});

// Plain text
app.get('/health', (req, res) => {
  res.send('OK');
});

// HTML
app.get('/page', (req, res) => {
  res.send('<h1>Hello from Express</h1>');
});

// Set custom headers
app.get('/api/download', (req, res) => {
  res.set('X-Custom-Header', 'custom-value');
  res.json({ file: 'report.pdf' });
});

// Redirect
app.get('/old-page', (req, res) => {
  res.redirect(301, '/new-page');
});

// 404
app.get('/api/missing', (req, res) => {
  res.status(404).json({ error: 'Resource not found' });
});
```

### 7. HTTP Status Codes Quick Reference

| Code | Meaning | When to Use |
|------|---------|-------------|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | Successful POST (resource created) |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Invalid input / validation error |
| `401` | Unauthorized | Not authenticated |
| `403` | Forbidden | Authenticated but not allowed |
| `404` | Not Found | Resource doesn't exist |
| `409` | Conflict | Duplicate resource |
| `500` | Internal Server Error | Unexpected server error |

### 8. Parsing Request Body

Express doesn't parse JSON bodies by default — you need middleware:

```js
import express from 'express';

const app = express();

// Parse JSON request bodies
app.use(express.json());

// Parse URL-encoded form data
app.use(express.urlencoded({ extended: true }));

app.post('/api/todos', (req, res) => {
  console.log(req.body);  // { title: 'Learn Express', completed: false }

  const { title } = req.body;

  if (!title) {
    return res.status(400).json({ error: 'Title is required' });
  }

  res.status(201).json({
    message: 'Todo created',
    todo: { id: 1, title, completed: false },
  });
});
```

### 9. Testing with Postman / Thunder Client

You can't send POST/PUT/DELETE from a browser URL bar. Use:

- **Postman** — desktop app for API testing
- **Thunder Client** — VS Code extension (recommended)
- **curl** — command line

```bash
# GET
curl http://localhost:3000/api/todos

# POST with JSON body
curl -X POST http://localhost:3000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"title": "Learn Express"}'
```

### 10. Complete Example — Simple Notes API

```js
// index.js
import express from 'express';

const app = express();
const PORT = 3000;

// Middleware
app.use(express.json());

// In-memory data store
let notes = [
  { id: 1, title: 'Learn Node.js', body: 'Understand the runtime' },
  { id: 2, title: 'Learn Express', body: 'Build web servers' },
];
let nextId = 3;

// GET all notes
app.get('/api/notes', (req, res) => {
  res.json(notes);
});

// GET single note
app.get('/api/notes/:id', (req, res) => {
  const note = notes.find(n => n.id === Number(req.params.id));
  if (!note) {
    return res.status(404).json({ error: 'Note not found' });
  }
  res.json(note);
});

// POST create note
app.post('/api/notes', (req, res) => {
  const { title, body } = req.body;
  if (!title) {
    return res.status(400).json({ error: 'Title is required' });
  }

  const note = { id: nextId++, title, body: body || '' };
  notes.push(note);
  res.status(201).json(note);
});

// PUT replace note
app.put('/api/notes/:id', (req, res) => {
  const index = notes.findIndex(n => n.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: 'Note not found' });
  }

  const { title, body } = req.body;
  if (!title) {
    return res.status(400).json({ error: 'Title is required' });
  }

  notes[index] = { ...notes[index], title, body: body || '' };
  res.json(notes[index]);
});

// DELETE note
app.delete('/api/notes/:id', (req, res) => {
  const index = notes.findIndex(n => n.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: 'Note not found' });
  }

  notes.splice(index, 1);
  res.status(204).end();
});

app.listen(PORT, () => {
  console.log(`Notes API running at http://localhost:${PORT}`);
});
```

---

## 💪 Hands-On Exercise

### Build a Bookshelf API

1. Create a new Express project with `npm init -y`
2. Install `express` and set up `nodemon`
3. Create an in-memory array of books: `{ id, title, author, year }`
4. Implement these routes:
   - `GET /api/books` — list all books
   - `GET /api/books/:id` — get one book
   - `POST /api/books` — add a book (validate title + author)
   - `PUT /api/books/:id` — update a book
   - `DELETE /api/books/:id` — delete a book
5. Test all endpoints with Thunder Client or curl

---

## 📝 Homework

1. Add a `GET /api/books/search?q=...` route that searches by title or author
2. Add a `PATCH /api/books/:id` route that partially updates a book
3. Return proper status codes for every response (201, 204, 400, 404)
4. Add a `GET /` route that returns an HTML welcome page

---

## 💡 Pro Tips

- Always call `app.use(express.json())` before your routes — otherwise `req.body` is `undefined`
- Use `res.status(code).json(data)` — set status first, then send JSON
- Route parameters (`:id`) are always strings — convert with `Number()` when comparing
- `res.status(204).end()` — for DELETE, 204 means "success, no content to return"
- Install the **Thunder Client** VS Code extension for quick API testing

---

[← Day 2](day-02.md) | [Back to Week 13](README.md) | [Day 4 →](day-04.md)
