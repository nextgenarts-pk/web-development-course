# 📅 Day 5: CSS Display, Positioning & Float 📐

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- Box model and backgrounds from Days 2-4

## 🎯 Today's Goals
- Master the CSS `display` property
- Understand CSS positioning (static, relative, absolute, fixed, sticky)
- Learn the z-index and stacking context
- Understand float and clear (legacy layout)

---

## 📖 Lesson Content

### 1. CSS Display Property

```css
/* Block: takes full width, starts on new line */
.block { display: block; }          /* div, p, h1-h6, section */

/* Inline: takes only needed width, no new line */
.inline { display: inline; }        /* span, a, strong, em */

/* Inline-block: inline + can set width/height */
.inline-block { display: inline-block; }

/* None: completely hidden */
.hidden { display: none; }
```

### 2. CSS Positioning

```css
/* Static (default) — normal flow */
.static { position: static; }

/* Relative — offset from normal position */
.relative {
    position: relative;
    top: 10px;
    left: 20px;
}

/* Absolute — positioned relative to nearest positioned ancestor */
.absolute {
    position: absolute;
    top: 0;
    right: 0;
}

/* Fixed — stays in place when scrolling */
.fixed-nav {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 1000;
}

/* Sticky — switches between relative and fixed */
.sticky-header {
    position: sticky;
    top: 0;
}
```

### 3. Z-Index (Stacking Order)

```css
.behind { z-index: 1; }
.middle { z-index: 10; }
.front  { z-index: 100; }
.modal  { z-index: 1000; }
```

### 4. Centering Techniques

```css
/* Center horizontally (block element) */
.center-h { margin: 0 auto; width: 80%; }

/* Center text */
.center-text { text-align: center; }

/* Center absolute element */
.center-absolute {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

---

## ✍️ Hands-On Exercise
1. Create a fixed navigation bar that stays at the top
2. Build a card with an absolute-positioned badge in the corner
3. Create a sticky sidebar that follows scroll
4. Center an element both horizontally and vertically

---

## 📝 Homework
- Read: [MDN — CSS Positioning](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Positioning)
- Practice: Build a simple layout using only `display` and `position`

---

## 💡 Tips & Best Practices
- 📐 Use `position: relative` on the parent for absolute children
- 🔝 Keep z-index values in increments (10, 20, 100, 1000)
- ⚠️ Avoid float for layouts — use Flexbox/Grid (Week 3)

---

[← Previous Day](day-04.md) | [Back to Week 2](README.md) | [Next Day →](day-06.md)
