# 📅 Day 3: Tailwind CSS Setup & Fundamentals 🌬️

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Solid HTML and CSS knowledge
- Understanding of responsive design from Days 1-2
- Node.js installed on your machine

---

## 🎯 Today's Goals

- 🌬️ Understand utility-first CSS philosophy
- ⚙️ Set up Tailwind CSS in a project
- 🎨 Learn core Tailwind utility classes
- 📦 Understand Tailwind's design system (colors, spacing, sizing)

---

## 📘 Lesson Content

### What is Tailwind CSS?

Tailwind is a **utility-first CSS framework** — instead of writing custom CSS, you compose designs using small, single-purpose utility classes directly in HTML.

```html
<!-- Traditional CSS approach -->
<button class="primary-button">Click Me</button>

<!-- Tailwind approach -->
<button class="bg-blue-500 text-white px-6 py-3 rounded-lg hover:bg-blue-600 transition">
  Click Me
</button>
```

### Setting Up Tailwind CSS

#### Method 1: CDN (Quick Start — Development Only)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.tailwindcss.com"></script>
  <title>Tailwind Demo</title>
</head>
<body class="bg-gray-100">
  <h1 class="text-3xl font-bold text-blue-600 p-8">Hello Tailwind!</h1>
</body>
</html>
```

#### Method 2: Tailwind CLI (Recommended for Projects)

```bash
# Initialize project
npm init -y

# Install Tailwind CSS
npm install -D tailwindcss

# Create config file
npx tailwindcss init
```

**tailwind.config.js:**
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./*.html", "./src/**/*.{html,js}"],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

**src/input.css:**
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**Build command:**
```bash
npx tailwindcss -i ./src/input.css -o ./dist/output.css --watch
```

### Core Utility Classes

#### Colors

```html
<!-- Text colors -->
<p class="text-red-500">Red text</p>
<p class="text-blue-700">Dark blue text</p>
<p class="text-gray-600">Gray text</p>

<!-- Background colors -->
<div class="bg-white">White bg</div>
<div class="bg-blue-100">Light blue bg</div>
<div class="bg-slate-900">Dark bg</div>

<!-- Color scale: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950 -->
```

#### Spacing (Padding & Margin)

```html
<!-- Padding: p-{size} -->
<div class="p-4">All sides padding (1rem)</div>
<div class="px-6 py-3">Horizontal 1.5rem, Vertical 0.75rem</div>
<div class="pt-8 pb-4">Top 2rem, Bottom 1rem</div>

<!-- Margin: m-{size} -->
<div class="m-4">All sides margin</div>
<div class="mx-auto">Centered horizontally</div>
<div class="mt-8 mb-4">Top & bottom margin</div>

<!-- Scale: 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 5, 6, 7, 8, 9, 10, 11, 12, 14, 16, 20, 24, ... -->
<!-- 1 unit = 0.25rem = 4px -->
```

#### Typography

```html
<!-- Font size -->
<p class="text-sm">Small (0.875rem)</p>
<p class="text-base">Base (1rem)</p>
<p class="text-lg">Large (1.125rem)</p>
<p class="text-2xl">2XL (1.5rem)</p>
<p class="text-4xl">4XL (2.25rem)</p>

<!-- Font weight -->
<p class="font-normal">Normal (400)</p>
<p class="font-medium">Medium (500)</p>
<p class="font-semibold">Semibold (600)</p>
<p class="font-bold">Bold (700)</p>

<!-- Text alignment -->
<p class="text-center">Centered</p>
<p class="text-right">Right aligned</p>

<!-- Line height -->
<p class="leading-relaxed">Relaxed line height (1.625)</p>
```

#### Sizing

```html
<!-- Width -->
<div class="w-full">100% width</div>
<div class="w-1/2">50% width</div>
<div class="w-64">16rem width</div>
<div class="max-w-4xl mx-auto">Max width container</div>

<!-- Height -->
<div class="h-screen">100vh height</div>
<div class="h-64">16rem height</div>
<div class="min-h-screen">Min 100vh</div>
```

#### Borders & Rounded

```html
<div class="border border-gray-300">1px border</div>
<div class="border-2 border-blue-500">2px blue border</div>
<div class="rounded">Slightly rounded (0.25rem)</div>
<div class="rounded-lg">Rounded (0.5rem)</div>
<div class="rounded-xl">More rounded (0.75rem)</div>
<div class="rounded-full">Fully rounded (circle)</div>
```

#### Shadows

```html
<div class="shadow">Default shadow</div>
<div class="shadow-md">Medium shadow</div>
<div class="shadow-lg">Large shadow</div>
<div class="shadow-xl">XL shadow</div>
```

---

## 🛠️ Hands-On Exercise

### Build a Simple Card with Tailwind

```html
<div class="max-w-sm mx-auto mt-10 bg-white rounded-xl shadow-lg overflow-hidden">
  <img class="w-full h-48 object-cover" src="https://picsum.photos/400/200" alt="Card image">
  <div class="p-6">
    <h2 class="text-xl font-bold text-gray-800 mb-2">Web Development Course</h2>
    <p class="text-gray-600 mb-4">Learn full stack web development from scratch with AI integration.</p>
    <div class="flex items-center justify-between">
      <span class="text-2xl font-bold text-blue-600">$99</span>
      <button class="bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600 transition">
        Enroll Now
      </button>
    </div>
  </div>
</div>
```

---

## 📝 Homework

1. Set up Tailwind CSS using the CLI method in a new project
2. Build a **hero section** with a heading, paragraph, and CTA button using only Tailwind classes
3. Create a **card grid** with 3 cards using Tailwind utility classes
4. Explore the [Tailwind Documentation](https://tailwindcss.com/docs) and bookmark frequently-used sections

---

## 💡 Pro Tips

> 🌬️ **VS Code Extension:** Install "Tailwind CSS IntelliSense" for autocomplete, linting, and hover previews of Tailwind classes.

> 🎨 **Color System:** Tailwind uses a 50-950 scale for each color. 50 is lightest, 950 is darkest. 500 is the "base" shade.

> 📦 **CDN vs CLI:** Use the CDN for quick experiments and learning. Switch to the CLI setup for real projects — it enables tree-shaking and customization.

---

[← Day 2](day-02.md) | [Back to Week 4](README.md) | [Day 4 →](day-04.md)
