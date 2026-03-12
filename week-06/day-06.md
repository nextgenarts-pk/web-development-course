# 📅 Day 6: Project Day — Interactive To-Do App ✅

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- All DOM and Events concepts from Days 1-5
- localStorage for data persistence

---

## 🎯 Today's Goals

- 🏗️ Build a complete To-Do App from scratch
- ✅ Implement add, complete, edit, delete functionality
- 🔍 Add filtering (All, Active, Completed)
- 💾 Persist data with localStorage
- 🎨 Polish with CSS animations

---

## 📘 Project: Interactive To-Do App

### HTML Structure

```html
<!DOCTYPE html>
<html lang="en" data-theme="light">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Todo App — NextGen Arts</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="app">
    <header class="app__header">
      <h1>📝 Todo App</h1>
      <button id="themeToggle" class="btn-icon" aria-label="Toggle theme">🌓</button>
    </header>

    <form id="todoForm" class="todo-form">
      <input
        type="text"
        id="todoInput"
        class="todo-input"
        placeholder="What needs to be done?"
        autocomplete="off"
        required
      >
      <button type="submit" class="btn btn--primary">Add</button>
    </form>

    <nav class="filters">
      <button class="filter-btn active" data-filter="all">All</button>
      <button class="filter-btn" data-filter="active">Active</button>
      <button class="filter-btn" data-filter="completed">Completed</button>
    </nav>

    <ul id="todoList" class="todo-list"></ul>

    <footer class="app__footer" id="footer">
      <span id="itemCount">0 items left</span>
      <button id="clearCompleted" class="btn-text">Clear Completed</button>
    </footer>
  </div>

  <script src="app.js"></script>
</body>
</html>
```

### CSS Styling

```css
:root {
  --bg: #f5f5f5;
  --surface: #ffffff;
  --text: #333333;
  --text-muted: #888888;
  --primary: #3498db;
  --danger: #e74c3c;
  --success: #2ecc71;
  --border: #e0e0e0;
  --shadow: 0 2px 10px rgba(0,0,0,0.1);
}

[data-theme="dark"] {
  --bg: #1a1a2e;
  --surface: #16213e;
  --text: #e0e0e0;
  --text-muted: #888;
  --border: #2a2a4a;
  --shadow: 0 2px 10px rgba(0,0,0,0.3);
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: 'Segoe UI', sans-serif;
  background: var(--bg);
  color: var(--text);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  padding: 2rem;
}

.app {
  width: 100%;
  max-width: 550px;
}

.app__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
}

.todo-form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.todo-input {
  flex: 1;
  padding: 0.8rem 1rem;
  border: 2px solid var(--border);
  border-radius: 8px;
  font-size: 1rem;
  background: var(--surface);
  color: var(--text);
  transition: border-color 0.3s;
}

.todo-input:focus {
  outline: none;
  border-color: var(--primary);
}

.todo-list {
  list-style: none;
}

.todo-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.8rem 1rem;
  background: var(--surface);
  border-radius: 8px;
  margin-bottom: 0.5rem;
  box-shadow: var(--shadow);
  animation: slideIn 0.3s ease;
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: var(--text-muted);
}

.todo-text {
  flex: 1;
  font-size: 1rem;
}

.filters {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.filter-btn {
  padding: 0.4rem 1rem;
  border: 1px solid var(--border);
  border-radius: 20px;
  background: transparent;
  color: var(--text);
  cursor: pointer;
}

.filter-btn.active {
  background: var(--primary);
  color: white;
  border-color: var(--primary);
}

.app__footer {
  display: flex;
  justify-content: space-between;
  padding: 1rem 0;
  color: var(--text-muted);
  font-size: 0.9rem;
}

@keyframes slideIn {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes fadeOut {
  to { opacity: 0; transform: translateX(50px); }
}
```

### JavaScript — Complete App Logic

