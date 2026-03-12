# 📅 Day 3: Fetch API — GET, POST & Headers 🌐

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Promises and async/await (Days 1-2)
- JSON data format

---

## 🎯 Today's Goals

- 🌐 Master the Fetch API for HTTP requests
- 📥 Make GET requests to retrieve data
- 📤 Make POST requests to send data
- 📋 Work with headers, status codes, and response types
- ⏱️ Implement request timeouts and cancellation

---

## 📘 Lesson Content

### Basic GET Request

```javascript
// Simple GET
const response = await fetch("https://jsonplaceholder.typicode.com/posts");
const posts = await response.json();
console.log(posts);

// With error checking
async function fetchPosts() {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts");

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}: ${response.statusText}`);
  }

  return response.json();
}
```

### Response Object

```javascript
const response = await fetch(url);

// Response properties
response.ok;         // true if status 200-299
response.status;     // 200, 404, 500, etc.
response.statusText; // "OK", "Not Found", etc.
response.headers;    // Headers object
response.url;        // final URL (after redirects)
response.redirected; // true if redirected

// Response body methods (can only be read ONCE)
const json = await response.json();     // parse as JSON
const text = await response.text();     // parse as text
const blob = await response.blob();     // parse as Blob
const buffer = await response.arrayBuffer(); // raw binary
const formData = await response.formData();  // parse as FormData
```

### POST Request — Sending JSON

```javascript
async function createPost(data) {
  const response = await fetch("https://jsonplaceholder.typicode.com/posts", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(data)
  });

  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }

  return response.json();
}

// Usage
const newPost = await createPost({
  title: "My First Post",
  body: "This is the content of my post.",
  userId: 1
});
console.log("Created:", newPost);
```

### PUT, PATCH & DELETE

```javascript
// PUT — replace entire resource
async function updatePost(id, data) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${encodeURIComponent(id)}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data)
  });
  return response.json();
}

// PATCH — partial update
async function patchPost(id, changes) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${encodeURIComponent(id)}`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(changes)
  });
  return response.json();
}

// DELETE
async function deletePost(id) {
  const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${encodeURIComponent(id)}`, {
    method: "DELETE"
  });
  return response.ok;
}
```

### Query Parameters

```javascript
// Build URL with query params safely
function buildUrl(base, params) {
  const url = new URL(base);
  Object.entries(params).forEach(([key, value]) => {
    if (value != null) {
      url.searchParams.append(key, value);
    }
  });
  return url.toString();
}

// Usage
const url = buildUrl("https://api.example.com/search", {
  q: "javascript",
  page: 1,
  limit: 20
});
// "https://api.example.com/search?q=javascript&page=1&limit=20"

const response = await fetch(url);
```

### Request Headers

```javascript
// Common headers
const response = await fetch(url, {
  headers: {
    "Content-Type": "application/json",
    "Accept": "application/json",
    "Authorization": "Bearer your-token-here",
    "X-Custom-Header": "value"
  }
});

// Reading response headers
response.headers.get("content-type");   // "application/json"
response.headers.get("x-total-count");  // pagination info

// Iterate headers
for (const [key, value] of response.headers) {
  console.log(`${key}: ${value}`);
}
```

### AbortController — Cancel Requests

```javascript
// Cancel a request
const controller = new AbortController();

const response = fetch(url, {
  signal: controller.signal
});

// Cancel after 5 seconds
setTimeout(() => controller.abort(), 5000);

try {
  const data = await response;
} catch (e) {
  if (e.name === "AbortError") {
    console.log("Request was cancelled");
  }
}

// Cancel on user action
const cancelBtn = document.getElementById("cancel");
cancelBtn.addEventListener("click", () => controller.abort());
```

### Reusable API Client

```javascript
class ApiClient {
  #baseUrl;
  #defaultHeaders;

