# 📅 Week 13 — Day 1: Introduction to Node.js & the Runtime 🟢

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand what Node.js is and why it exists
- Learn the difference between browser JavaScript and Node.js
- Install Node.js and run JavaScript files from the terminal
- Explore the event loop and non-blocking I/O model
- Work with built-in global objects and modules (`process`, `fs`, `path`)

---

## 📖 Lesson Content

### 1. What Is Node.js?

Node.js is a **JavaScript runtime** built on Chrome's V8 engine. It lets you run JavaScript **outside the browser** — on servers, command-line tools, scripts, and more.

| Browser JavaScript | Node.js |
|---|---|
| Runs in the browser | Runs on the server / terminal |
| Has `window`, `document`, DOM APIs | Has `process`, `fs`, `http`, `path` |
| Can't access the file system | Full file system access |
| Used for UI and interactivity | Used for servers, APIs, scripts, tools |
| Sandboxed for security | Full system access |

### 2. Installing Node.js

Download from [nodejs.org](https://nodejs.org/) (LTS version recommended).

Verify installation:

```bash
node --version    # v20.x.x or higher
npm --version     # 10.x.x or higher
```

### 3. Running JavaScript with Node

Create a file `hello.js`:

```js
// hello.js
const name = 'NextGen Arts';
console.log(`Hello from ${name}!`);
console.log('Node.js version:', process.version);
console.log('Platform:', process.platform);
```

Run it:

```bash
node hello.js
```

### 4. Node.js Global Objects

In the browser you have `window`. In Node.js you have:

```js
// Global objects available everywhere in Node.js

console.log(__filename);  // Full path to current file
console.log(__dirname);   // Full path to current directory

// process — info about the running Node.js process
console.log(process.env.PATH);      // Environment variables
console.log(process.argv);          // Command-line arguments
console.log(process.cwd());         // Current working directory
console.log(process.pid);           // Process ID

// setTimeout, setInterval — same as browser
setTimeout(() => console.log('After 1 second'), 1000);
```

### 5. The Event Loop & Non-Blocking I/O

Node.js is **single-threaded** but handles thousands of concurrent connections through the **event loop**:

```
1. Node receives a request
2. If it needs I/O (file read, database query), it delegates to the OS
3. Node continues handling other requests (non-blocking)
4. When I/O completes, the callback runs
```

```js
// Blocking (synchronous) — DON'T do this in servers
const fs = require('fs');
const data = fs.readFileSync('file.txt', 'utf8');  // Blocks until done
console.log(data);

// Non-blocking (asynchronous) — DO this
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('Error:', err.message);
    return;
  }
  console.log(data);
});
console.log('This runs BEFORE the file is read!');
```

### 6. Built-in Module: `fs` (File System)

```js
const fs = require('fs');

// Read a file
fs.readFile('notes.txt', 'utf8', (err, data) => {
  if (err) {
    console.error('Error reading file:', err.message);
    return;
  }
  console.log('File contents:', data);
});

// Write a file (creates or overwrites)
fs.writeFile('output.txt', 'Hello, Node.js!', (err) => {
  if (err) {
    console.error('Error writing file:', err.message);
    return;
  }
  console.log('File written successfully');
});

// Append to a file
fs.appendFile('log.txt', `[${new Date().toISOString()}] Server started\n`, (err) => {
  if (err) console.error(err.message);
});

// Check if a file exists
fs.access('notes.txt', fs.constants.F_OK, (err) => {
  console.log(err ? 'File does not exist' : 'File exists');
});

// Create a directory
fs.mkdir('data', { recursive: true }, (err) => {
  if (err) console.error(err.message);
});
```

### 7. Promises and Async/Await with `fs`

Modern Node.js provides promise-based file APIs:

```js
const fs = require('fs/promises');

async function main() {
  try {
    // Write
    await fs.writeFile('data.json', JSON.stringify({ count: 0 }));
    console.log('File created');

    // Read
    const raw = await fs.readFile('data.json', 'utf8');
    const data = JSON.parse(raw);
    console.log('Data:', data);

    // Update
    data.count += 1;
    await fs.writeFile('data.json', JSON.stringify(data, null, 2));
    console.log('Updated count to', data.count);

    // List directory
    const files = await fs.readdir('.');
    console.log('Files:', files);
  } catch (err) {
    console.error('Error:', err.message);
  }
}

main();
```

### 8. Built-in Module: `path`

```js
const path = require('path');

// Join path segments (handles OS differences)
const filePath = path.join(__dirname, 'data', 'users.json');
console.log(filePath);  // /home/user/project/data/users.json (Linux)
                         // C:\Users\user\project\data\users.json (Windows)

// Get parts of a path
console.log(path.basename('/home/user/notes.txt'));   // notes.txt
console.log(path.extname('photo.jpg'));                // .jpg
console.log(path.dirname('/home/user/notes.txt'));     // /home/user

// Resolve to absolute path
console.log(path.resolve('data', 'users.json'));
```

### 9. Built-in Module: `http` (Sneak Peek)

Node.js can create a web server without any framework:

```js
const http = require('http');

const server = http.createServer((req, res) => {
  console.log(`${req.method} ${req.url}`);

  if (req.url === '/' && req.method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ message: 'Hello from Node.js!' }));
  } else if (req.url === '/about' && req.method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('About page');
  } else {
    res.writeHead(404, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ error: 'Not found' }));
  }
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}`);
});
```

> This works but gets messy fast — that's why we'll use **Express.js** starting Day 3.

---

## 💪 Hands-On Exercise

### Build a Contact Manager (File-Based)

1. Create a Node.js script that manages contacts stored in `contacts.json`
2. Use `process.argv` to accept commands:
   - `node contacts.js list` — display all contacts
   - `node contacts.js add "Ali" "ali@example.com"` — add a contact
   - `node contacts.js remove "Ali"` — remove a contact
3. Use `fs/promises` for all file operations
4. Handle errors gracefully (file doesn't exist, duplicate name, etc.)

---

## 📝 Homework

1. Build a simple file logger — appends timestamped messages to `app.log`
2. Create a script that reads all `.txt` files in a directory and counts the total word count
3. Build a basic HTTP server with 3 routes: `/`, `/api/time`, `/api/random`
4. Explore: run `node` in the terminal (REPL mode) and try expressions

---

## 💡 Pro Tips

- Use `fs/promises` (async) instead of callback-based `fs` — cleaner code
- `path.join()` handles `/` vs `\` across operating systems — always use it
- The `http` module is low-level — Express wraps it with a cleaner API
- Use `process.exit(1)` for error exits, `process.exit(0)` for success

---

[← Back to Week 13](README.md) | [Day 2 →](day-02.md)
