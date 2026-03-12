# 📅 Day 4: Working with REST APIs & JSON 📊

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Fetch API (GET, POST, headers) from Day 3
- async/await and error handling

---

## 🎯 Today's Goals

- 📊 Understand REST API architecture and conventions
- 🔗 Work with real-world public APIs
- 📋 Handle pagination, filtering, and sorting from APIs
- 🔄 Build dynamic data-driven UIs with fetched data

---

## 📘 Lesson Content

### REST API Conventions

```
REST = Representational State Transfer

HTTP Methods → CRUD Operations:
GET    → Read    (fetch data)
POST   → Create  (send new data)
PUT    → Update  (replace entire resource)
PATCH  → Update  (modify specific fields)
DELETE → Delete  (remove resource)

Common URL patterns:
GET    /api/users          → list all users
GET    /api/users/123      → get user 123
POST   /api/users          → create new user
PUT    /api/users/123      → replace user 123
PATCH  /api/users/123      → update user 123
DELETE /api/users/123      → delete user 123
GET    /api/users/123/posts → get posts by user 123
```

### HTTP Status Codes

```javascript
// 2xx Success
200 // OK — request succeeded
201 // Created — resource created
204 // No Content — success, no body (common for DELETE)

// 3xx Redirect
301 // Moved Permanently
304 // Not Modified (cached)

// 4xx Client Error
400 // Bad Request — invalid data
401 // Unauthorized — not authenticated
403 // Forbidden — not authorized
404 // Not Found
409 // Conflict — duplicate resource
422 // Unprocessable Entity — validation errors
429 // Too Many Requests — rate limited

// 5xx Server Error
500 // Internal Server Error
503 // Service Unavailable
```

### Working with Real APIs

```javascript
// Weather API example (OpenWeatherMap)
async function getWeather(city) {
  const apiKey = "your-api-key"; // Store securely!
  const url = new URL("https://api.openweathermap.org/data/2.5/weather");
  url.searchParams.set("q", city);
  url.searchParams.set("appid", apiKey);
  url.searchParams.set("units", "metric");

  const response = await fetch(url);
  if (!response.ok) {
    if (response.status === 404) throw new Error("City not found");
    throw new Error(`Weather API error: ${response.status}`);
  }

  const data = await response.json();
  return {
    city: data.name,
    temp: data.main.temp,
    description: data.weather[0].description,
    icon: `https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`,
    humidity: data.main.humidity,
    wind: data.wind.speed
  };
}
```

### Pagination Handling

```javascript
class PaginatedList {
  #baseUrl;
  #currentPage = 1;
  #totalPages = 1;
  #perPage = 10;
  #isLoading = false;

  constructor(baseUrl, perPage = 10) {
    this.#baseUrl = baseUrl;
    this.#perPage = perPage;
  }

  async loadPage(page = 1) {
    if (this.#isLoading) return null;
    this.#isLoading = true;

    try {
      const url = new URL(this.#baseUrl);
      url.searchParams.set("_page", page);
      url.searchParams.set("_limit", this.#perPage);

      const response = await fetch(url);
      const totalCount = response.headers.get("x-total-count");
      this.#totalPages = Math.ceil(totalCount / this.#perPage);
      this.#currentPage = page;

      return await response.json();
    } finally {
      this.#isLoading = false;
    }
  }

  async nextPage() {
    if (this.#currentPage < this.#totalPages) {
      return this.loadPage(this.#currentPage + 1);
    }
    return null;
  }

  async prevPage() {
    if (this.#currentPage > 1) {
      return this.loadPage(this.#currentPage - 1);
    }
    return null;
  }

  get pageInfo() {
    return {
      current: this.#currentPage,
      total: this.#totalPages,
      hasNext: this.#currentPage < this.#totalPages,
      hasPrev: this.#currentPage > 1
    };
  }
}

// Usage
const postList = new PaginatedList("https://jsonplaceholder.typicode.com/posts", 10);
const firstPage = await postList.loadPage(1);
console.log(postList.pageInfo); // { current: 1, total: 10, hasNext: true, hasPrev: false }
```

### Debounced Search

