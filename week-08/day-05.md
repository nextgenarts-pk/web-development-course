# 📅 Day 5: Concurrent Requests & Advanced Patterns ⚡

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Fetch API & REST APIs from Days 3-4
- Promises and async/await fundamentals

---

## 🎯 Today's Goals

- 🔀 Master Promise.all, allSettled, race, and any
- ⏳ Implement request throttling and retry logic
- 🏗️ Build robust request pipelines
- 🔗 Chain dependent API calls efficiently

---

## 📘 Lesson Content

### Promise Concurrency Methods

```javascript
// Promise.all — Fails fast (rejects if ANY promise rejects)
// Use when you need ALL results and want to fail if any fails
async function getDashboardData(userId) {
  try {
    const [user, posts, notifications] = await Promise.all([
      fetch(`/api/users/${encodeURIComponent(userId)}`).then(r => r.json()),
      fetch(`/api/users/${encodeURIComponent(userId)}/posts`).then(r => r.json()),
      fetch(`/api/users/${encodeURIComponent(userId)}/notifications`).then(r => r.json())
    ]);

    return { user, posts, notifications };
  } catch (error) {
    console.error("Dashboard load failed:", error);
    throw error;
  }
}

// Promise.allSettled — Never rejects, returns status of each promise
// Use when you want partial results even if some fail
async function loadWidgets() {
  const results = await Promise.allSettled([
    fetch("/api/weather").then(r => r.json()),
    fetch("/api/news").then(r => r.json()),
    fetch("/api/stocks").then(r => r.json())
  ]);

  const widgets = {};
  const labels = ["weather", "news", "stocks"];

  results.forEach((result, i) => {
    if (result.status === "fulfilled") {
      widgets[labels[i]] = result.value;
    } else {
      widgets[labels[i]] = { error: result.reason.message };
      console.warn(`${labels[i]} widget failed:`, result.reason);
    }
  });

  return widgets;
}

// Promise.race — Resolves/rejects with the FIRST settled promise
// Use for timeouts or fastest-response scenarios
function fetchWithTimeout(url, timeout = 5000) {
  return Promise.race([
    fetch(url),
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("Request timed out")), timeout)
    )
  ]);
}

// Promise.any — Resolves with the FIRST fulfilled promise
// Rejects only if ALL promises reject
async function fetchFromMirrors(path) {
  const mirrors = [
    `https://api1.example.com${path}`,
    `https://api2.example.com${path}`,
    `https://api3.example.com${path}`
  ];

  try {
    const response = await Promise.any(
      mirrors.map(url => fetch(url))
    );
    return response.json();
  } catch (error) {
    // AggregateError — all promises rejected
    console.error("All mirrors failed:", error.errors);
    throw new Error("Service unavailable");
  }
}
```

### Retry with Exponential Backoff

```javascript
async function fetchWithRetry(url, options = {}) {
  const {
    maxRetries = 3,
    baseDelay = 1000,
    maxDelay = 10000,
    ...fetchOptions
  } = options;

  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch(url, fetchOptions);

      // Retry on server errors or rate limiting
      if (response.status === 429 || response.status >= 500) {
        if (attempt < maxRetries) {
          const retryAfter = response.headers.get("Retry-After");
          const delay = retryAfter
            ? parseInt(retryAfter, 10) * 1000
            : Math.min(baseDelay * 2 ** attempt, maxDelay);
          console.warn(`Retrying in ${delay}ms (attempt ${attempt + 1})`);
          await new Promise(resolve => setTimeout(resolve, delay));
          continue;
        }
      }

      return response;
    } catch (error) {
      if (attempt === maxRetries) throw error;

      const delay = Math.min(baseDelay * 2 ** attempt, maxDelay);
      console.warn(`Network error, retrying in ${delay}ms...`);
      await new Promise(resolve => setTimeout(resolve, delay));
    }
  }
}

