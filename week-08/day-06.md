# 📅 Day 6: Project Day — Movie Search App 🎬

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Everything from Days 1-5 (Promises, async/await, Fetch, REST APIs, concurrency)
- DOM manipulation & event handling

---

## 🎯 Today's Goals

- 🎬 Build a complete **Movie Search App** using the OMDB API
- 🔍 Implement debounced search, loading states, and error handling
- 📄 Add movie detail view with ratings and full info
- ⭐ Create a watchlist feature using localStorage

---

## 📘 Project Overview

### What We're Building

A professional movie search application with:
- **Search bar** with debounced live search
- **Results grid** with movie posters and basic info
- **Detail modal** showing full movie information
- **Watchlist** persisted in localStorage
- **Loading states** and graceful error handling

---

## 🏗️ Project Structure

```
movie-search-app/
├── index.html
├── styles.css
└── app.js
```

### Step 1: HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🎬 Movie Search App</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <h1>🎬 Movie Search</h1>
    <nav>
      <button class="nav-btn active" data-view="search">Search</button>
      <button class="nav-btn" data-view="watchlist">
        Watchlist (<span id="watchlist-count">0</span>)
      </button>
    </nav>
  </header>

  <main>
    <!-- Search Section -->
    <section id="search-section">
      <div class="search-bar">
        <input type="text" id="search-input" placeholder="Search movies..." autocomplete="off">
        <select id="type-filter">
          <option value="">All</option>
          <option value="movie">Movies</option>
          <option value="series">Series</option>
          <option value="episode">Episodes</option>
        </select>
      </div>
      <div id="results" class="movie-grid"></div>
      <div id="pagination" class="pagination"></div>
    </section>

    <!-- Watchlist Section -->
    <section id="watchlist-section" class="hidden">
      <h2>My Watchlist</h2>
      <div id="watchlist" class="movie-grid"></div>
    </section>
  </main>

  <!-- Movie Detail Modal -->
  <div id="modal" class="modal hidden">
    <div class="modal-content">
      <button class="modal-close">&times;</button>
      <div id="movie-detail"></div>
    </div>
  </div>

  <script src="app.js"></script>
</body>
</html>
```

### Step 2: Styling (styles.css)

```css
:root {
  --bg-primary: #141414;
  --bg-secondary: #1f1f1f;
  --bg-card: #2a2a2a;
  --text-primary: #ffffff;
  --text-secondary: #b3b3b3;
  --accent: #e50914;
  --accent-hover: #f40612;
  --success: #46d369;
  --radius: 8px;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: "Segoe UI", system-ui, sans-serif;
  background: var(--bg-primary);
  color: var(--text-primary);
  min-height: 100vh;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background: var(--bg-secondary);
  border-bottom: 2px solid var(--accent);
}

header h1 { font-size: 1.5rem; }

.nav-btn {
  background: transparent;
  color: var(--text-secondary);
  border: 1px solid var(--text-secondary);
  padding: 0.5rem 1rem;
  border-radius: var(--radius);
  cursor: pointer;
  margin-left: 0.5rem;
  transition: all 0.3s;
}

.nav-btn.active {
  background: var(--accent);
  color: white;
  border-color: var(--accent);
}

main { max-width: 1200px; margin: 0 auto; padding: 2rem; }

.search-bar {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}

#search-input {
  flex: 1;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  background: var(--bg-secondary);
  border: 2px solid #333;
  border-radius: var(--radius);
  color: white;
  transition: border-color 0.3s;
}

#search-input:focus {
  outline: none;
  border-color: var(--accent);
}

#type-filter {
  padding: 0.75rem 1rem;
  background: var(--bg-secondary);
  border: 2px solid #333;
  border-radius: var(--radius);
  color: white;
  cursor: pointer;
}

.movie-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1.5rem;
}

.movie-card {
  background: var(--bg-card);
  border-radius: var(--radius);
  overflow: hidden;
  cursor: pointer;
  transition: transform 0.3s, box-shadow 0.3s;
  position: relative;
}

.movie-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.4);
}

.movie-card img {
  width: 100%;
  height: 300px;
  object-fit: cover;
}

.movie-card .info {
  padding: 0.75rem;
}

