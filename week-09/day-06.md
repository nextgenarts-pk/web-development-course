# 📅 Day 6: Project Day — React To-Do App ✅

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Everything from Days 1-5 (JSX, components, props, lists, styling)
- JavaScript arrays and DOM events

---

## 🎯 Today's Goals

- ✅ Build a complete **React To-Do App** from scratch
- 🧩 Practice component composition and props
- 🎨 Style the app with Tailwind CSS
- 📋 Implement add, display, filter, and delete functionality

---

## 📘 Project Overview

### What We're Building

A clean, styled To-Do application featuring:
- **Add tasks** with an input form
- **Display tasks** in a list with completion status
- **Toggle completion** on click
- **Delete tasks** with a button
- **Filter tasks** (All / Active / Completed)
- **Task counter** showing remaining items

> **Note:** We'll use props and basic event handlers today. In Week 10, we'll refactor this with `useState` and full state management!

---

## 🏗️ Project Structure

```
react-todo-app/
├── src/
│   ├── components/
│   │   ├── TodoForm.jsx
│   │   ├── TodoList.jsx
│   │   ├── TodoItem.jsx
│   │   ├── TodoFilter.jsx
│   │   └── TodoStats.jsx
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── index.html
└── package.json
```

### Step 1: Setup

```bash
npm create vite@latest react-todo-app -- --template react
cd react-todo-app
npm install
npm install -D tailwindcss @tailwindcss/vite
```

```js
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

```css
/* src/index.css */
@import "tailwindcss";
```

### Step 2: TodoItem Component

```jsx
// src/components/TodoItem.jsx
export default function TodoItem({ todo, onToggle, onDelete }) {
  return (
    <li className="flex items-center justify-between p-3 bg-white rounded-lg shadow-sm hover:shadow-md transition-shadow group">
      <div className="flex items-center gap-3 flex-1">
        <button
          onClick={() => onToggle(todo.id)}
          className={`w-6 h-6 rounded-full border-2 flex items-center justify-center transition-colors ${
            todo.completed
              ? "bg-green-500 border-green-500 text-white"
              : "border-gray-300 hover:border-green-400"
          }`}
        >
          {todo.completed && "✓"}
        </button>
        <span
          className={`flex-1 ${
            todo.completed ? "line-through text-gray-400" : "text-gray-800"
          }`}
        >
          {todo.text}
        </span>
      </div>
      <button
        onClick={() => onDelete(todo.id)}
        className="text-red-400 hover:text-red-600 opacity-0 group-hover:opacity-100 transition-opacity text-xl"
        title="Delete task"
      >
        ×
      </button>
    </li>
  );
}
```

### Step 3: TodoList Component

```jsx
// src/components/TodoList.jsx
import TodoItem from "./TodoItem";

export default function TodoList({ todos, onToggle, onDelete }) {
  if (todos.length === 0) {
    return (
      <div className="text-center py-10 text-gray-400">
        <p className="text-4xl mb-2">📝</p>
        <p>No tasks yet. Add one above!</p>
      </div>
    );
  }

  return (
    <ul className="space-y-2">
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onToggle={onToggle}
          onDelete={onDelete}
        />
      ))}
    </ul>
  );
}
```

### Step 4: TodoForm Component

```jsx
// src/components/TodoForm.jsx
export default function TodoForm({ inputValue, onInputChange, onSubmit }) {
  return (
    <form onSubmit={onSubmit} className="flex gap-2 mb-6">
      <input
        type="text"
        value={inputValue}
        onChange={onInputChange}
        placeholder="What needs to be done?"
        className="flex-1 px-4 py-3 rounded-lg border border-gray-200 focus:outline-none focus:ring-2 focus:ring-red-500 focus:border-transparent text-gray-800"
      />
      <button
        type="submit"
        className="px-6 py-3 bg-red-600 text-white rounded-lg hover:bg-red-700 transition-colors font-medium"
      >
        Add
      </button>
    </form>
  );
}
```

### Step 5: TodoFilter Component

```jsx
// src/components/TodoFilter.jsx
export default function TodoFilter({ currentFilter, onFilterChange }) {
  const filters = ["all", "active", "completed"];

  return (
    <div className="flex gap-2 mb-4">
      {filters.map(filter => (
        <button
          key={filter}
          onClick={() => onFilterChange(filter)}
          className={`px-4 py-1.5 rounded-full text-sm font-medium transition-colors capitalize ${
            currentFilter === filter
              ? "bg-red-600 text-white"
              : "bg-gray-100 text-gray-600 hover:bg-gray-200"
          }`}
        >
          {filter}
        </button>
      ))}
    </div>
  );
}
```

### Step 6: TodoStats Component

```jsx
// src/components/TodoStats.jsx
export default function TodoStats({ todos, onClearCompleted }) {
  const total = todos.length;
  const completed = todos.filter(t => t.completed).length;
  const active = total - completed;

  return (
    <div className="flex items-center justify-between text-sm text-gray-500 mt-4 pt-4 border-t border-gray-100">
      <span>{active} {active === 1 ? "item" : "items"} left</span>
      <span>{completed} completed</span>
      {completed > 0 && (
        <button
          onClick={onClearCompleted}
          className="text-red-500 hover:text-red-700 transition-colors"
        >
          Clear completed
        </button>
      )}
    </div>
  );
}
```

### Step 7: App Component (Putting It All Together)

```jsx
// src/App.jsx
import { useState } from "react";
import TodoForm from "./components/TodoForm";
import TodoList from "./components/TodoList";
import TodoFilter from "./components/TodoFilter";
import TodoStats from "./components/TodoStats";

