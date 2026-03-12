# 📅 Day 2: Responsive Typography & Units 🔤

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Media queries and mobile-first design from Day 1
- Basic CSS typography knowledge

---

## 🎯 Today's Goals

- 🔤 Master responsive CSS units (`rem`, `em`, `vw`, `vh`, `%`)
- 📐 Use `clamp()` for fluid typography
- 📊 Create a responsive type scale
- 📏 Build fluid spacing systems

---

## 📘 Lesson Content

### CSS Units Comparison

| Unit | Type | Relative To | Use Case |
|------|------|-------------|----------|
| `px` | Absolute | Nothing | Borders, shadows |
| `rem` | Relative | Root font size (16px) | Font sizes, spacing |
| `em` | Relative | Parent font size | Component-level scaling |
| `%` | Relative | Parent dimension | Widths, layouts |
| `vw` | Relative | Viewport width | Full-width elements |
| `vh` | Relative | Viewport height | Full-height sections |
| `vmin` | Relative | Smaller viewport dimension | Fluid sizing |
| `ch` | Relative | Width of "0" character | Line width limits |

### `rem` vs `em`

```css
/* rem — always relative to html font-size (default 16px) */
html { font-size: 16px; }
h1 { font-size: 2rem; }    /* Always 32px */
p  { font-size: 1rem; }    /* Always 16px */

/* em — relative to parent's font-size */
.parent { font-size: 20px; }
.child  { font-size: 1.5em; }  /* 30px (20 × 1.5) */
.child  { padding: 1em; }       /* 30px (based on child's font-size) */
```

### The `clamp()` Function — Fluid Typography

```css
/* clamp(minimum, preferred, maximum) */
h1 {
  /* Min 1.5rem, preferred 4vw, max 3rem */
  font-size: clamp(1.5rem, 4vw, 3rem);
}

p {
  font-size: clamp(0.9rem, 1.5vw, 1.125rem);
}

/* Fluid padding */
section {
  padding: clamp(1rem, 5vw, 4rem);
}

/* Fluid container */
.container {
  width: clamp(300px, 90%, 1200px);
  margin: 0 auto;
}
```

### Responsive Type Scale

```css
:root {
  --fs-xs:   clamp(0.75rem,  1vw,   0.875rem);
  --fs-sm:   clamp(0.875rem, 1.2vw, 1rem);
  --fs-base: clamp(1rem,     1.5vw, 1.125rem);
  --fs-md:   clamp(1.125rem, 2vw,   1.5rem);
  --fs-lg:   clamp(1.5rem,   3vw,   2rem);
  --fs-xl:   clamp(2rem,     4vw,   3rem);
  --fs-2xl:  clamp(2.5rem,   5vw,   4rem);
}

h1 { font-size: var(--fs-2xl); }
h2 { font-size: var(--fs-xl); }
h3 { font-size: var(--fs-lg); }
h4 { font-size: var(--fs-md); }
p  { font-size: var(--fs-base); }
small { font-size: var(--fs-sm); }
```

### Fluid Spacing System

```css
:root {
  --space-xs:  clamp(0.25rem, 0.5vw,  0.5rem);
  --space-sm:  clamp(0.5rem,  1vw,    0.75rem);
  --space-md:  clamp(1rem,    2vw,    1.5rem);
  --space-lg:  clamp(1.5rem,  3vw,    2.5rem);
  --space-xl:  clamp(2rem,    5vw,    4rem);
  --space-2xl: clamp(3rem,    8vw,    6rem);
}

section {
  padding: var(--space-xl) var(--space-md);
}

.card {
  padding: var(--space-md);
  margin-bottom: var(--space-lg);
}
```

### Optimal Reading Width

```css
.article {
  max-width: 65ch; /* ~65 characters per line — optimal readability */
  margin: 0 auto;
  line-height: 1.7;
}
```

### CSS Custom Properties for Theming

```css
:root {
  --color-primary: #3498db;
  --color-secondary: #2ecc71;
  --color-text: #333;
  --color-bg: #fff;
  --border-radius: 8px;
  --shadow: 0 4px 6px rgba(0,0,0,0.1);
}

/* Easy theme switching via classes or media queries */
@media (prefers-color-scheme: dark) {
  :root {
    --color-text: #f0f0f0;
    --color-bg: #1a1a2e;
  }
}
```

---

## 🛠️ Hands-On Exercise

Create a **blog article page** with:
1. A fluid type scale using CSS custom properties and `clamp()`
2. Maximum reading width of `65ch`
3. Fluid spacing between sections
4. Responsive headings that scale smoothly between mobile and desktop
5. A color theme system using CSS custom properties

---

## 📝 Homework

1. Create your own **type scale** using `clamp()` with custom properties
2. Build a **landing page** using only `rem`, `clamp()`, and `%` — no `px` for sizing
3. Add dark mode support using `prefers-color-scheme`
4. Test your fluid typography from 320px to 1920px — ensure readability at all sizes

---

## 💡 Pro Tips

> 🔠 **clamp() is magic**: `font-size: clamp(1rem, 2.5vw, 2rem)` handles responsive text with zero media queries.

> 📏 **65ch rule**: For body text, `max-width: 65ch` ensures optimal readability regardless of screen size.

> 🎨 **CSS Variables:** Define all your colors, spacing, and font sizes as custom properties at the `:root` level. It makes theming and consistency effortless.

---

[← Day 1](day-01.md) | [Back to Week 4](README.md) | [Day 3 →](day-03.md)
