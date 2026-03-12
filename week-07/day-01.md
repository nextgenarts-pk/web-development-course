# 📅 Day 1: ES6 Modules — import, export & Module Architecture 📦

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- JavaScript fundamentals and functions (Week 5)
- DOM manipulation and events (Week 6)

---

## 🎯 Today's Goals

- 📦 Understand why modules matter
- 📤 Use named and default exports
- 📥 Use named and default imports
- 🏗️ Design modular application architecture
- 🔧 Set up a project with ES modules

---

## 📘 Lesson Content

### Why Modules?

Without modules, all JavaScript shares a global scope — variables collide, dependencies are unclear, and code is hard to maintain. Modules solve this by:
- **Encapsulating** code in isolated files
- Making **dependencies explicit** via imports
- Enabling **code reuse** across projects

### Setting Up ES Modules

```html
<!-- In HTML, use type="module" -->
<script type="module" src="app.js"></script>
```

### Named Exports & Imports

```javascript
// math.js — named exports
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export function multiply(a, b) {
  return a * b;
}

// Alternative: export at the end
const subtract = (a, b) => a - b;
const divide = (a, b) => b !== 0 ? a / b : NaN;
export { subtract, divide };
```

```javascript
// app.js — named imports
import { add, multiply, PI } from "./math.js";

console.log(add(2, 3));     // 5
console.log(PI);             // 3.14159

// Rename imports
import { add as sum, multiply as product } from "./math.js";

// Import everything as namespace
import * as MathUtils from "./math.js";
console.log(MathUtils.add(2, 3));
console.log(MathUtils.PI);
```

### Default Exports & Imports

```javascript
// User.js — default export (one per file)
export default class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }
}
```

```javascript
// app.js — default import (any name works)
import User from "./User.js";
// or: import MyUser from "./User.js";

const user = new User("Ali", "ali@example.com");
console.log(user.greet());
```

### Combining Named and Default

```javascript
// api.js
export default class ApiClient {
  constructor(baseUrl) {
    this.baseUrl = baseUrl;
  }
}

export const API_VERSION = "v2";
export function buildUrl(path) {
  return `/api/${API_VERSION}/${path}`;
}
```

```javascript
// app.js
import ApiClient, { API_VERSION, buildUrl } from "./api.js";
```

### Re-exports (Barrel Files)

```javascript
// utils/index.js — re-export from multiple modules
export { add, subtract, multiply } from "./math.js";
export { formatDate, parseDate } from "./date.js";
export { validateEmail, validatePhone } from "./validation.js";
export { default as Storage } from "./storage.js";
```

```javascript
// app.js — clean imports from single entry point
import { add, formatDate, validateEmail, Storage } from "./utils/index.js";
```

### Module Architecture Example

```
project/
├── index.html
├── app.js              ← entry point
├── components/
│   ├── Header.js
│   ├── TodoList.js
│   └── TodoItem.js
├── services/
│   ├── storage.js
│   └── api.js
├── utils/
│   ├── helpers.js
│   ├── validators.js
│   └── index.js        ← barrel file
└── styles/
    └── main.css
```

### Dynamic Imports (Code Splitting)

```javascript
// Load a module only when needed
async function loadChart() {
  const { default: Chart } = await import("./chart.js");
  const chart = new Chart("#container");
  chart.render(data);
}

// Conditional loading
if (userWantsAdvancedFeatures) {
  const { advancedSearch } = await import("./features/advanced.js");
  advancedSearch(query);
}
```

---

## 🛠️ Hands-On Exercise

### Modular Utility Library

Create a modular utility library with this structure:

```
utils-lib/
├── index.html
├── app.js
├── utils/
│   ├── math.js      (add, subtract, multiply, divide, average)
│   ├── string.js     (capitalize, slugify, truncate, escapeHtml)
│   ├── array.js      (unique, chunk, shuffle, groupBy)
│   ├── dom.js        (select, selectAll, create, on)
│   └── index.js      (barrel re-exports)
```

```javascript
// utils/dom.js
export function select(selector) {
  return document.querySelector(selector);
}

export function selectAll(selector) {
  return [...document.querySelectorAll(selector)];
}

export function create(tag, attrs = {}, children = []) {
  const el = document.createElement(tag);
  Object.entries(attrs).forEach(([key, val]) => {
    if (key === "class") el.className = val;
    else if (key === "text") el.textContent = val;
    else el.setAttribute(key, val);
  });
  children.forEach(child =>
    el.appendChild(typeof child === "string" ? document.createTextNode(child) : child)
  );
  return el;
}

export function on(element, event, handler) {
  element.addEventListener(event, handler);
  return () => element.removeEventListener(event, handler); // cleanup function
}
```

---

## 📝 Homework

1. Refactor your Week 6 To-Do App to use ES modules (separate files for storage, rendering, events)
2. Create a `validators.js` module with email, URL, phone number, and password validation functions
3. Build a `dom.js` utility module and use it to simplify DOM operations
4. Create a barrel file (`index.js`) that re-exports all utilities

---

## 💡 Pro Tips

> 📦 **One concept per file:** Each module should do one thing well. If a file has 300+ lines, it probably needs to be split.

> 📂 **Barrel files:** Use `index.js` barrel files to re-export from directories. This keeps import paths clean: `import { x } from "./utils"` instead of `import { x } from "./utils/deep/nested/file"`.

> 🔄 **Avoid circular imports:** If module A imports from B and B imports from A, you have a circular dependency. Restructure by extracting shared code into a third module.

---

[← Back to Week 7](README.md) | [Day 2 →](day-02.md)
