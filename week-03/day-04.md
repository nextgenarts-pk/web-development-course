# 📅 Day 4: CSS Grid Practice — Complex Page Layouts 📊

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Grid fundamentals from Day 3
- Understanding of `grid-template-areas`, `fr`, `repeat()`, `minmax()`

---

## 🎯 Today's Goals

- 📊 Build a magazine-style layout with Grid
- 🖼️ Create masonry-like gallery layouts
- 🔀 Combine Flexbox inside Grid for hybrid layouts
- 📱 Make Grid layouts responsive

---

## 📘 Lesson Content

### Magazine-Style Layout

```css
.magazine {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: auto;
  gap: 1.5rem;
  padding: 2rem;
}

/* Featured article spans full width */
.article--featured {
  grid-column: 1 / -1; /* -1 means last line */
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}

/* Large article spans 2 columns */
.article--large {
  grid-column: span 2;
  grid-row: span 2;
}

/* Regular articles take 1 cell each */
.article--regular {
  grid-column: span 1;
}
```

### Responsive Grid — No Media Queries

```css
/* Auto-responsive card grid */
.auto-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 2rem;
  padding: 2rem;
}
```

### Grid with Media Queries

```css
.dashboard {
  display: grid;
  grid-template-areas:
    "header"
    "nav"
    "main"
    "aside"
    "footer";
  grid-template-columns: 1fr;
  gap: 1rem;
}

@media (min-width: 768px) {
  .dashboard {
    grid-template-columns: 250px 1fr;
    grid-template-areas:
      "header header"
      "nav    main"
      "nav    aside"
      "footer footer";
  }
}

@media (min-width: 1024px) {
  .dashboard {
    grid-template-columns: 250px 1fr 300px;
    grid-template-areas:
      "header header header"
      "nav    main   aside"
      "footer footer footer";
  }
}
```

### Combining Flexbox & Grid

Use Grid for the overall page layout, Flexbox for components inside:

```css
/* Grid for page structure */
.page {
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh;
}

/* Flexbox for navbar inside grid */
.page__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
}

/* Grid for content cards */
.page__main {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  padding: 2rem;
}

/* Flexbox for individual card */
.card {
  display: flex;
  flex-direction: column;
}

.card__body { flex: 1; }
```

### Grid Masonry-like Effect

```css
.gallery {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}

.gallery__item:nth-child(1) { grid-row: span 2; }
.gallery__item:nth-child(4) { grid-column: span 2; }
.gallery__item:nth-child(7) { grid-row: span 2; grid-column: span 2; }

.gallery__item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 8px;
}
```

---

## 🛠️ Hands-On Exercise

### Build a Dashboard Layout

Create a full admin dashboard with:
- Top navigation bar (Flexbox)
- Left sidebar with menu items (Grid area)
- Main content area with stats cards (Grid + Flexbox)
- Right panel with activity feed

```html
<div class="dashboard">
  <header class="dashboard__header">
    <h1>Admin Dashboard</h1>
    <div class="user-menu">Welcome, Admin</div>
  </header>

  <nav class="dashboard__sidebar">
    <ul>
      <li>📊 Dashboard</li>
      <li>👥 Users</li>
      <li>📦 Products</li>
      <li>📈 Analytics</li>
      <li>⚙️ Settings</li>
    </ul>
  </nav>

  <main class="dashboard__main">
    <div class="stats-grid">
      <div class="stat-card">
        <h3>1,234</h3>
        <p>Total Users</p>
      </div>
      <div class="stat-card">
        <h3>$56,789</h3>
        <p>Revenue</p>
      </div>
      <div class="stat-card">
        <h3>892</h3>
        <p>Orders</p>
      </div>
      <div class="stat-card">
        <h3>98.5%</h3>
        <p>Uptime</p>
      </div>
    </div>
  </main>

  <aside class="dashboard__aside">
    <h3>Recent Activity</h3>
    <ul class="activity-feed">
      <li>New user registered</li>
      <li>Order #1234 completed</li>
      <li>Product updated</li>
    </ul>
  </aside>

  <footer class="dashboard__footer">© 2025 NextGen Arts</footer>
</div>
```

---

## 📝 Homework

1. Complete the dashboard with full CSS styling
2. Make the dashboard **fully responsive** — sidebar collapses on mobile
3. Create a **blog homepage** layout with featured post spanning full width and a grid of smaller posts below
4. Build a **portfolio gallery** with items of varying sizes using `grid-column: span` and `grid-row: span`

---

## 💡 Pro Tips

> 🏗️ **Layout Strategy:** Start with `grid-template-areas` for the overall page structure, then use `repeat(auto-fit, minmax())` for content grids inside.

> 📱 **Mobile First:** Define the mobile layout (single column) first, then add complexity with media queries for larger screens.

---

[← Day 3](day-03.md) | [Back to Week 3](README.md) | [Day 5 →](day-05.md)
