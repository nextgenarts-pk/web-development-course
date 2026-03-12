# 📅 Day 4: Semantic HTML — header, nav, main, section, footer 🏗️

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- HTML5 fundamentals from Day 3
- Understanding of tags, elements, and attributes

---

## 🎯 Today's Goals

- Understand what semantic HTML means and why it matters
- Learn all major semantic HTML5 elements
- Build a properly structured webpage using semantic tags
- Understand accessibility benefits of semantic HTML

---

## 📖 Lesson Content

### 1. What is Semantic HTML?

**Semantic HTML** uses tags that describe their **meaning** rather than just appearance.

```html
<!-- Non-semantic (bad) -->
<div id="header">
    <div id="nav">...</div>
</div>

<!-- Semantic (good) -->
<header>
    <nav>...</nav>
</header>
```

**Why Semantic HTML?**
- ♿ **Accessibility** — Screen readers understand the page structure
- 🔍 **SEO** — Search engines rank semantic pages higher
- 👨‍💻 **Readability** — Easier for developers to understand code
- 📱 **Maintainability** — Cleaner, more organized code

### 2. Semantic Layout Elements

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Semantic HTML Example</title>
</head>
<body>

    <!-- Site header with logo and navigation -->
    <header>
        <h1>NextGen Arts</h1>
        <nav>
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#about">About</a></li>
                <li><a href="#courses">Courses</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>

    <!-- Main content area -->
    <main>
        <!-- Independent section -->
        <section id="about">
            <h2>About Us</h2>
            <p>We provide world-class tech education.</p>
        </section>

        <!-- Blog-style article -->
        <article>
            <h2>Why Learn Web Development?</h2>
            <p>Web development is one of the most in-demand skills...</p>
            <time datetime="2026-03-12">March 12, 2026</time>
        </article>

        <!-- Sidebar content -->
        <aside>
            <h3>Quick Links</h3>
            <ul>
                <li><a href="#">HTML Reference</a></li>
                <li><a href="#">CSS Reference</a></li>
            </ul>
        </aside>
    </main>

    <!-- Site footer -->
    <footer>
        <p>&copy; 2026 NextGen Arts. All rights reserved.</p>
    </footer>

</body>
</html>
```

### 3. Semantic Elements Reference

| Element | Purpose |
|---------|---------|
| `<header>` | Introductory content, logo, navigation |
| `<nav>` | Navigation links |
| `<main>` | Main unique content of the page |
| `<section>` | Thematic grouping of content |
| `<article>` | Self-contained, reusable content (blog post, news) |
| `<aside>` | Sidebar or tangentially related content |
| `<footer>` | Footer with copyright, links, contact info |
| `<figure>` | Self-contained media (image + caption) |
| `<figcaption>` | Caption for a `<figure>` |
| `<time>` | Date/time representation |
| `<mark>` | Highlighted/marked text |
| `<details>` | Expandable/collapsible content |
| `<summary>` | Summary heading for `<details>` |

### 4. Figure & Figcaption

```html
<figure>
    <img src="gilgit.jpg" alt="Beautiful Gilgit Baltistan landscape">
    <figcaption>The stunning mountains of Gilgit Baltistan, Pakistan</figcaption>
</figure>
```

### 5. Details & Summary

```html
<details>
    <summary>What will I learn in this course?</summary>
    <p>You'll learn HTML, CSS, JavaScript, React, Node.js, MongoDB, and AI integration!</p>
</details>
```

---

## ✍️ Hands-On Exercise

Build a **NextGen Arts Landing Page** using only semantic HTML:

1. `<header>` with a logo text and `<nav>` with 4 links
2. `<main>` containing:
   - A `<section>` for "About Us"
   - An `<article>` for a course description
   - An `<aside>` for quick contact info
3. `<footer>` with copyright text
4. Use `<figure>` and `<figcaption>` for at least one image
5. Use `<details>` and `<summary>` for an FAQ

---

## 📝 Homework

- Read: [MDN — Document and website structure](https://developer.mozilla.org/en-US/docs/Learn/HTML/Introduction_to_HTML/Document_and_website_structure)
- Practice: Convert your Day 3 page to use semantic HTML
- Challenge: View 3 websites and identify their semantic structure using DevTools

---

## 💡 Tips & Best Practices

- 📦 Use `<section>` for grouping related content with a heading
- 📰 Use `<article>` for content that makes sense on its own
- 🧭 Use `<nav>` only for major navigation blocks
- ⚠️ Don't use semantic tags just for styling — use them for meaning

---

[← Previous Day](day-03.md) | [Back to Week 1](README.md) | [Next Day →](day-05.md)
