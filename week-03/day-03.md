# 📅 Day 3: CSS Grid Fundamentals — Rows, Columns & Areas 🗂️

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Completed Flexbox fundamentals (Days 1-2)
- Understanding of CSS box model and display properties

---

## 🎯 Today's Goals

- 🗂️ Understand the CSS Grid layout model
- 📊 Define rows and columns with `grid-template`
- 🏷️ Use named grid areas for intuitive layouts
- 🔢 Master the `fr` unit, `repeat()`, and `minmax()`

---

## 📘 Lesson Content

### What is CSS Grid?

CSS Grid is a **two-dimensional** layout system — it handles both rows AND columns simultaneously, unlike Flexbox which is one-dimensional.

### Grid Container Basics

```css
.grid-container {
  display: grid; /* or inline-grid */

  /* Define columns */
  grid-template-columns: 200px 1fr 200px;

  /* Define rows */
  grid-template-rows: 80px 1fr 60px;

  /* Gap between cells */
  gap: 20px;
  row-gap: 20px;
  column-gap: 15px;
}
```

### The `fr` Unit

The `fr` unit represents a **fraction** of available space:

```css
.grid {
  display: grid;

  /* 3 equal columns */
  grid-template-columns: 1fr 1fr 1fr;

  /* Middle column gets twice the space */
  grid-template-columns: 1fr 2fr 1fr;

  /* Fixed sidebar + flexible content */
  grid-template-columns: 250px 1fr;
}
```

### `repeat()` Function

```css
.grid {
  /* 4 equal columns */
  grid-template-columns: repeat(4, 1fr);

  /* Pattern: 100px then 1fr, repeated 3 times */
  grid-template-columns: repeat(3, 100px 1fr);

  /* Auto-fit: as many 250px columns as fit */
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));

  /* Auto-fill: similar but keeps empty tracks */
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
}
```

### `minmax()` Function

```css
.grid {
  /* Columns are at least 200px, at most 1fr */
  grid-template-columns: repeat(3, minmax(200px, 1fr));

  /* Rows are at least 100px, grow with content */
  grid-template-rows: minmax(100px, auto);
}
```

### Grid Item Placement

```css
.item {
  /* Span columns */
  grid-column: 1 / 3;        /* From line 1 to line 3 */
  grid-column: 1 / span 2;   /* Start at 1, span 2 columns */
  grid-column: span 2;       /* Span 2 from auto-placement */

  /* Span rows */
  grid-row: 1 / 3;

  /* Shorthand */
  grid-area: 1 / 1 / 3 / 3; /* row-start / col-start / row-end / col-end */
}
```

### Named Grid Areas

```css
.layout {
  display: grid;
  grid-template-columns: 250px 1fr;
  grid-template-rows: 80px 1fr 60px;
  grid-template-areas:
    "header  header"
    "sidebar content"
    "footer  footer";
  gap: 0;
  min-height: 100vh;
}

.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }
```

### Grid Alignment

```css
.grid-container {
  /* Align all items horizontally within their cells */
  justify-items: start; /* start | end | center | stretch */

  /* Align all items vertically within their cells */
  align-items: start;

  /* Align the entire grid horizontally */
  justify-content: center;

  /* Align the entire grid vertically */
  align-content: center;
}

/* Individual item alignment */
.item {
  justify-self: center;
  align-self: end;
}
```

---

## 🛠️ Hands-On Exercise

### Build a Classic Page Layout

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Grid Layout</title>
  <style>
    * { margin: 0; box-sizing: border-box; }

    .page-layout {
      display: grid;
      grid-template-columns: 250px 1fr;
      grid-template-rows: 70px 1fr 50px;
      grid-template-areas:
        "header  header"
        "sidebar main"
        "footer  footer";
      min-height: 100vh;
      gap: 0;
    }

    .header  { grid-area: header;  background: #2c3e50; color: white; display: flex; align-items: center; padding: 0 2rem; }
    .sidebar { grid-area: sidebar; background: #34495e; color: white; padding: 1.5rem; }
    .main    { grid-area: main;    padding: 2rem; }
    .footer  { grid-area: footer;  background: #2c3e50; color: white; display: flex; align-items: center; justify-content: center; }
  </style>
</head>
<body>
  <div class="page-layout">
    <header class="header"><h1>My Website</h1></header>
    <aside class="sidebar">
      <h3>Navigation</h3>
      <ul>
        <li>Dashboard</li>
        <li>Profile</li>
        <li>Settings</li>
      </ul>
    </aside>
    <main class="main">
      <h2>Welcome</h2>
      <p>This is a CSS Grid page layout with named areas.</p>
    </main>
    <footer class="footer">© 2025 NextGen Arts</footer>
  </div>
</body>
</html>
```

---

## 📝 Homework

1. Create a **photo gallery** using CSS Grid with items of different sizes (some spanning 2 columns or 2 rows)
2. Build a **dashboard layout** with header, sidebar, main content, and footer using named grid areas
3. Create a **3-column article layout** (sidebar, content, aside) that stacks vertically on small screens

---

## 💡 Pro Tips

> 🎮 **Practice:** Play [Grid Garden](https://cssgridgarden.com/) to master grid placement interactively.

> 🆚 **Flexbox vs Grid:** Use Flexbox for UI components (navbars, card rows, form inputs). Use Grid for page layouts and complex 2D arrangements. They work great together!

> 🔢 **auto-fit vs auto-fill:** `auto-fit` collapses empty tracks, making items stretch. `auto-fill` keeps empty tracks, honoring the size. For responsive cards, `auto-fit` is usually what you want.

---

[← Day 2](day-02.md) | [Back to Week 3](README.md) | [Day 4 →](day-04.md)