.movie-card .title {
  font-size: 0.9rem;
  font-weight: 600;
  margin-bottom: 0.25rem;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.movie-card .year {
  color: var(--text-secondary);
  font-size: 0.8rem;
}

.watchlist-btn {
  position: absolute;
  top: 8px;
  right: 8px;
  background: rgba(0, 0, 0, 0.7);
  border: none;
  color: white;
  font-size: 1.2rem;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  cursor: pointer;
  transition: background 0.3s;
}

.watchlist-btn:hover { background: var(--accent); }
.watchlist-btn.saved { background: var(--accent); }

/* Modal */
.modal {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
}

.modal-content {
  background: var(--bg-secondary);
  border-radius: 12px;
  max-width: 700px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  padding: 2rem;
}

.modal-close {
  position: absolute;
  top: 1rem;
  right: 1rem;
  background: none;
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
}

.movie-detail-grid {
  display: grid;
  grid-template-columns: 200px 1fr;
  gap: 1.5rem;
}

.movie-detail-grid img {
  width: 100%;
  border-radius: var(--radius);
}

.detail-info h2 { margin-bottom: 0.5rem; }

.detail-info .meta {
  color: var(--text-secondary);
  margin-bottom: 1rem;
}

.detail-info .plot {
  line-height: 1.6;
  margin-bottom: 1rem;
}

.rating-badge {
  display: inline-block;
  padding: 0.25rem 0.5rem;
  background: var(--accent);
  border-radius: 4px;
  font-weight: bold;
  margin-right: 0.5rem;
}

/* States */
.loading-spinner {
  text-align: center;
  padding: 3rem;
  color: var(--text-secondary);
  font-size: 1.2rem;
}

.error-message {
  text-align: center;
  padding: 2rem;
  color: var(--accent);
}

.empty-state {
  text-align: center;
  padding: 3rem;
  color: var(--text-secondary);
}

.pagination {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-top: 2rem;
}

.pagination button {
  padding: 0.5rem 1rem;
  background: var(--bg-card);
  border: 1px solid #444;
  color: white;
  border-radius: var(--radius);
  cursor: pointer;
}

.pagination button.active {
  background: var(--accent);
  border-color: var(--accent);
}

.hidden { display: none !important; }

@media (max-width: 600px) {
  .movie-detail-grid {
    grid-template-columns: 1fr;
  }
  .search-bar {
    flex-direction: column;
  }
}
```

### Step 3: JavaScript Application (app.js)

```javascript
// ============================================
// 🎬 Movie Search App — Complete Application
// ============================================

// --- Configuration ---
const API_KEY = "YOUR_OMDB_API_KEY"; // Get free key at https://www.omdbapi.com/apikey.aspx
const API_URL = "https://www.omdbapi.com/";

// --- DOM Elements ---
const elements = {
  searchInput: document.getElementById("search-input"),
  typeFilter: document.getElementById("type-filter"),
  results: document.getElementById("results"),
  pagination: document.getElementById("pagination"),
  modal: document.getElementById("modal"),
  movieDetail: document.getElementById("movie-detail"),
  modalClose: document.querySelector(".modal-close"),
  watchlistSection: document.getElementById("watchlist-section"),
  searchSection: document.getElementById("search-section"),
  watchlist: document.getElementById("watchlist"),
  watchlistCount: document.getElementById("watchlist-count"),
  navBtns: document.querySelectorAll(".nav-btn")
};

// --- Utility Functions ---
function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}

// --- API Service ---
class MovieAPI {
  #cache = new Map();
  #cacheTTL = 5 * 60 * 1000; // 5 minutes

  async search(query, type = "", page = 1) {
    const cacheKey = `search:${query}:${type}:${page}`;
    const cached = this.#getFromCache(cacheKey);
    if (cached) return cached;

    const url = new URL(API_URL);
    url.searchParams.set("apikey", API_KEY);
    url.searchParams.set("s", query);
    url.searchParams.set("page", page);
    if (type) url.searchParams.set("type", type);

    const response = await fetch(url);
    if (!response.ok) throw new Error("Network error");

    const data = await response.json();
    if (data.Response === "False") {
      throw new Error(data.Error || "No results found");
    }

    const result = {
      movies: data.Search,
      totalResults: parseInt(data.totalResults, 10),
      totalPages: Math.ceil(parseInt(data.totalResults, 10) / 10)
    };

    this.#setCache(cacheKey, result);
    return result;
  }