```javascript
function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

// Search with debounce
const searchInput = document.getElementById("search");
const resultsContainer = document.getElementById("results");

const searchAPI = debounce(async (query) => {
  if (!query.trim()) {
    resultsContainer.innerHTML = "";
    return;
  }

  resultsContainer.innerHTML = '<div class="loading">Searching...</div>';

  try {
    const url = new URL("https://api.github.com/search/repositories");
    url.searchParams.set("q", query);
    url.searchParams.set("per_page", 10);

    const response = await fetch(url);
    const data = await response.json();

    resultsContainer.innerHTML = data.items.map(repo => `
      <div class="repo-card">
        <h3><a href="${escapeHtml(repo.html_url)}" target="_blank" rel="noopener">${escapeHtml(repo.full_name)}</a></h3>
        <p>${escapeHtml(repo.description || "No description")}</p>
        <span>⭐ ${repo.stargazers_count}</span>
      </div>
    `).join("");
  } catch (error) {
    resultsContainer.innerHTML = `<p class="error">${escapeHtml(error.message)}</p>`;
  }
}, 500);

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

searchInput.addEventListener("input", (e) => searchAPI(e.target.value));
```

### Loading States Pattern

```javascript
function renderState(container, state) {
  switch (state.status) {
    case "loading":
      container.innerHTML = `
        <div class="loading-state">
          <div class="spinner"></div>
          <p>Loading...</p>
        </div>`;
      break;

    case "error":
      container.innerHTML = `
        <div class="error-state">
          <p>❌ ${escapeHtml(state.message)}</p>
          <button class="btn retry-btn">Retry</button>
        </div>`;
      break;

    case "empty":
      container.innerHTML = `
        <div class="empty-state">
          <p>📭 No results found</p>
        </div>`;
      break;

    case "success":
      container.innerHTML = state.render(state.data);
      break;
  }
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}
```

---

## 🛠️ Hands-On Exercise

### GitHub User Explorer

Build a page that searches GitHub users and displays their repos:

```javascript
async function searchGitHubUser(username) {
  const [userRes, reposRes] = await Promise.all([
    fetch(`https://api.github.com/users/${encodeURIComponent(username)}`),
    fetch(`https://api.github.com/users/${encodeURIComponent(username)}/repos?sort=stars&per_page=5`)
  ]);

  if (!userRes.ok) throw new Error("User not found");

  const user = await userRes.json();
  const repos = await reposRes.json();

  return { user, repos };
}

// Display user profile + top repos
async function handleSearch(username) {
  const container = document.getElementById("profile");
  renderState(container, { status: "loading" });

  try {
    const { user, repos } = await searchGitHubUser(username);

    renderState(container, {
      status: "success",
      data: { user, repos },
      render: ({ user, repos }) => `
        <div class="user-card">
          <img src="${escapeHtml(user.avatar_url)}" alt="${escapeHtml(user.login)}" width="120">
          <h2>${escapeHtml(user.name || user.login)}</h2>
          <p>${escapeHtml(user.bio || "")}</p>
          <div class="stats">
            <span>📦 ${user.public_repos} repos</span>
            <span>👥 ${user.followers} followers</span>
          </div>
        </div>
        <h3>Top Repositories</h3>
        <div class="repos">
          ${repos.map(r => `
            <div class="repo">
              <a href="${escapeHtml(r.html_url)}" target="_blank" rel="noopener">${escapeHtml(r.name)}</a>
              <span>⭐ ${r.stargazers_count}</span>
            </div>
          `).join("")}
        </div>
      `
    });
  } catch (error) {
    renderState(container, { status: "error", message: error.message });
  }
}

function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}
```

---

## 📝 Homework

1. Build a **country explorer** using the [REST Countries API](https://restcountries.com/) — search, filter by region, sort by population
2. Add **infinite scroll** to the paginated posts list (load more when scrolling near bottom)
3. Add **caching** — store API responses in a Map to avoid redundant requests
4. Build a **rate limiter** that prevents more than N requests per second

---

## 💡 Pro Tips

> 🔍 **Check API docs:** Always read the API documentation for rate limits, authentication requirements, and available query parameters.

> 📦 **Cache responses:** Use a Map to cache API responses and avoid redundant requests. Set expiry times for cache entries.

> 🔄 **Debounce user input:** When calling an API on every keystroke (search), use debounce (300-500ms) to avoid flooding the API.

---

[← Day 3](day-03.md) | [Back to Week 8](README.md) | [Day 5 →](day-05.md)
