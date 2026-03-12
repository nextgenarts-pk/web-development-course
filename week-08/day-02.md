# 📅 Day 2: async/await & Error Handling ⚡

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Promises, `.then()`, `.catch()` (Day 1)
- Error handling with `try/catch` (Week 7)

---

## 🎯 Today's Goals

- ⚡ Master `async/await` syntax
- 🛡️ Handle errors in async code with `try/catch`
- 🔄 Combine async/await with Promise utilities
- 📐 Write clean async control flow

---

## 📘 Lesson Content

### async/await Basics

```javascript
// async function always returns a Promise
async function greet() {
  return "Hello!"; // Same as: return Promise.resolve("Hello!");
}

greet().then(msg => console.log(msg)); // "Hello!"

// await pauses execution until Promise resolves
async function fetchUser(id) {
  const response = await fetch(`/api/users/${encodeURIComponent(id)}`);
  const user = await response.json();
  return user;
}
```

### Error Handling with try/catch

```javascript
async function loadUserProfile(userId) {
  try {
    const response = await fetch(`/api/users/${encodeURIComponent(userId)}`);

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }

    const user = await response.json();
    return user;

  } catch (error) {
    if (error.name === "TypeError") {
      console.error("Network error — are you online?");
    } else {
      console.error("Failed to load profile:", error.message);
    }
    return null;
  } finally {
    hideLoadingSpinner();
  }
}
```

### Sequential vs Parallel Execution

```javascript
// ❌ Sequential — each waits for previous (slow)
async function loadSequential() {
  const users = await fetchUsers();    // 1s
  const posts = await fetchPosts();    // 1s
  const comments = await fetchComments(); // 1s
  // Total: ~3 seconds
}

// ✅ Parallel — all start at once (fast)
async function loadParallel() {
  const [users, posts, comments] = await Promise.all([
    fetchUsers(),    // 1s ─┐
    fetchPosts(),    // 1s ─┤ All run together
    fetchComments()  // 1s ─┘
  ]);
  // Total: ~1 second
}
```

### Promise.all vs Promise.allSettled

```javascript
// Promise.all — fails fast (rejects if ANY promise rejects)
try {
  const [users, posts] = await Promise.all([
    fetchUsers(),
    fetchPosts()
  ]);
} catch (err) {
  // One failed — we don't get ANY results
  console.error("One request failed:", err);
}

// Promise.allSettled — waits for ALL (never rejects)
const results = await Promise.allSettled([
  fetchUsers(),
  fetchPosts(),
  fetchComments()
]);

results.forEach(result => {
  if (result.status === "fulfilled") {
    console.log("Data:", result.value);
  } else {
    console.error("Failed:", result.reason);
  }
});
```

### Promise.race & Promise.any

```javascript
// Promise.race — first to settle (fulfill OR reject) wins
const fastest = await Promise.race([
  fetch("https://api-server-1.com/data"),
  fetch("https://api-server-2.com/data")
]);

// Promise.any — first to FULFILL wins (ignores rejections)
const firstSuccess = await Promise.any([
  fetch("https://primary-api.com/data"),
  fetch("https://backup-api.com/data")
]);
// Only rejects if ALL promises reject
```

### Async Iteration

```javascript
// Process items sequentially with async
async function processItems(items) {
  const results = [];
  for (const item of items) {
    const result = await processItem(item);
    results.push(result);
  }
  return results;
}

// Process with concurrency limit
async function processWithLimit(items, limit, fn) {
  const results = [];
  const executing = new Set();

  for (const item of items) {
    const p = fn(item).then(result => {
      executing.delete(p);
      return result;
    });
    executing.add(p);
    results.push(p);

    if (executing.size >= limit) {
      await Promise.race(executing);
    }
  }

  return Promise.all(results);
}
```

### Async Error Wrapper

```javascript
// Utility to avoid try/catch everywhere
function asyncHandler(fn) {
  return async (...args) => {
    try {
      return await fn(...args);
    } catch (error) {
      console.error(`Error in ${fn.name}:`, error);
      return null;
    }
  };
}

// Usage
const safeLoadUser = asyncHandler(async (id) => {
  const res = await fetch(`/api/users/${encodeURIComponent(id)}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
});

const user = await safeLoadUser(123); // null if error
```

---

## 🛠️ Hands-On Exercise

### Async Data Loader with Retry

```javascript
class AsyncLoader {
  static async fetchJSON(url, options = {}) {
    const { retries = 3, delay = 1000, timeout = 5000 } = options;

    for (let attempt = 1; attempt <= retries; attempt++) {
      try {
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), timeout);

        const response = await fetch(url, { signal: controller.signal });
        clearTimeout(timeoutId);

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}`);
        }

        return await response.json();

      } catch (error) {
        console.warn(`Attempt ${attempt}/${retries} failed:`, error.message);

        if (attempt === retries) {
          throw new Error(`Failed after ${retries} attempts: ${error.message}`);
        }

        // Exponential backoff
        await new Promise(r => setTimeout(r, delay * attempt));
      }
    }
  }

  static async fetchAll(urls) {
    const results = await Promise.allSettled(
      urls.map(url => this.fetchJSON(url))
    );

    return {
      successful: results
        .filter(r => r.status === "fulfilled")
        .map(r => r.value),
      failed: results
        .filter(r => r.status === "rejected")
        .map(r => r.reason.message)
    };
  }
}

// Usage
const data = await AsyncLoader.fetchJSON("https://api.example.com/data", {
  retries: 3,
  timeout: 5000
});
```

---

## 📝 Homework

1. Convert the Week 7 Quiz App to load questions from a JSON file using `fetch()`
2. Build a **parallel image loader** that shows a progress bar as images load
3. Create a `debounce()` function and use it with an async search input
4. Implement `Promise.all()` from scratch using only basic Promise constructor

---

## 💡 Pro Tips

> ⚡ **Use `async/await` everywhere:** It's cleaner than `.then()` chains. Reserve `.then()` for simple one-liners.

> 🛡️ **Always handle errors:** Every `await` can throw. Wrap related operations in a single `try/catch`, not every single `await`.

> 🚫 **Avoid `await` in loops** when operations are independent — use `Promise.all()` instead. Sequential `await` in a loop processes items one at a time.

---

[← Day 1](day-01.md) | [Back to Week 8](README.md) | [Day 3 →](day-03.md)
