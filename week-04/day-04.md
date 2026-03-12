# 📅 Day 4: Tailwind Layouts, Spacing & Responsive Prefixes 📐

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Tailwind CSS setup and core utility classes from Day 3
- Flexbox and Grid concepts from Week 3

---

## 🎯 Today's Goals

- 📐 Use Tailwind's Flexbox and Grid utilities
- 📱 Master responsive prefixes (`sm:`, `md:`, `lg:`, `xl:`, `2xl:`)
- 📏 Build complex responsive layouts with Tailwind
- 🔧 Customize Tailwind configuration

---

## 📘 Lesson Content

### Flex Utilities in Tailwind

```html
<!-- Flexbox container -->
<div class="flex items-center justify-between gap-4">
  <div>Left</div>
  <div>Center</div>
  <div>Right</div>
</div>

<!-- Flex direction -->
<div class="flex flex-col">Vertical</div>
<div class="flex flex-row">Horizontal</div>

<!-- Flex wrap -->
<div class="flex flex-wrap gap-4">
  <div class="flex-1 min-w-[250px]">Card 1</div>
  <div class="flex-1 min-w-[250px]">Card 2</div>
  <div class="flex-1 min-w-[250px]">Card 3</div>
</div>

<!-- Justify & Align -->
<!-- justify-start | justify-center | justify-end | justify-between | justify-around | justify-evenly -->
<!-- items-start | items-center | items-end | items-stretch | items-baseline -->
```

### Grid Utilities in Tailwind

```html
<!-- Grid columns -->
<div class="grid grid-cols-3 gap-6">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
</div>

<!-- Spanning columns -->
<div class="grid grid-cols-4 gap-4">
  <div class="col-span-2">Spans 2 columns</div>
  <div>Col 3</div>
  <div>Col 4</div>
</div>

<!-- Grid rows -->
<div class="grid grid-rows-3 grid-flow-col gap-4">
  <div class="row-span-2">Spans 2 rows</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>

<!-- Auto-fit equivalent -->
<div class="grid grid-cols-[repeat(auto-fit,minmax(280px,1fr))] gap-6">
  <!-- Cards automatically responsive -->
</div>
```

### Responsive Prefixes

Tailwind uses **mobile-first** breakpoint prefixes:

| Prefix | Min Width | CSS |
|--------|-----------|-----|
| (none) | 0px | Default (mobile) |
| `sm:` | 640px | `@media (min-width: 640px)` |
| `md:` | 768px | `@media (min-width: 768px)` |
| `lg:` | 1024px | `@media (min-width: 1024px)` |
| `xl:` | 1280px | `@media (min-width: 1280px)` |
| `2xl:` | 1536px | `@media (min-width: 1536px)` |

```html
<!-- Mobile: 1 col, Tablet: 2 cols, Desktop: 3 cols -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <div class="p-4 bg-white rounded-lg shadow">Card 1</div>
  <div class="p-4 bg-white rounded-lg shadow">Card 2</div>
  <div class="p-4 bg-white rounded-lg shadow">Card 3</div>
</div>

<!-- Font size: mobile small, tablet medium, desktop large -->
<h1 class="text-2xl md:text-4xl lg:text-6xl font-bold">
  Responsive Heading
</h1>

<!-- Padding changes at breakpoints -->
<section class="px-4 md:px-8 lg:px-16 py-8 md:py-16">
  Content
</section>

<!-- Hide/show at breakpoints -->
<div class="hidden md:block">Only visible on tablet+</div>
<div class="block md:hidden">Only visible on mobile</div>
```

### Responsive Navigation

```html
<nav class="bg-white shadow-md">
  <div class="max-w-6xl mx-auto px-4 py-3 flex items-center justify-between">
    <a href="#" class="text-xl font-bold text-blue-600">NextGen Arts</a>

    <!-- Desktop links (hidden on mobile) -->
    <ul class="hidden md:flex gap-6">
      <li><a href="#" class="text-gray-700 hover:text-blue-600">Home</a></li>
      <li><a href="#" class="text-gray-700 hover:text-blue-600">Courses</a></li>
      <li><a href="#" class="text-gray-700 hover:text-blue-600">About</a></li>
      <li><a href="#" class="text-gray-700 hover:text-blue-600">Contact</a></li>
    </ul>

    <!-- Mobile menu button (hidden on desktop) -->
    <button class="md:hidden text-gray-700">☰</button>
  </div>
</nav>
```

### Container Utility

```html
<!-- Centered container with responsive max-width -->
<div class="container mx-auto px-4">
  Content is automatically constrained at each breakpoint
</div>
```

### Customizing Tailwind Config

```js
// tailwind.config.js
module.exports = {
  content: ["./*.html"],
  theme: {
    extend: {
      colors: {
        brand: {
          50:  '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a5f',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      spacing: {
        '128': '32rem',
      },
    },
  },
  plugins: [],
}
```

```html
<!-- Use custom values -->
<div class="bg-brand-500 text-brand-50 font-sans p-128">
  Custom design system
</div>
```

---

## 🛠️ Hands-On Exercise

### Build a Responsive Landing Page

Create a landing page with Tailwind that includes:
1. Responsive navigation (hamburger on mobile, horizontal on desktop)
2. Hero section with responsive text sizing
3. Features grid (1 col → 2 col → 3 col)
4. Testimonial section
5. CTA section
6. Footer with multiple columns

All responsive, all using Tailwind utility classes only.

---

## 📝 Homework

1. Build a **pricing page** with 3 pricing cards that stack on mobile and sit side-by-side on desktop
2. Create a **blog layout** with sidebar that collapses on mobile
3. Customize your `tailwind.config.js` with brand colors and custom fonts
4. Practice using every responsive prefix (`sm:`, `md:`, `lg:`, `xl:`)

---

## 💡 Pro Tips

> 📱 **Read it as:** `md:text-2xl` means "at medium screens **and above**, use text-2xl". Unprefixed classes are the mobile/default styles.

> 🎯 **Arbitrary Values:** Need a specific value? Use square brackets: `w-[350px]`, `text-[#1a1a2e]`, `grid-cols-[200px_1fr_200px]`

> 🧹 **Tailwind Merge:** In React projects, use the `tailwind-merge` package to intelligently merge conflicting classes.

---

[← Day 3](day-03.md) | [Back to Week 4](README.md) | [Day 5 →](day-05.md)