// Usage
const response = await fetchWithRetry("/api/data", {
  maxRetries: 3,
  baseDelay: 1000
});
const data = await response.json();
```

### Request Queue (Throttling)

```javascript
class RequestQueue {
  #queue = [];
  #activeCount = 0;
  #maxConcurrent;

  constructor(maxConcurrent = 3) {
    this.#maxConcurrent = maxConcurrent;
  }

  add(requestFn) {
    return new Promise((resolve, reject) => {
      this.#queue.push({ requestFn, resolve, reject });
      this.#processNext();
    });
  }

  async #processNext() {
    if (this.#activeCount >= this.#maxConcurrent || this.#queue.length === 0) {
      return;
    }

    this.#activeCount++;
    const { requestFn, resolve, reject } = this.#queue.shift();

    try {
      const result = await requestFn();
      resolve(result);
    } catch (error) {
      reject(error);
    } finally {
      this.#activeCount--;
      this.#processNext();
    }
  }
}

// Usage — fetch 50 URLs, max 5 at a time
const queue = new RequestQueue(5);
const urls = Array.from({ length: 50 }, (_, i) =>
  `https://jsonplaceholder.typicode.com/posts/${i + 1}`
);

const results = await Promise.all(
  urls.map(url => queue.add(() => fetch(url).then(r => r.json())))
);
```

### Dependent API Calls (Waterfall)

```javascript
// Sequential — when each call depends on the previous result
async function getUserWithDetails(userId) {
  // Step 1: Get user
  const userRes = await fetch(`/api/users/${encodeURIComponent(userId)}`);
  const user = await userRes.json();

  // Step 2: Get user's team (depends on user.teamId)
  const teamRes = await fetch(`/api/teams/${encodeURIComponent(user.teamId)}`);
  const team = await teamRes.json();

  // Step 3: Get team's projects (depends on team.id)
  const projectsRes = await fetch(`/api/teams/${encodeURIComponent(team.id)}/projects`);
  const projects = await projectsRes.json();

  return { user, team, projects };
}

// Mixed — parallel where possible, sequential when dependent
async function getFullProfile(userId) {
  const userRes = await fetch(`/api/users/${encodeURIComponent(userId)}`);
  const user = await userRes.json();

  // These don't depend on each other — run in parallel
  const [posts, followers, settings] = await Promise.all([
    fetch(`/api/users/${encodeURIComponent(userId)}/posts`).then(r => r.json()),
    fetch(`/api/users/${encodeURIComponent(userId)}/followers`).then(r => r.json()),
    fetch(`/api/users/${encodeURIComponent(userId)}/settings`).then(r => r.json())
  ]);

  return { user, posts, followers, settings };
}
```

### Infinite Scroll Implementation

```javascript
class InfiniteScroller {
  #container;
  #page = 1;
  #perPage = 20;
  #isLoading = false;
  #hasMore = true;
  #url;
  #renderItem;

  constructor(container, url, renderItem) {
    this.#container = container;
    this.#url = url;
    this.#renderItem = renderItem;

    this.#setupScrollListener();
    this.loadMore();
  }

