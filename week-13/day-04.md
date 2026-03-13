# 📅 Week 13 — Day 4: Routing & Request Handling 🛣️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Organize routes using Express Router
- Build modular route files
- Work with route parameters, query strings, and request bodies
- Implement input validation
- Structure a project with controllers and routes

---

## 📖 Lesson Content

### 1. Why Use Express Router?

Putting all routes in `index.js` quickly becomes messy. **Express Router** lets you split routes into separate files by resource:

```
routes/
├── todoRoutes.js     →  /api/todos/*
├── userRoutes.js     →  /api/users/*
└── healthRoutes.js   →  /health
```

### 2. Creating a Router

```js
// routes/todoRoutes.js
import { Router } from 'express';

const router = Router();

// All paths here are relative to the mount point
router.get('/', (req, res) => {
  res.json({ todos: [] });
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id });
});

router.post('/', (req, res) => {
  res.status(201).json({ message: 'Todo created' });
});

export default router;
```

### 3. Mounting Routes in the App

```js
// index.js
import express from 'express';
import todoRoutes from './routes/todoRoutes.js';
import userRoutes from './routes/userRoutes.js';

const app = express();

app.use(express.json());

// Mount route groups
app.use('/api/todos', todoRoutes);  // /api/todos, /api/todos/:id
app.use('/api/users', userRoutes);  // /api/users, /api/users/:id

app.listen(3000, () => console.log('Server running on port 3000'));
```

### 4. The Controller Pattern

Separate **route definitions** from **business logic**:

```js
// controllers/todoController.js
let todos = [];
let nextId = 1;

export function getAllTodos(req, res) {
  const { completed, search } = req.query;
  let result = [...todos];

  // Filter by completion status
  if (completed !== undefined) {
    result = result.filter(t => t.completed === (completed === 'true'));
  }

  // Search by title
  if (search) {
    result = result.filter(t =>
      t.title.toLowerCase().includes(search.toLowerCase())
    );
  }

  res.json({
    count: result.length,
    todos: result,
  });
}

export function getTodoById(req, res) {
  const todo = todos.find(t => t.id === Number(req.params.id));
  if (!todo) {
    return res.status(404).json({ error: 'Todo not found' });
  }
  res.json(todo);
}

export function createTodo(req, res) {
  const { title, priority } = req.body;

  if (!title || typeof title !== 'string' || title.trim().length === 0) {
    return res.status(400).json({ error: 'Title is required and must be a non-empty string' });
  }

  const todo = {
    id: nextId++,
    title: title.trim(),
    completed: false,
    priority: priority || 'medium',
    createdAt: new Date().toISOString(),
  };

  todos.push(todo);
  res.status(201).json(todo);
}

export function updateTodo(req, res) {
  const index = todos.findIndex(t => t.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: 'Todo not found' });
  }

  const { title, completed, priority } = req.body;

  if (title !== undefined) {
    if (typeof title !== 'string' || title.trim().length === 0) {
      return res.status(400).json({ error: 'Title must be a non-empty string' });
    }
    todos[index].title = title.trim();
  }
  if (completed !== undefined) todos[index].completed = Boolean(completed);
  if (priority !== undefined) todos[index].priority = priority;

  todos[index].updatedAt = new Date().toISOString();
  res.json(todos[index]);
}

export function deleteTodo(req, res) {
  const index = todos.findIndex(t => t.id === Number(req.params.id));
  if (index === -1) {
    return res.status(404).json({ error: 'Todo not found' });
  }

  todos.splice(index, 1);
  res.status(204).end();
}
```

### 5. Clean Route File

```js
// routes/todoRoutes.js
import { Router } from 'express';
import {
  getAllTodos,
  getTodoById,
  createTodo,
  updateTodo,
  deleteTodo,
} from '../controllers/todoController.js';

const router = Router();

router.get('/', getAllTodos);
router.get('/:id', getTodoById);
router.post('/', createTodo);
router.patch('/:id', updateTodo);
router.delete('/:id', deleteTodo);

export default router;
```

