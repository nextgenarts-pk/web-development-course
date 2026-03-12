# 📅 Day 5: Tailwind Components & Dark Mode 🌙

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Tailwind utility classes and responsive prefixes from Days 3-4
- Understanding of Flexbox/Grid utilities in Tailwind

---

## 🎯 Today's Goals

- 🧩 Build reusable UI components with Tailwind
- 🌙 Implement dark mode with Tailwind
- 🎭 Master state modifiers (`hover:`, `focus:`, `active:`, `group-hover:`)
- ⚡ Learn Tailwind animations and transitions

---

## 📘 Lesson Content

### State Modifiers

```html
<!-- Hover -->
<button class="bg-blue-500 hover:bg-blue-700 text-white px-6 py-3 rounded-lg transition">
  Hover Me
</button>

<!-- Focus -->
<input class="border border-gray-300 focus:border-blue-500 focus:ring-2 focus:ring-blue-200 outline-none rounded-lg px-4 py-2 transition" placeholder="Focus me">

<!-- Active -->
<button class="bg-blue-500 active:bg-blue-800 active:scale-95 transition">
  Press Me
</button>

<!-- Group hover -->
<div class="group cursor-pointer">
  <h3 class="group-hover:text-blue-500 transition">Title</h3>
  <p class="group-hover:text-gray-600 transition">Subtitle changes too</p>
</div>

<!-- First/Last child -->
<ul>
  <li class="first:pt-0 last:pb-0 py-4 border-b last:border-0">Item</li>
</ul>

<!-- Disabled -->
<button class="bg-blue-500 disabled:bg-gray-300 disabled:cursor-not-allowed" disabled>
  Disabled
</button>
```

### Tailwind Button Components

```html
<!-- Primary Button -->
<button class="bg-blue-600 text-white px-6 py-3 rounded-lg font-semibold
               hover:bg-blue-700 active:scale-95
               focus:ring-4 focus:ring-blue-300
               transition duration-200">
  Primary
</button>

<!-- Outline Button -->
<button class="border-2 border-blue-600 text-blue-600 px-6 py-3 rounded-lg font-semibold
               hover:bg-blue-600 hover:text-white
               transition duration-200">
  Outline
</button>

<!-- Ghost Button -->
<button class="text-blue-600 px-6 py-3 rounded-lg font-semibold
               hover:bg-blue-50
               transition duration-200">
  Ghost
</button>

<!-- Icon Button -->
<button class="bg-gray-100 p-3 rounded-full hover:bg-gray-200 transition">
  ❤️
</button>
```

### Card Components

```html
<!-- Product Card -->
<div class="bg-white rounded-2xl shadow-lg overflow-hidden hover:shadow-xl transition duration-300 group">
  <div class="relative overflow-hidden">
    <img src="https://picsum.photos/400/250" alt="Product"
         class="w-full h-56 object-cover group-hover:scale-105 transition duration-500">
    <span class="absolute top-3 right-3 bg-red-500 text-white text-xs font-bold px-3 py-1 rounded-full">
      NEW
    </span>
  </div>
  <div class="p-6">
    <h3 class="text-lg font-bold text-gray-800 mb-2">Product Name</h3>
    <p class="text-gray-500 text-sm mb-4">Short description goes here.</p>
    <div class="flex items-center justify-between">
      <span class="text-2xl font-bold text-blue-600">$49.99</span>
      <button class="bg-blue-600 text-white px-4 py-2 rounded-lg text-sm font-semibold
                     hover:bg-blue-700 transition">
        Add to Cart
      </button>
    </div>
  </div>
</div>
```

### Dark Mode

#### Setup

```js
// tailwind.config.js
module.exports = {
  darkMode: 'class', // or 'media' for system preference
  // ...
}
```

#### Using Dark Mode Classes

```html
<body class="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100 transition-colors">

  <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg dark:shadow-gray-900/30 p-6">
    <h2 class="text-gray-800 dark:text-white font-bold text-xl">Card Title</h2>
    <p class="text-gray-600 dark:text-gray-300 mt-2">Card description text.</p>
    <button class="bg-blue-500 dark:bg-blue-600 text-white px-4 py-2 rounded-lg mt-4
                   hover:bg-blue-600 dark:hover:bg-blue-700 transition">
      Action
    </button>
  </div>

</body>
```

#### Toggle Dark Mode Script

```html
<button onclick="document.documentElement.classList.toggle('dark')"
        class="fixed bottom-4 right-4 bg-gray-200 dark:bg-gray-700 p-3 rounded-full shadow-lg">
  🌙
</button>
```

### Animations & Transitions

```html
<!-- Built-in transitions -->
<div class="transition duration-300 ease-in-out">Smooth transition</div>

<!-- Transform on hover -->
<div class="hover:scale-105 hover:-translate-y-1 transition duration-300">
  Lift effect
</div>

<!-- Built-in animations -->
<div class="animate-bounce">Bouncing</div>
<div class="animate-spin">Spinning</div>
<div class="animate-pulse">Pulsing</div>
<div class="animate-ping">Pinging</div>

<!-- Loading skeleton -->
<div class="bg-gray-200 dark:bg-gray-700 animate-pulse rounded-lg h-48 w-full"></div>
```

### Form Components

```html
<form class="max-w-md mx-auto space-y-4">
  <div>
    <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
      Email
    </label>
    <input type="email"
           class="w-full px-4 py-3 border border-gray-300 rounded-lg
                  focus:border-blue-500 focus:ring-2 focus:ring-blue-200
                  dark:bg-gray-800 dark:border-gray-600 dark:text-white
                  outline-none transition"
           placeholder="you@example.com">
  </div>

  <div>
    <label class="block text-sm font-medium text-gray-700 dark:text-gray-300 mb-1">
      Password
    </label>
    <input type="password"
           class="w-full px-4 py-3 border border-gray-300 rounded-lg
                  focus:border-blue-500 focus:ring-2 focus:ring-blue-200
                  dark:bg-gray-800 dark:border-gray-600 dark:text-white
                  outline-none transition"
           placeholder="••••••••">
  </div>

  <button class="w-full bg-blue-600 text-white py-3 rounded-lg font-semibold
                 hover:bg-blue-700 active:scale-[0.98] transition">
    Sign In
  </button>
</form>
```

---

## 🛠️ Hands-On Exercise

Build a **component library page** showcasing:
1. 3 button variants (primary, outline, ghost)
2. A product card with hover effects
3. A form with styled inputs
4. Dark mode toggle
5. A loading skeleton card (using `animate-pulse`)

---

## 📝 Homework

1. Build a **login/signup page** with dark mode support
2. Create an **alert/notification component** in 4 colors (success, error, warning, info)
3. Build a **testimonial carousel card** with user avatar, quote, and name
4. Add dark mode to your Week 3 portfolio (convert to Tailwind)

---

## 💡 Pro Tips

> 🌙 **Dark Mode Testing:** Toggle the `dark` class on the `<html>` element in DevTools to quickly test dark mode.

> 🧩 **@apply Directive:** If you're repeating the same class combinations, extract them with `@apply` in your CSS: `.btn-primary { @apply bg-blue-600 text-white px-6 py-3 rounded-lg; }`

> ⚡ **group/peer:** `group-hover:` works on children when hovering a parent. `peer-focus:` works on siblings for form validation styling.

---

[← Day 4](day-04.md) | [Back to Week 4](README.md) | [Day 6 →](day-06.md)