  #setupScrollListener() {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && !this.#isLoading && this.#hasMore) {
          this.loadMore();
        }
      },
      { rootMargin: "200px" }
    );

    const sentinel = document.createElement("div");
    sentinel.id = "scroll-sentinel";
    this.#container.after(sentinel);
    observer.observe(sentinel);
  }

  async loadMore() {
    if (this.#isLoading || !this.#hasMore) return;
    this.#isLoading = true;

    try {
      const url = new URL(this.#url);
      url.searchParams.set("_page", this.#page);
      url.searchParams.set("_limit", this.#perPage);

      const response = await fetch(url);
      const items = await response.json();

      if (items.length < this.#perPage) {
        this.#hasMore = false;
      }

      items.forEach(item => {
        const el = this.#renderItem(item);
        this.#container.appendChild(el);
      });

      this.#page++;
    } catch (error) {
      console.error("Load failed:", error);
    } finally {
      this.#isLoading = false;
    }
  }
}

// Usage
const container = document.getElementById("feed");
new InfiniteScroller(
  container,
  "https://jsonplaceholder.typicode.com/posts",
  (post) => {
    const div = document.createElement("div");
    div.className = "post-card";
    div.textContent = post.title;
    return div;
  }
);
```

### API Response Caching

```javascript
class APICache {
  #cache = new Map();
  #defaultTTL;

  constructor(defaultTTL = 5 * 60 * 1000) {
    this.#defaultTTL = defaultTTL;
  }

  async fetch(url, options = {}) {
    const key = `${url}:${JSON.stringify(options)}`;
    const cached = this.#cache.get(key);

    if (cached && Date.now() < cached.expiresAt) {
      return structuredClone(cached.data);
    }

    const response = await fetch(url, options);
    const data = await response.json();

    this.#cache.set(key, {
      data,
      expiresAt: Date.now() + this.#defaultTTL
    });

    return data;
  }

  invalidate(urlPattern) {
    for (const key of this.#cache.keys()) {
      if (key.includes(urlPattern)) {
        this.#cache.delete(key);
      }
    }
  }

  clear() {
    this.#cache.clear();
  }
}

// Usage
const api = new APICache(60000); // 1 minute TTL

const users = await api.fetch("/api/users"); // Network request
const usersAgain = await api.fetch("/api/users"); // From cache!

api.invalidate("/api/users"); // Clear user cache after mutation
```

---

## 🛠️ Hands-On Exercise

### Multi-Source Dashboard

Build a dashboard that loads data from multiple APIs simultaneously:

```javascript
async function loadDashboard() {
  const container = document.getElementById("dashboard");
  container.innerHTML = '<p class="loading">Loading dashboard...</p>';

  const widgetConfigs = [
    {
      id: "posts",
      url: "https://jsonplaceholder.typicode.com/posts?_limit=5",
      title: "Recent Posts"
    },
    {
      id: "users",
      url: "https://jsonplaceholder.typicode.com/users?_limit=5",
      title: "Users"
    },
    {
      id: "todos",
      url: "https://jsonplaceholder.typicode.com/todos?_limit=10",
      title: "Tasks"
    }
  ];

  const results = await Promise.allSettled(
    widgetConfigs.map(async (config) => {
      const response = await fetchWithTimeout(config.url, 5000);
      const data = await response.json();
      return { ...config, data };
    })
  );

  container.innerHTML = results.map((result, i) => {
    const config = widgetConfigs[i];
    if (result.status === "rejected") {
      return `<div class="widget error">
        <h3>${escapeHtml(config.title)}</h3>
        <p>Failed to load</p>
        <button onclick="retryWidget('${config.id}')">Retry</button>
      </div>`;
    }

    const { title, data } = result.value;
    return `<div class="widget">
      <h3>${escapeHtml(title)}</h3>
      <ul>${data.map(item =>
        `<li>${escapeHtml(item.title || item.name)}</li>`
      ).join("")}</ul>
    </div>`;
  }).join("");
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}
```

---

## 📝 Homework

1. Build a **simultaneous search** that queries GitHub repos and npm packages at the same time, displays combined results
2. Implement a **request queue** that limits API calls to 3 concurrent requests and shows a progress bar
3. Create a **resilient data loader** using `fetchWithRetry` that retries failed requests with exponential backoff
4. Add **optimistic caching** — show cached data immediately, update from network in background

---

## 💡 Pro Tips

> ⚡ **Promise.allSettled over Promise.all:** Use `allSettled` when loading independent widgets/sections — one failure shouldn't crash the whole page.

> 🔄 **Exponential backoff:** Always use exponential backoff (1s, 2s, 4s, 8s) with retry logic. Don't hammer a failing server.

> 🗂️ **Cache invalidation tip:** When the user creates/updates/deletes data, invalidate only the affected cache entries, not the entire cache.

---

[← Day 4](day-04.md) | [Back to Week 8](README.md) | [Day 6 →](day-06.md)