```javascript
// ===== State Management =====
const state = {
  todos: JSON.parse(localStorage.getItem("todos") || "[]"),
  filter: "all"
};

function saveTodos() {
  localStorage.setItem("todos", JSON.stringify(state.todos));
}

// ===== DOM References =====
const form = document.getElementById("todoForm");
const input = document.getElementById("todoInput");
const list = document.getElementById("todoList");
const itemCount = document.getElementById("itemCount");
const clearCompletedBtn = document.getElementById("clearCompleted");
const themeToggle = document.getElementById("themeToggle");

// ===== Render =====
function render() {
  const filtered = state.todos.filter(todo => {
    if (state.filter === "active") return !todo.completed;
    if (state.filter === "completed") return todo.completed;
    return true;
  });

  list.innerHTML = filtered.map(todo => `
    <li class="todo-item ${todo.completed ? "completed" : ""}" data-id="${todo.id}">
      <input type="checkbox" class="todo-checkbox" ${todo.completed ? "checked" : ""}>
      <span class="todo-text">${escapeHtml(todo.text)}</span>
      <button class="btn-icon btn-edit" aria-label="Edit">✏️</button>
      <button class="btn-icon btn-delete" aria-label="Delete">🗑️</button>
    </li>
  `).join("");

  // Update counter
  const active = state.todos.filter(t => !t.completed).length;
  itemCount.textContent = `${active} item${active !== 1 ? "s" : ""} left`;

  // Show/hide footer
  document.getElementById("footer").style.display =
    state.todos.length ? "flex" : "none";
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

// ===== Add Todo =====
form.addEventListener("submit", (e) => {
  e.preventDefault();
  const text = input.value.trim();
  if (!text) return;

  state.todos.push({
    id: Date.now(),
    text,
    completed: false
  });

  saveTodos();
  render();
  input.value = "";
  input.focus();
});

// ===== Event Delegation for List Actions =====
list.addEventListener("click", (e) => {
  const item = e.target.closest(".todo-item");
  if (!item) return;
  const id = Number(item.dataset.id);

  // Toggle complete
  if (e.target.closest(".todo-checkbox")) {
    const todo = state.todos.find(t => t.id === id);
    if (todo) todo.completed = !todo.completed;
    saveTodos();
    render();
  }

  // Delete
  if (e.target.closest(".btn-delete")) {
    state.todos = state.todos.filter(t => t.id !== id);
    item.style.animation = "fadeOut 0.3s ease forwards";
    item.addEventListener("animationend", () => {
      saveTodos();
      render();
    });
  }

  // Edit
  if (e.target.closest(".btn-edit")) {
    const todo = state.todos.find(t => t.id === id);
    if (!todo) return;
    const newText = prompt("Edit todo:", todo.text);
    if (newText !== null && newText.trim()) {
      todo.text = newText.trim();
      saveTodos();
      render();
    }
  }
});

// ===== Filters =====
document.querySelector(".filters").addEventListener("click", (e) => {
  const btn = e.target.closest(".filter-btn");
  if (!btn) return;

  document.querySelectorAll(".filter-btn").forEach(b => b.classList.remove("active"));
  btn.classList.add("active");
  state.filter = btn.dataset.filter;
  render();
});

// ===== Clear Completed =====
clearCompletedBtn.addEventListener("click", () => {
  state.todos = state.todos.filter(t => !t.completed);
  saveTodos();
  render();
});

// ===== Theme Toggle =====
const savedTheme = localStorage.getItem("theme") || "light";
document.documentElement.setAttribute("data-theme", savedTheme);

themeToggle.addEventListener("click", () => {
  const current = document.documentElement.getAttribute("data-theme");
  const next = current === "light" ? "dark" : "light";
  document.documentElement.setAttribute("data-theme", next);
  localStorage.setItem("theme", next);
});

// ===== Initial Render =====
render();
```

---

## 📝 Homework / Enhancements

1. **Drag & Drop:** Reorder todos by dragging them (use `dragstart`, `dragover`, `drop` events)
2. **Due Dates:** Add optional due dates with a date picker; highlight overdue items
3. **Categories:** Add color-coded categories/tags to todos
4. **Inline Editing:** Double-click a todo to edit it in place (instead of using `prompt`)
5. **Undo Delete:** Show a brief "Undo" toast after deleting a todo

---

## 💡 Pro Tips

> 🏗️ **State-driven rendering:** Always modify the data (state) first, then re-render the UI. Never modify the DOM directly and try to sync state afterward.

> 🎭 **Animations:** Use CSS animations for adding/removing elements. Add the animation class, listen for `animationend`, then remove the element.

> 💾 **Single source of truth:** localStorage is the persistence layer, the `state` object is the runtime truth. Always save after mutations.

---

[← Day 5](day-05.md) | [Back to Week 6](README.md)
