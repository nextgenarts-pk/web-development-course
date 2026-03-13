# 📅 Week 13 — Day 2: Node.js Modules & npm 📦

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand the module system — CommonJS vs ES Modules
- Create and export/import your own modules
- Initialize a project with `npm init`
- Install, use, and manage third-party packages
- Understand `package.json`, scripts, and semantic versioning

---

## 📖 Lesson Content

### 1. Why Modules?

Writing everything in one file doesn't scale. Modules let you:

- **Split** code into logical files
- **Reuse** functions across files
- **Encapsulate** — only export what's needed
- **Use third-party packages** from npm

### 2. CommonJS Modules (CJS)

The traditional Node.js module system — still the default:

```js
// math.js — exporting
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

function multiply(a, b) {
  return a * b;
}

module.exports = { add, subtract, multiply };
```

```js
// app.js — importing
const { add, subtract } = require('./math');

console.log(add(5, 3));       // 8
console.log(subtract(10, 4)); // 6
```

```js
// Exporting a single value
// logger.js
class Logger {
  info(msg) { console.log(`[INFO] ${msg}`); }
  error(msg) { console.error(`[ERROR] ${msg}`); }
}

module.exports = Logger;

// usage
const Logger = require('./logger');
const log = new Logger();
log.info('Server started');
```

### 3. ES Modules (ESM)

Modern JavaScript modules — to enable them in Node.js, add `"type": "module"` to `package.json`:

```json
{
  "name": "my-app",
  "type": "module"
}
```

```js
// math.js — ESM export
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export default function multiply(a, b) {
  return a * b;
}
```

```js
// app.js — ESM import
import multiply, { add, subtract } from './math.js';

console.log(add(5, 3));       // 8
console.log(multiply(4, 5));  // 20
```

| Feature | CommonJS | ES Modules |
|---------|----------|------------|
| Syntax | `require()` / `module.exports` | `import` / `export` |
| Loading | Synchronous | Asynchronous |
| File extension | `.js` (default) | `.js` (with `"type": "module"`) or `.mjs` |
| Used in | Node.js (legacy) | Node.js (modern), browsers |
| Top-level await | ❌ | ✅ |

> **We'll use ES Modules** in this course — it's the modern standard.

### 4. Initializing a Project with npm

```bash
mkdir todo-api
cd todo-api
npm init -y
```

This creates `package.json`:

```json
{
  "name": "todo-api",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "keywords": [],
  "author": "NextGen Arts",
  "license": "ISC"
}
```

### 5. Installing Packages

```bash
# Install a production dependency
npm install express

# Install a dev-only dependency
npm install --save-dev nodemon

# Install multiple packages
npm install dotenv cors

# Install a specific version
npm install express@4.18.2
```

After installing, your project looks like:

```
todo-api/
├── node_modules/      ← installed packages (DON'T commit)
├── package.json       ← project metadata + dependencies
├── package-lock.json  ← exact dependency tree (DO commit)
└── index.js           ← your code
```

### 6. Understanding `package.json`

```json
{
  "name": "todo-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "echo \"No tests yet\""
  },
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.3.1",
    "cors": "^2.8.5"
  },
  "devDependencies": {
    "nodemon": "^3.0.2"
  }
}
```

| Field | Purpose |
|-------|---------|
| `name` | Package name |
| `version` | Your app's version |
| `type` | `"module"` for ES Modules |
| `scripts` | CLI shortcuts (`npm run dev`) |
| `dependencies` | Required in production |
| `devDependencies` | Only for development |

### 7. Semantic Versioning (SemVer)

```
  4 . 18 . 2
  ^    ^    ^
  |    |    └── PATCH — bug fixes
  |    └─────── MINOR — new features (backward-compatible)
  └──────────── MAJOR — breaking changes
```

In `package.json`:

- `^4.18.2` — allows minor + patch updates (4.18.2 → 4.19.0 ✅, 5.0.0 ❌)
- `~4.18.2` — allows patch updates only (4.18.2 → 4.18.5 ✅, 4.19.0 ❌)
- `4.18.2` — exact version only

### 8. npm Scripts

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "lint": "eslint .",
    "test": "vitest"
  }
}
```

```bash
npm start          # runs "start" script (no "run" needed)
npm run dev        # runs "dev" script
npm test           # runs "test" script (shortcut)
npm run lint       # runs "lint" script
```

### 9. nodemon — Auto-Restart on Changes

```bash
npm install --save-dev nodemon
```

```bash
# Instead of restarting manually:
npx nodemon index.js

# Or via npm script:
npm run dev
```

`nodemon` watches your files and restarts the server whenever you save — essential for development.

### 10. The `.gitignore` File

```gitignore
# .gitignore
node_modules/
.env
*.log
dist/
```

> **Never commit** `node_modules/` — it's huge and reproducible via `npm install`.

### 11. Building a Modular Project

```
todo-api/
├── src/
│   ├── routes/
│   │   └── todoRoutes.js
│   ├── controllers/
│   │   └── todoController.js
│   ├── utils/
│   │   └── logger.js
│   └── app.js
├── index.js
├── package.json
└── .gitignore
```

```js
// src/utils/logger.js
export function log(message) {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${message}`);
}

export function error(message) {
  const timestamp = new Date().toISOString();
  console.error(`[${timestamp}] ERROR: ${message}`);
}
```

```js
// index.js
import { log } from './src/utils/logger.js';

log('Application starting...');
```

---

## 💪 Hands-On Exercise

### Build a Modular Utility Library

1. Create a new npm project with `npm init -y` and set `"type": "module"`
2. Create these modules:
   - `utils/math.js` — `add`, `subtract`, `multiply`, `divide`, `average`
   - `utils/string.js` — `capitalize`, `slugify`, `truncate`, `wordCount`
   - `utils/date.js` — `formatDate`, `timeAgo`, `isWeekend`
3. Create `index.js` that imports and tests all functions
4. Add a `"dev"` script using `nodemon`
5. Install `chalk` package and add colored console output

---

## 📝 Homework

1. Create a `utils/validator.js` module with `isEmail`, `isURL`, `isStrongPassword`
2. Add a `"test"` script that runs your validator with sample inputs
3. Install `uuid` package and generate unique IDs in your app
4. Create a `.gitignore` file with proper entries
5. Read: [npm — About semantic versioning](https://docs.npmjs.com/about-semantic-versioning)

---

## 💡 Pro Tips

- Always set `"type": "module"` in new projects — ES Modules are the future
- Use `npx` to run CLI tools without installing globally: `npx nodemon index.js`
- `npm install` (with no arguments) installs everything from `package.json`
- Use `npm outdated` to check for newer versions of your dependencies
- The `package-lock.json` ensures everyone gets the exact same dependency tree — commit it

---

[← Day 1](day-01.md) | [Back to Week 13](README.md) | [Day 3 →](day-03.md)
