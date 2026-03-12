# 📅 Day 5: Local Storage & Data Persistence 💾

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- DOM manipulation and events (Days 1-4)
- JavaScript objects and JSON

---

## 🎯 Today's Goals

- 💾 Understand Web Storage API (localStorage & sessionStorage)
- 🔄 Store and retrieve complex data with JSON
- 📦 Build a data persistence layer for web apps
- 🎨 Implement theme persistence and user preferences

---

## 📘 Lesson Content

### Web Storage API

```javascript
// localStorage — persists across browser sessions
localStorage.setItem("key", "value");
const value = localStorage.getItem("key");
localStorage.removeItem("key");
localStorage.clear(); // removes all items

// sessionStorage — cleared when tab/window closes
sessionStorage.setItem("key", "value");
```

### Storing Complex Data with JSON

```javascript
// localStorage only stores strings!
// ❌ This stores "[object Object]"
localStorage.setItem("user", { name: "Ali" });

// ✅ Convert to JSON string
const user = { name: "Ali", age: 25, skills: ["HTML", "CSS", "JS"] };
localStorage.setItem("user", JSON.stringify(user));

// ✅ Parse back to object
const stored = JSON.parse(localStorage.getItem("user"));
console.log(stored.name); // "Ali"
```

### Safe Storage Helper

```javascript
const Storage = {
  get(key, defaultValue = null) {
    const item = localStorage.getItem(key);
    if (item === null) return defaultValue;
    try {
      return JSON.parse(item);
    } catch {
      return item; // return as string if not valid JSON
    }
  },

  set(key, value) {
    localStorage.setItem(key, JSON.stringify(value));
  },

  remove(key) {
    localStorage.removeItem(key);
  },

  has(key) {
    return localStorage.getItem(key) !== null;
  }
};

// Usage
Storage.set("todos", [{ id: 1, text: "Learn JS", done: false }]);
const todos = Storage.get("todos", []);
```

### Theme Persistence

```javascript
const themeToggle = document.getElementById("themeToggle");

// Load saved theme
const savedTheme = localStorage.getItem("theme") || "light";
document.documentElement.setAttribute("data-theme", savedTheme);

// Toggle theme
themeToggle.addEventListener("click", () => {
  const current = document.documentElement.getAttribute("data-theme");
  const next = current === "light" ? "dark" : "light";

  document.documentElement.setAttribute("data-theme", next);
  localStorage.setItem("theme", next);
});
```

### Storage Events (Cross-Tab Communication)

```javascript
// Fires in OTHER tabs/windows when storage changes
window.addEventListener("storage", (e) => {
  console.log("Key:", e.key);
  console.log("Old value:", e.oldValue);
  console.log("New value:", e.newValue);
  console.log("URL:", e.url);

  // Sync theme across tabs
  if (e.key === "theme") {
    document.documentElement.setAttribute("data-theme", e.newValue);
  }
});
```

### CRUD Operations with localStorage

```javascript
class TodoStore {
  constructor(storageKey = "todos") {
    this.key = storageKey;
  }

  getAll() {
    return JSON.parse(localStorage.getItem(this.key) || "[]");
  }

  save(todos) {
    localStorage.setItem(this.key, JSON.stringify(todos));
  }

  add(text) {
    const todos = this.getAll();
    const newTodo = {
      id: Date.now(),
      text,
      completed: false,
      createdAt: new Date().toISOString()
    };
    todos.push(newTodo);
    this.save(todos);
    return newTodo;
  }

  toggle(id) {
    const todos = this.getAll();
    const todo = todos.find(t => t.id === id);
    if (todo) {
      todo.completed = !todo.completed;
      this.save(todos);
    }
    return todo;
  }

  remove(id) {
    const todos = this.getAll().filter(t => t.id !== id);
    this.save(todos);
  }

  update(id, newText) {
    const todos = this.getAll();
    const todo = todos.find(t => t.id === id);
    if (todo) {
      todo.text = newText;
      this.save(todos);
    }
    return todo;
  }
}
```

---

## 🛠️ Hands-On Exercise

### Notes App with Persistence

Build a notes app that saves to localStorage:

```javascript
const store = new TodoStore("notes");

function renderNotes() {
  const list = document.getElementById("notesList");
  const notes = store.getAll();

  list.innerHTML = notes.map(note => `
    <div class="note" data-id="${note.id}">
      <p class="note__text">${escapeHtml(note.text)}</p>
      <div class="note__meta">
        <time>${new Date(note.createdAt).toLocaleDateString()}</time>
        <button class="btn-delete" aria-label="Delete note">🗑️</button>
      </div>
    </div>
  `).join("");
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

// Add note
document.getElementById("noteForm").addEventListener("submit", (e) => {
  e.preventDefault();
  const input = e.target.elements.noteText;
  if (input.value.trim()) {
    store.add(input.value.trim());
    input.value = "";
    renderNotes();
  }
});

// Delete note (delegation)
document.getElementById("notesList").addEventListener("click", (e) => {
  if (e.target.closest(".btn-delete")) {
    const noteEl = e.target.closest(".note");
    const id = Number(noteEl.dataset.id);
    store.remove(id);
    renderNotes();
  }
});

// Initial render
renderNotes();
```

---

## 📝 Homework

1. **User Preferences Panel:** Build a settings page (theme, font size, language) that persists across sessions
2. **Shopping Cart:** Store cart items in localStorage — add, remove, update quantity, calculate total
3. **Form Auto-save:** Auto-save form inputs every 2 seconds, restore on page load, clear on submit
4. **Export/Import:** Add buttons to export notes as JSON file and import from a JSON file

---

## 💡 Pro Tips

> 📏 **Size Limit:** localStorage has a ~5MB limit per origin. Don't store large amounts of data — use IndexedDB for anything beyond basic settings.

> 🔒 **Security:** Never store sensitive data (passwords, tokens, personal info) in localStorage. It's accessible to any JavaScript on the page.

> 🔄 **Sync Across Tabs:** The `storage` event lets you keep multiple tabs in sync — great for themes, auth state, and real-time updates.

---

[← Day 4](day-04.md) | [Back to Week 6](README.md) | [Day 6 →](day-06.md)
