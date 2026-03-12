# 📅 Day 2: The Box Model — Margin, Padding & Border 📦

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- CSS syntax and selectors from Day 1

## 🎯 Today's Goals
- Master the CSS Box Model
- Understand margin, padding, border, and content
- Learn CSS units and box-sizing

---

## 📖 Lesson Content

### 1. The Box Model

Every HTML element is a rectangular box with four layers:

```
┌─────────────── Margin ───────────────┐
│  ┌─────────── Border ─────────────┐  │
│  │  ┌─────── Padding ──────────┐  │  │
│  │  │  ┌──── Content ───────┐  │  │  │
│  │  │  │   Your text/image  │  │  │  │
│  │  │  └────────────────────┘  │  │  │
│  │  └──────────────────────────┘  │  │
│  └────────────────────────────────┘  │
└──────────────────────────────────────┘
```

### 2. Box Model Properties

```css
.box {
    /* Content */
    width: 300px;
    height: 200px;

    /* Padding (inside the border) */
    padding: 20px;              /* all sides */
    padding: 10px 20px;         /* top/bottom, left/right */
    padding: 10px 20px 15px 25px; /* top, right, bottom, left */

    /* Border */
    border: 2px solid #333;
    border-radius: 8px;         /* rounded corners */

    /* Margin (outside the border) */
    margin: 20px;
    margin: 0 auto;             /* center horizontally */
}
```

### 3. Box Sizing

```css
/* Default: width = content only */
.box-content {
    box-sizing: content-box;  /* default */
    width: 300px;
    padding: 20px;
    /* Total width = 300 + 20 + 20 = 340px */
}

/* Better: width = content + padding + border */
.box-border {
    box-sizing: border-box;
    width: 300px;
    padding: 20px;
    /* Total width = 300px (padding included) */
}

/* Apply globally (recommended) */
*, *::before, *::after {
    box-sizing: border-box;
}
```

### 4. CSS Units

```css
.element {
    /* Absolute units */
    width: 300px;        /* pixels (most common) */

    /* Relative units */
    font-size: 1.5rem;   /* relative to root font-size */
    padding: 2em;        /* relative to element's font-size */
    width: 50%;          /* relative to parent's width */
    height: 100vh;       /* 100% of viewport height */
    width: 100vw;        /* 100% of viewport width */
}
```

---

## ✍️ Hands-On Exercise
1. Create 3 boxes with different padding, margin, and border values
2. Use DevTools to inspect the box model of each element
3. Apply `box-sizing: border-box` globally and observe the difference
4. Center a div horizontally using `margin: 0 auto`

---

## 📝 Homework
- Read: [MDN — The Box Model](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/The_box_model)
- Use Chrome DevTools to inspect the box model of 3 different websites

---

## 💡 Tips & Best Practices
- 📦 Always use `box-sizing: border-box` globally
- ↔️ Use `margin: 0 auto` to center block elements
- 📏 Prefer `rem` for font sizes and `px` for borders

---

[← Previous Day](day-01.md) | [Back to Week 2](README.md) | [Next Day →](day-03.md)