  async getDetails(imdbId) {
    const cacheKey = `detail:${imdbId}`;
    const cached = this.#getFromCache(cacheKey);
    if (cached) return cached;

    const url = new URL(API_URL);
    url.searchParams.set("apikey", API_KEY);
    url.searchParams.set("i", imdbId);
    url.searchParams.set("plot", "full");

    const response = await fetch(url);
    if (!response.ok) throw new Error("Network error");

    const data = await response.json();
    if (data.Response === "False") {
      throw new Error(data.Error || "Movie not found");
    }

    this.#setCache(cacheKey, data);
    return data;
  }

  #getFromCache(key) {
    const entry = this.#cache.get(key);
    if (entry && Date.now() < entry.expires) {
      return entry.data;
    }
    this.#cache.delete(key);
    return null;
  }

  #setCache(key, data) {
    this.#cache.set(key, {
      data,
      expires: Date.now() + this.#cacheTTL
    });
  }
}

// --- Watchlist Manager ---
class WatchlistManager {
  #storageKey = "movie-watchlist";

  getAll() {
    try {
      return JSON.parse(localStorage.getItem(this.#storageKey)) || [];
    } catch {
      return [];
    }
  }

  add(movie) {
    const list = this.getAll();
    if (!list.some(m => m.imdbID === movie.imdbID)) {
      list.push({
        imdbID: movie.imdbID,
        Title: movie.Title,
        Year: movie.Year,
        Poster: movie.Poster,
        Type: movie.Type,
        addedAt: new Date().toISOString()
      });
      localStorage.setItem(this.#storageKey, JSON.stringify(list));
    }
  }

  remove(imdbId) {
    const list = this.getAll().filter(m => m.imdbID !== imdbId);
    localStorage.setItem(this.#storageKey, JSON.stringify(list));
  }

  has(imdbId) {
    return this.getAll().some(m => m.imdbID === imdbId);
  }

  get count() {
    return this.getAll().length;
  }
}

// --- App Controller ---
class MovieSearchApp {
  #api = new MovieAPI();
  #watchlist = new WatchlistManager();
  #currentQuery = "";
  #currentPage = 1;
  #currentType = "";

  constructor() {
    this.#bindEvents();
    this.#updateWatchlistCount();
  }

  #bindEvents() {
    // Debounced search
    elements.searchInput.addEventListener(
      "input",
      debounce(() => this.#handleSearch(), 400)
    );

    // Type filter
    elements.typeFilter.addEventListener("change", () => {
      this.#currentType = elements.typeFilter.value;
      if (this.#currentQuery) {
        this.#currentPage = 1;
        this.#performSearch();
      }
    });

    // Navigation
    elements.navBtns.forEach(btn => {
      btn.addEventListener("click", () => this.#switchView(btn.dataset.view));
    });

    // Modal
    elements.modalClose.addEventListener("click", () => this.#closeModal());
    elements.modal.addEventListener("click", (e) => {
      if (e.target === elements.modal) this.#closeModal();
    });
    document.addEventListener("keydown", (e) => {
      if (e.key === "Escape") this.#closeModal();
    });

    // Delegated events
    elements.results.addEventListener("click", (e) => this.#handleResultClick(e));
    elements.watchlist.addEventListener("click", (e) => this.#handleResultClick(e));
    elements.pagination.addEventListener("click", (e) => this.#handlePaginationClick(e));
  }

  #handleSearch() {
    const query = elements.searchInput.value.trim();
    if (query.length < 3) {
      elements.results.innerHTML = query.length > 0
        ? '<p class="empty-state">Type at least 3 characters...</p>'
        : "";
      elements.pagination.innerHTML = "";
      return;
    }

    this.#currentQuery = query;
    this.#currentPage = 1;
    this.#performSearch();
  }

  async #performSearch() {
    elements.results.innerHTML = '<div class="loading-spinner">🔍 Searching...</div>';
    elements.pagination.innerHTML = "";

    try {
      const { movies, totalPages } = await this.#api.search(
        this.#currentQuery,
        this.#currentType,
        this.#currentPage
      );

      this.#renderMovies(movies, elements.results);
      this.#renderPagination(totalPages);
    } catch (error) {
      elements.results.innerHTML = `
        <div class="error-message">
          <p>😕 ${escapeHtml(error.message)}</p>
        </div>`;
    }
  }

  #renderMovies(movies, container) {
    container.innerHTML = movies.map(movie => {
      const inWatchlist = this.#watchlist.has(movie.imdbID);
      const posterSrc = movie.Poster !== "N/A"
        ? escapeHtml(movie.Poster)
        : "https://via.placeholder.com/200x300?text=No+Poster";

      return `
        <div class="movie-card" data-id="${escapeHtml(movie.imdbID)}">
          <button class="watchlist-btn ${inWatchlist ? "saved" : ""}"
                  data-action="watchlist"
                  data-id="${escapeHtml(movie.imdbID)}"
                  title="${inWatchlist ? "Remove from" : "Add to"} watchlist">
            ${inWatchlist ? "★" : "☆"}
          </button>
          <img src="${posterSrc}" alt="${escapeHtml(movie.Title)}" loading="lazy">
          <div class="info">
            <div class="title">${escapeHtml(movie.Title)}</div>
            <div class="year">${escapeHtml(movie.Year)} • ${escapeHtml(movie.Type)}</div>
          </div>
        </div>`;
    }).join("");
  }

  #renderPagination(totalPages) {
    if (totalPages <= 1) {
      elements.pagination.innerHTML = "";
      return;
    }

    const maxVisible = 5;
    let startPage = Math.max(1, this.#currentPage - Math.floor(maxVisible / 2));
    let endPage = Math.min(totalPages, startPage + maxVisible - 1);
    startPage = Math.max(1, endPage - maxVisible + 1);

    let html = "";
    if (this.#currentPage > 1) {
      html += `<button data-page="${this.#currentPage - 1}">← Prev</button>`;
    }

    for (let i = startPage; i <= endPage; i++) {
      html += `<button data-page="${i}" class="${i === this.#currentPage ? "active" : ""}">${i}</button>`;
    }

    if (this.#currentPage < totalPages) {
      html += `<button data-page="${this.#currentPage + 1}">Next →</button>`;
    }

    elements.pagination.innerHTML = html;
  }

  #handlePaginationClick(e) {
    const btn = e.target.closest("[data-page]");
    if (!btn) return;

    this.#currentPage = parseInt(btn.dataset.page, 10);
    this.#performSearch();
    window.scrollTo({ top: 0, behavior: "smooth" });
  }

  async #handleResultClick(e) {
    // Watchlist button
    const watchlistBtn = e.target.closest("[data-action='watchlist']");
    if (watchlistBtn) {
      e.stopPropagation();
      this.#toggleWatchlist(watchlistBtn);
      return;
    }

    // Movie card — show details
    const card = e.target.closest(".movie-card");
    if (card) {
      await this.#showMovieDetail(card.dataset.id);
    }
  }

  #toggleWatchlist(btn) {
    const id = btn.dataset.id;

    if (this.#watchlist.has(id)) {
      this.#watchlist.remove(id);
      btn.classList.remove("saved");
      btn.textContent = "☆";
      btn.title = "Add to watchlist";
    } else {
      // Get movie data from the card
      const card = btn.closest(".movie-card");
      const movie = {
        imdbID: id,
        Title: card.querySelector(".title").textContent,
        Year: card.querySelector(".year").textContent.split(" • ")[0],
        Poster: card.querySelector("img").src,
        Type: card.querySelector(".year").textContent.split(" • ")[1]
      };
      this.#watchlist.add(movie);
      btn.classList.add("saved");
      btn.textContent = "★";
      btn.title = "Remove from watchlist";
    }

    this.#updateWatchlistCount();

    // Re-render watchlist if visible
    if (!elements.watchlistSection.classList.contains("hidden")) {
      this.#renderWatchlistView();
    }
  }

  async #showMovieDetail(imdbId) {
    elements.modal.classList.remove("hidden");
    elements.movieDetail.innerHTML = '<div class="loading-spinner">Loading...</div>';

    try {
      const movie = await this.#api.getDetails(imdbId);
      const inWatchlist = this.#watchlist.has(imdbId);
      const posterSrc = movie.Poster !== "N/A"
        ? escapeHtml(movie.Poster)
        : "https://via.placeholder.com/200x300?text=No+Poster";

      elements.movieDetail.innerHTML = `
        <div class="movie-detail-grid">
          <img src="${posterSrc}" alt="${escapeHtml(movie.Title)}">
          <div class="detail-info">
            <h2>${escapeHtml(movie.Title)} (${escapeHtml(movie.Year)})</h2>
            <p class="meta">
              ${escapeHtml(movie.Rated)} | ${escapeHtml(movie.Runtime)} | ${escapeHtml(movie.Genre)}
            </p>
            <p class="plot">${escapeHtml(movie.Plot)}</p>
            <p><strong>Director:</strong> ${escapeHtml(movie.Director)}</p>
            <p><strong>Cast:</strong> ${escapeHtml(movie.Actors)}</p>
            <div style="margin-top: 1rem;">
              ${movie.imdbRating !== "N/A" ? `<span class="rating-badge">⭐ ${escapeHtml(movie.imdbRating)}/10</span>` : ""}
              ${movie.Metascore !== "N/A" ? `<span class="rating-badge">📊 ${escapeHtml(movie.Metascore)}/100</span>` : ""}
            </div>
            <button class="nav-btn ${inWatchlist ? "active" : ""}" style="margin-top: 1rem;"
                    onclick="app.toggleWatchlistFromDetail('${escapeHtml(imdbId)}')">
              ${inWatchlist ? "★ In Watchlist" : "☆ Add to Watchlist"}
            </button>
          </div>
        </div>`;
    } catch (error) {
      elements.movieDetail.innerHTML = `
        <div class="error-message">
          <p>Failed to load movie details: ${escapeHtml(error.message)}</p>
        </div>`;
    }
  }

  toggleWatchlistFromDetail(imdbId) {
    if (this.#watchlist.has(imdbId)) {
      this.#watchlist.remove(imdbId);
    } else {
      // Need to fetch basic info — for simplicity, re-fetch detail
      this.#api.getDetails(imdbId).then(movie => {
        this.#watchlist.add(movie);
        this.#updateWatchlistCount();
      });
      return;
    }
    this.#updateWatchlistCount();
    this.#showMovieDetail(imdbId); // Re-render modal
  }

  #closeModal() {
    elements.modal.classList.add("hidden");
    elements.movieDetail.innerHTML = "";
  }

  #switchView(view) {
    elements.navBtns.forEach(btn => {
      btn.classList.toggle("active", btn.dataset.view === view);
    });

    if (view === "search") {
      elements.searchSection.classList.remove("hidden");
      elements.watchlistSection.classList.add("hidden");
    } else {
      elements.searchSection.classList.add("hidden");
      elements.watchlistSection.classList.remove("hidden");
      this.#renderWatchlistView();
    }
  }

  #renderWatchlistView() {
    const movies = this.#watchlist.getAll();

    if (movies.length === 0) {
      elements.watchlist.innerHTML = `
        <div class="empty-state" style="grid-column: 1 / -1;">
          <p>📭 Your watchlist is empty</p>
          <p>Search for movies and click ☆ to add them!</p>
        </div>`;
      return;
    }

    this.#renderMovies(movies, elements.watchlist);
  }

  #updateWatchlistCount() {
    elements.watchlistCount.textContent = this.#watchlist.count;
  }
}