function App() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React fundamentals", completed: true },
    { id: 2, text: "Build a todo app", completed: false },
    { id: 3, text: "Master React hooks", completed: false }
  ]);
  const [input, setInput] = useState("");
  const [filter, setFilter] = useState("all");

  const handleSubmit = (e) => {
    e.preventDefault();
    const text = input.trim();
    if (!text) return;

    setTodos([
      ...todos,
      { id: Date.now(), text, completed: false }
    ]);
    setInput("");
  };

  const handleToggle = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  const handleDelete = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  const handleClearCompleted = () => {
    setTodos(todos.filter(todo => !todo.completed));
  };

  const filteredTodos = todos.filter(todo => {
    if (filter === "active") return !todo.completed;
    if (filter === "completed") return todo.completed;
    return true;
  });

  return (
    <div className="min-h-screen bg-gray-50 py-10">
      <div className="max-w-lg mx-auto px-4">
        <header className="text-center mb-8">
          <h1 className="text-4xl font-bold text-gray-800">✅ Todo App</h1>
          <p className="text-gray-500 mt-1">Built with React & Tailwind CSS</p>
        </header>

        <div className="bg-white rounded-2xl shadow-lg p-6">
          <TodoForm
            inputValue={input}
            onInputChange={(e) => setInput(e.target.value)}
            onSubmit={handleSubmit}
          />

          <TodoFilter
            currentFilter={filter}
            onFilterChange={setFilter}
          />

          <TodoList
            todos={filteredTodos}
            onToggle={handleToggle}
            onDelete={handleDelete}
          />

          <TodoStats
            todos={todos}
            onClearCompleted={handleClearCompleted}
          />
        </div>

        <footer className="text-center mt-6 text-sm text-gray-400">
          <p>&copy; 2026 NextGen Arts — Week 9 Project</p>
        </footer>
      </div>
    </div>
  );
}

export default App;
```

---

## 🧪 Test Your App

1. ✅ Add a new task — appears in the list
2. ✅ Click the circle — toggles completed (strikethrough)
3. ✅ Hover a task — delete button appears
4. ✅ Click × — task is removed
5. ✅ Filter buttons work (All / Active / Completed)
6. ✅ Task counter updates correctly
7. ✅ "Clear completed" removes all done tasks
8. ✅ Empty state shows when no tasks match filter

---

## 🚀 Stretch Goals

- 💾 Add **localStorage** persistence (save/load tasks)
- ✏️ Add **edit task** — double-click to edit inline
- 🔄 Add **drag and drop** to reorder tasks
- 🌙 Add **dark mode** toggle
- ⌨️ Add **keyboard shortcuts** (Enter to add, Escape to cancel)

---

## 📝 Homework

1. Add a **priority** field (low, medium, high) with colored indicators
2. Add a **due date** picker and highlight overdue tasks in red
3. Create a **category/tag** system for organizing tasks
4. Implement **bulk actions** (select multiple → delete or complete)

---

## 💡 Pro Tips

> 🧩 **Component breakdown:** Notice how each part of the UI is a separate component. This makes code reusable and easy to test.

> 🔑 **Unique keys:** We use `Date.now()` for simple IDs. In production, use a library like `uuid` or `nanoid` for guaranteed unique IDs.

> 📦 **Props as API:** Think of each component's props as its API. `TodoItem` needs a `todo`, `onToggle`, and `onDelete` — that's its contract with the parent.

---

[← Day 5](day-05.md) | [Back to Week 9](README.md) | [Week 10 →](../week-10/README.md)