  constructor(baseUrl, defaultHeaders = {}) {
    this.#baseUrl = baseUrl;
    this.#defaultHeaders = {
      "Content-Type": "application/json",
      ...defaultHeaders
    };
  }

  async #request(endpoint, options = {}) {
    const url = `${this.#baseUrl}${endpoint}`;
    const config = {
      ...options,
      headers: {
        ...this.#defaultHeaders,
        ...options.headers
      }
    };

    if (config.body && typeof config.body === "object") {
      config.body = JSON.stringify(config.body);
    }

    const response = await fetch(url, config);

    if (!response.ok) {
      const error = new Error(`HTTP ${response.status}`);
      error.status = response.status;
      try {
        error.data = await response.json();
      } catch {
        error.data = null;
      }
      throw error;
    }

    if (response.status === 204) return null;
    return response.json();
  }

  get(endpoint) { return this.#request(endpoint); }
  post(endpoint, body) { return this.#request(endpoint, { method: "POST", body }); }
  put(endpoint, body) { return this.#request(endpoint, { method: "PUT", body }); }
  patch(endpoint, body) { return this.#request(endpoint, { method: "PATCH", body }); }
  delete(endpoint) { return this.#request(endpoint, { method: "DELETE" }); }
}

// Usage
const api = new ApiClient("https://jsonplaceholder.typicode.com");

const posts = await api.get("/posts");
const newPost = await api.post("/posts", { title: "Hello", body: "World", userId: 1 });
await api.delete("/posts/1");
```

---

## 🛠️ Hands-On Exercise

Build a page that fetches and displays posts from JSONPlaceholder:

```javascript
const api = new ApiClient("https://jsonplaceholder.typicode.com");

async function loadPosts() {
  const container = document.getElementById("posts");
  container.innerHTML = '<div class="loading">Loading...</div>';

  try {
    const posts = await api.get("/posts?_limit=10");
    container.innerHTML = posts.map(post => `
      <article class="post-card">
        <h3>${escapeHtml(post.title)}</h3>
        <p>${escapeHtml(post.body)}</p>
        <button class="btn-comments" data-id="${post.id}">Load Comments</button>
        <div class="comments" id="comments-${post.id}"></div>
      </article>
    `).join("");
  } catch (error) {
    container.innerHTML = `<div class="error">Failed to load: ${escapeHtml(error.message)}</div>`;
  }
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

// Event delegation for comment loading
document.getElementById("posts").addEventListener("click", async (e) => {
  const btn = e.target.closest(".btn-comments");
  if (!btn) return;

  const postId = btn.dataset.id;
  const container = document.getElementById(`comments-${postId}`);

  btn.disabled = true;
  btn.textContent = "Loading...";

  try {
    const comments = await api.get(`/posts/${encodeURIComponent(postId)}/comments`);
    container.innerHTML = comments.map(c =>
      `<div class="comment"><strong>${escapeHtml(c.name)}</strong><p>${escapeHtml(c.body)}</p></div>`
    ).join("");
  } catch {
    container.innerHTML = '<p class="error">Failed to load comments</p>';
  }
});

loadPosts();
```

---

## 📝 Homework

1. Add **pagination** — "Load More" button that fetches the next 10 posts
2. Add a **create post form** that POSTs to the API and adds the new post to the list
3. Implement **search** with debouncing that filters posts by title
4. Add **delete functionality** with confirmation dialog

---

## 💡 Pro Tips

> 🔒 **Validate responses:** Always check `response.ok` before calling `.json()`. Non-200 responses don't throw — they resolve with error status.

> 🧹 **AbortController for cleanup:** Cancel in-flight requests when users navigate away or type new search queries. This prevents stale data from appearing.

> 📐 **URL encoding:** Always use `encodeURIComponent()` for user-provided URL segments and `URLSearchParams` for query strings to prevent injection.

---

[← Day 2](day-02.md) | [Back to Week 8](README.md) | [Day 4 →](day-04.md)
