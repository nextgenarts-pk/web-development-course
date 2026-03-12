# 📅 Day 1: Media Queries & Mobile-First Design 📱

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- HTML5 and CSS3 fundamentals
- Flexbox & Grid layouts from Week 3

---

## 🎯 Today's Goals

- 📱 Understand responsive web design principles
- 📐 Write CSS media queries
- 🏗️ Apply mobile-first design methodology
- 📊 Define and use common breakpoints

---

## 📘 Lesson Content

### The Viewport Meta Tag

```html
<!-- REQUIRED for responsive design -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

### Mobile-First vs Desktop-First

**Mobile-First (Recommended):** Write base styles for mobile, then add complexity for larger screens using `min-width`.

```css
/* Mobile first — base styles for small screens */
.container {
  padding: 1rem;
}

/* Tablet and up */
@media (min-width: 768px) {
  .container {
    padding: 2rem;
    max-width: 720px;
    margin: 0 auto;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .container {
    max-width: 960px;
  }
}

/* Large desktop */
@media (min-width: 1280px) {
  .container {
    max-width: 1200px;
  }
}
```

### Common Breakpoints

| Breakpoint | Width | Target |
|-----------|-------|--------|
| `xs` | 0 - 575px | Small phones |
| `sm` | 576px+ | Large phones |
| `md` | 768px+ | Tablets |
| `lg` | 1024px+ | Laptops |
| `xl` | 1280px+ | Desktops |
| `2xl` | 1536px+ | Large screens |

### Media Query Syntax

```css
/* Min-width (mobile-first) */
@media (min-width: 768px) { }

/* Max-width (desktop-first) */
@media (max-width: 767px) { }

/* Range */
@media (min-width: 768px) and (max-width: 1023px) { }

/* Orientation */
@media (orientation: landscape) { }

/* Multiple conditions */
@media (min-width: 768px) and (orientation: landscape) { }

/* Hover capability */
@media (hover: hover) {
  .card:hover { transform: translateY(-5px); }
}

/* Prefers reduced motion */
@media (prefers-reduced-motion: reduce) {
  * { animation: none !important; transition: none !important; }
}
```

### Responsive Navigation Pattern

```css
/* Mobile: hamburger menu */
.nav__links {
  display: none;
  flex-direction: column;
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.nav__links.active {
  display: flex;
}

.nav__toggle {
  display: block;
  cursor: pointer;
}

/* Desktop: horizontal links */
@media (min-width: 768px) {
  .nav__links {
    display: flex;
    flex-direction: row;
    position: static;
    box-shadow: none;
    gap: 1.5rem;
  }

  .nav__toggle {
    display: none;
  }
}
```

### Responsive Grid Columns

```css
.grid {
  display: grid;
  gap: 1.5rem;
  grid-template-columns: 1fr; /* Mobile: 1 column */
}

@media (min-width: 768px) {
  .grid { grid-template-columns: repeat(2, 1fr); } /* Tablet: 2 columns */
}

@media (min-width: 1024px) {
  .grid { grid-template-columns: repeat(3, 1fr); } /* Desktop: 3 columns */
}
```

---

## 🛠️ Hands-On Exercise

Build a responsive page with:
1. A navigation that switches between hamburger (mobile) and horizontal (desktop)
2. A hero section that changes padding and font size at breakpoints
3. A content grid: 1 column on mobile, 2 on tablet, 3 on desktop
4. A footer that stacks on mobile, spreads on desktop

---

## 📝 Homework

1. Take your Week 3 portfolio and refine its responsive behavior at all breakpoints
2. Add `prefers-reduced-motion` media query to disable animations for accessibility
3. Test your page at 320px, 768px, 1024px, and 1440px widths

---

## 💡 Pro Tips

> 📱 **Mobile First:** Always start with mobile styles as your base. It's easier to add complexity (for larger screens) than to remove it.

> 🧪 **Testing:** Use browser DevTools responsive mode (Ctrl+Shift+M in Chrome) to test different screen sizes instantly.

> ♿ **Accessibility:** The `prefers-reduced-motion` and `prefers-color-scheme` media queries are essential for user experience.

---

[← Back to Week 4](README.md) | [Day 2 →](day-02.md)
