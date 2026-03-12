# 📋 Week 8 Assignments — Asynchronous JavaScript & APIs

> **NextGen Arts — Full Stack Web Development** | [nextgenarts.pk](https://nextgenarts.pk)

---

## 🎯 Weekly Project: Movie Search App 🎬

Build a complete movie search application using the OMDB API that demonstrates mastery of async JavaScript, API consumption, and modern UI patterns.

### Requirements

| Feature | Points |
|---------|--------|
| Search with debounced input (400ms delay) | 15 |
| Movie results displayed in responsive grid | 10 |
| Loading states shown during API calls | 10 |
| Error handling with user-friendly messages | 10 |
| Movie detail modal with full info | 15 |
| Watchlist with add/remove (localStorage) | 15 |
| Pagination for search results | 10 |
| API response caching (avoid duplicate requests) | 10 |
| Code quality (clean, well-organized, no globals) | 5 |
| **Total** | **100** |

### Submission Checklist

- [ ] App searches movies via OMDB API
- [ ] Debounced search input (doesn't fire on every keystroke)
- [ ] Loading spinner shown while fetching data
- [ ] Graceful error messages (no console errors shown to users)
- [ ] Click movie card → opens detail modal with poster, plot, cast, ratings
- [ ] ☆/★ button adds/removes movies to watchlist
- [ ] Watchlist persists across page refreshes
- [ ] Pagination works for results > 10
- [ ] Cached API calls (same search doesn't re-fetch)
- [ ] Responsive layout (works on mobile & desktop)

---

## 📝 Daily Exercises

### Day 1: Callbacks, Promises & Microtask Queue

1. Convert 3 nested callback functions into a Promise chain
2. Create a `delay(ms)` function that returns a Promise
3. Build a sequential task runner using Promises
4. Demonstrate microtask vs macrotask execution order

### Day 2: async/await & Error Handling

1. Rewrite the Promise chain from Day 1 using async/await
2. Build a `retry(fn, attempts)` function using async/await
3. Create a function that fetches data with a timeout using `Promise.race`
4. Write a `safeAsync` wrapper that catches errors and returns `[error, data]`

### Day 3: Fetch API — GET, POST & Headers

1. Fetch and display a list of users from JSONPlaceholder
2. POST a new resource and display the response
3. Build a reusable `ApiClient` class with `get()`, `post()`, `put()`, `delete()`
4. Add request/response interceptors to the ApiClient

### Day 4: Working with REST APIs & JSON

1. Build a **country explorer** using the REST Countries API — search, filter, sort
2. Implement infinite scroll for a paginated API endpoint
3. Add caching to API responses using a Map with TTL
4. Build a rate limiter that prevents more than N requests per second

### Day 5: Concurrent Requests & Advanced Patterns

1. Search GitHub repos and npm packages simultaneously, display combined results
2. Implement a request queue limiting to 3 concurrent requests with progress bar
3. Create a resilient data loader with exponential backoff retry
4. Add optimistic caching — show cached data first, update from network

### Day 6: Project Day

1. Complete the **Movie Search App** (all features from requirements above)
2. (Stretch) Add a theme toggle, recently viewed section, or export/import

---

## 📊 Grading Rubric

| Criteria | Excellent (90-100%) | Good (70-89%) | Needs Work (< 70%) |
|----------|-------------------|---------------|---------------------|
| **Functionality** | All features work flawlessly | Most features work, minor bugs | Core features broken or missing |
| **API Usage** | Proper error handling, caching, debouncing | Basic fetch works, some error handling | Raw fetch calls, no error handling |
| **Async Patterns** | Uses async/await, Promise.all, proper patterns | Mix of callbacks and promises, works | Callback hell, unhandled rejections |
| **UI/UX** | Loading states, smooth transitions, responsive | Functional UI, some loading states | No loading states, poor layout |
| **Code Quality** | Well-organized classes, no globals, DRY | Decent structure, some repetition | No structure, global variables |

---

## 📅 Submission

- **Due:** End of Week 8 (Saturday)
- **Format:** GitHub repository with README explaining setup & API key instructions
- **Demo:** Live demo or screen recording showing all features

---

[← Back to Week 8](README.md) | [Week 9 →](../week-09/README.md)
