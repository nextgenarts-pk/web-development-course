# 📅 Day 1: Flexbox Fundamentals — Container & Items 📦

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Complete understanding of CSS box model
- Knowledge of `display`, `position`, and `float`
- VS Code and browser dev tools ready

---

## 🎯 Today's Goals

- 📦 Understand the Flexbox layout model
- 🔀 Learn flex container properties
- 📐 Learn flex item properties
- 🧭 Create flexible layouts without floats

---

## 📘 Lesson Content

### What is Flexbox?

Flexbox (Flexible Box Layout) is a **one-dimensional** layout method for arranging items in rows or columns. Items flex to fill additional space or shrink to fit into smaller spaces.

### Flex Container Properties

```css
.container {
  display: flex; /* or inline-flex */

  /* Direction */
  flex-direction: row; /* row | row-reverse | column | column-reverse */

  /* Wrapping */
  flex-wrap: nowrap; /* nowrap | wrap | wrap-reverse */

  /* Shorthand */
  flex-flow: row wrap; /* direction + wrap */
}
```

### Main Axis Alignment

```css
.container {
  /* Align along main axis */
  justify-content: flex-start;
  /* flex-start | flex-end | center | space-between | space-around | space-evenly */
}
```

### Cross Axis Alignment

```css
.container {
  /* Align along cross axis */
  align-items: stretch;
  /* stretch | flex-start | flex-end | center | baseline */

  /* Align wrapped lines */
  align-content: flex-start;
  /* flex-start | flex-end | center | space-between | space-around | stretch */
}
```

### Gap Property

```css
.container {
  gap: 20px;           /* row-gap and column-gap */
  row-gap: 20px;       /* gap between rows */
  column-gap: 10px;    /* gap between columns */
}
```

### Flex Item Properties

```css
.item {
  /* Growth factor */
  flex-grow: 0;    /* How much item should grow relative to others */

  /* Shrink factor */
  flex-shrink: 1;  /* How much item should shrink relative to others */

  /* Base size */
  flex-basis: auto; /* Initial size before growing/shrinking */

  /* Shorthand */
  flex: 0 1 auto;  /* grow shrink basis */

  /* Override container alignment */
  align-self: auto; /* auto | flex-start | flex-end | center | baseline | stretch */

  /* Order */
  order: 0; /* Change visual order without changing HTML */
}
```

### Common Flex Shortcuts

```css
.item { flex: 1; }       /* flex: 1 1 0% — Equal sizing */
.item { flex: auto; }    /* flex: 1 1 auto — Based on content */
.item { flex: none; }    /* flex: 0 0 auto — Fixed size */
.item { flex: 2; }       /* flex: 2 1 0% — Twice as much space */
```

### Centering with Flexbox

```css
/* The holy grail — perfect centering */
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
```

---

## 🛠️ Hands-On Exercise

### Build a Flex Playground

Create an interactive playground to experiment with Flexbox properties:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flexbox Playground</title>
  <style>
    .flex-container {
      display: flex;
      gap: 15px;
      padding: 20px;
      background: #f0f0f0;
      min-height: 300px;

      /* Try changing these: */
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
    }

    .flex-item {
      background: #3498db;
      color: white;
      padding: 30px;
      font-size: 1.2rem;
      border-radius: 8px;
      text-align: center;
    }

    .flex-item:nth-child(2) {
      flex: 2; /* This item takes twice the space */
    }
  </style>
</head>
<body>
  <div class="flex-container">
    <div class="flex-item">Item 1</div>
    <div class="flex-item">Item 2 (flex: 2)</div>
    <div class="flex-item">Item 3</div>
    <div class="flex-item">Item 4</div>
  </div>
</body>
</html>
```

---

## 📝 Homework

1. Create a **horizontal navigation bar** using Flexbox with logo on the left, links centered, and a button on the right
2. Build a **three-column layout** where the middle column takes up twice the space
3. Create a **vertically and horizontally centered** card using Flexbox

---

## 💡 Pro Tips

> 🔍 **DevTools Tip:** In Chrome/Firefox DevTools, clicking the flex badge next to `display: flex` opens a visual Flexbox editor!

> 🧸 **Practice:** Play [Flexbox Froggy](https://flexboxfroggy.com/) — it's the best way to internalize Flexbox concepts.

> ⚡ **Shorthand:** Always use the `flex` shorthand (`flex: 1`) instead of setting `flex-grow`, `flex-shrink`, and `flex-basis` separately.

---

[← Back to Week 3](README.md) | [Day 2 →](day-02.md)