### 6. Route Chaining

```js
// Shorthand for multiple methods on the same path
router
  .route('/')
  .get(getAllTodos)
  .post(createTodo);

router
  .route('/:id')
  .get(getTodoById)
  .patch(updateTodo)
  .delete(deleteTodo);
```

### 7. Query Parameters for Filtering, Sorting & Pagination

```js
// GET /api/todos?completed=true&sort=createdAt&order=desc&page=1&limit=10

export function getAllTodos(req, res) {
  let result = [...todos];

  // Filter
  const { completed, priority, search } = req.query;
  if (completed !== undefined) {
    result = result.filter(t => t.completed === (completed === 'true'));
  }
  if (priority) {
    result = result.filter(t => t.priority === priority);
  }
  if (search) {
    result = result.filter(t =>
      t.title.toLowerCase().includes(search.toLowerCase())
    );
  }

  // Sort
  const { sort = 'createdAt', order = 'desc' } = req.query;
  result.sort((a, b) => {
    if (order === 'asc') return a[sort] > b[sort] ? 1 : -1;
    return a[sort] < b[sort] ? 1 : -1;
  });

  // Pagination
  const page = Math.max(1, parseInt(req.query.page) || 1);
  const limit = Math.min(100, Math.max(1, parseInt(req.query.limit) || 10));
  const startIndex = (page - 1) * limit;
  const paginatedResult = result.slice(startIndex, startIndex + limit);

  res.json({
    page,
    limit,
    total: result.length,
    totalPages: Math.ceil(result.length / limit),
    todos: paginatedResult,
  });
}
```

### 8. Input Validation Helper

```js
// utils/validate.js
export function validateTodo(data) {
  const errors = [];

  if (!data.title || typeof data.title !== 'string') {
    errors.push('Title is required and must be a string');
  } else if (data.title.trim().length < 2) {
    errors.push('Title must be at least 2 characters');
  } else if (data.title.trim().length > 200) {
    errors.push('Title must be 200 characters or less');
  }

  if (data.priority && !['low', 'medium', 'high'].includes(data.priority)) {
    errors.push('Priority must be low, medium, or high');
  }

  return errors;
}
```

Use it in the controller:

```js
import { validateTodo } from '../utils/validate.js';

export function createTodo(req, res) {
  const errors = validateTodo(req.body);
  if (errors.length > 0) {
    return res.status(400).json({ errors });
  }
  // ... create todo
}
```

### 9. Project Structure So Far

```
todo-api/
├── src/
│   ├── controllers/
│   │   └── todoController.js
│   ├── routes/
│   │   └── todoRoutes.js
│   ├── utils/
│   │   └── validate.js
│   └── app.js
├── index.js
├── package.json
├── .gitignore
└── README.md
```

---

## 💪 Hands-On Exercise

### Build a Products API with Router + Controller

1. Create `routes/productRoutes.js` and `controllers/productController.js`
2. Implement full CRUD: GET all, GET by id, POST, PATCH, DELETE
3. Add query params: `?category=electronics&sort=price&order=asc&page=1&limit=5`
4. Add input validation for name (required, 2-100 chars) and price (positive number)
5. Mount routes at `/api/products`

---

## 📝 Homework

1. Add a `GET /api/todos/stats` route that returns `{ total, completed, pending, byPriority: { low, medium, high } }`
2. Add route chaining with `.route()` syntax
3. Create a `userRoutes.js` with basic CRUD and mount at `/api/users`
4. Add `PATCH /api/todos/:id/toggle` that toggles the completed status

---

## 💡 Pro Tips

- Keep route files thin — they just map URLs to controller functions
- Controllers hold the logic — validation, data manipulation, response
- Always validate and sanitize input — never trust `req.body` or `req.params`
- Use `Number()` to convert route params, and check for `NaN`
- Pagination defaults should be sensible (e.g., page 1, limit 10, max 100)

---

[← Day 3](day-03.md) | [Back to Week 13](README.md) | [Day 5 →](day-05.md)