// --- Initialize App ---
const app = new MovieSearchApp();
```

---

## 🧪 Test Your App

1. ✅ Search "Batman" — should display results with posters
2. ✅ Click a movie card — modal opens with full details
3. ✅ Click ☆ on a movie — adds to watchlist, star becomes ★
4. ✅ Switch to Watchlist view — saved movies appear
5. ✅ Refresh page — watchlist persists (localStorage)
6. ✅ Search with less than 3 chars — shows helper message
7. ✅ Search nonsense — shows "Movie not found" error
8. ✅ Type filter works — filters by movie/series/episode

---

## 🚀 Stretch Goals

- 🎨 Add **dark/light theme toggle**
- 📱 Make it a **PWA** (manifest + service worker for offline)
- 🔄 Add **recently viewed** section
- 📊 Show **Rotten Tomatoes** and **Metacritic** scores in cards
- ∞ Implement **infinite scroll** instead of pagination

---

## 📝 Homework

1. Add a **"Sort by"** feature to the watchlist (date added, title, year, rating)
2. Implement **similar movies** — when viewing a detail, search for movies with the same genre
3. Add a **remove from watchlist** button with a confirm dialog
4. Create an **export/import** feature for watchlist (JSON download/upload)

---

## 💡 Pro Tips

> 🔑 **API keys:** Get your free OMDB key at [omdbapi.com/apikey.aspx](https://www.omdbapi.com/apikey.aspx). Never commit API keys to Git — use environment variables in real projects.

> 🎯 **Progressive enhancement:** Start with search working, then add watchlist, then add the modal — build features incrementally.

> 🧪 **Test edge cases:** Empty search, no poster images, very long titles, API errors, rate limiting — handle them all gracefully.

---

[← Day 5](day-05.md) | [Back to Week 8](README.md) | [Week 9 →](../week-09/README.md)
