# 📅 Day 3: HTML5 Fundamentals — Structure, Tags & Elements 📝

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- VS Code installed with extensions (Day 2)
- Git configured and GitHub account ready

---

## 🎯 Today's Goals

- Understand HTML document structure
- Learn essential HTML tags and elements
- Understand the difference between tags, elements, and attributes
- Create your first HTML page

---

## 📖 Lesson Content

### 1. What is HTML?

**HTML** (HyperText Markup Language) is the standard language for creating web pages. It defines the **structure** and **content** of a webpage.

### 2. HTML Document Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Page</title>
</head>
<body>
    <h1>Hello, World!</h1>
    <p>This is my first HTML page.</p>
</body>
</html>
```

**Breakdown:**
- `<!DOCTYPE html>` — Tells the browser this is HTML5
- `<html>` — Root element of the page
- `<head>` — Contains metadata (title, charset, viewport)
- `<body>` — Contains visible content

### 3. HTML Tags, Elements & Attributes

```html
<!-- Tag: the opening and closing markers -->
<p>This is a paragraph.</p>

<!-- Element: tag + content -->
<h1>This entire thing is an element</h1>

<!-- Attributes: extra info about an element -->
<a href="https://nextgenarts.pk" target="_blank">Visit NextGen Arts</a>
<!--  ^^^^                        ^^^^^^^^^^^^^^  = Attributes -->
```

### 4. Heading Tags (h1 — h6)

```html
<h1>Main Heading (Largest)</h1>
<h2>Sub Heading</h2>
<h3>Section Heading</h3>
<h4>Sub-Section Heading</h4>
<h5>Minor Heading</h5>
<h6>Smallest Heading</h6>
```

### 5. Text Elements

```html
<p>This is a paragraph of text.</p>
<strong>This text is bold/important.</strong>
<em>This text is italicized/emphasized.</em>
<br> <!-- Line break -->
<hr> <!-- Horizontal rule (line) -->
<blockquote>This is a quoted text block.</blockquote>
<code>This is inline code.</code>
<pre>This text preserves    spacing and line breaks.</pre>
```

### 6. Lists

```html
<!-- Unordered List (bullet points) -->
<ul>
    <li>HTML</li>
    <li>CSS</li>
    <li>JavaScript</li>
</ul>

<!-- Ordered List (numbered) -->
<ol>
    <li>Learn HTML</li>
    <li>Learn CSS</li>
    <li>Learn JavaScript</li>
</ol>
```

### 7. Links & Images

```html
<!-- Links -->
<a href="https://nextgenarts.pk">Visit NextGen Arts</a>
<a href="about.html">About Page</a>
<a href="#section1">Jump to Section 1</a>

<!-- Images -->
<img src="photo.jpg" alt="My Photo" width="300">
```

### 8. Div & Span

```html
<!-- Div: block-level container -->
<div>
    <h2>About Me</h2>
    <p>I am learning web development!</p>
</div>

<!-- Span: inline container -->
<p>My favorite color is <span style="color: blue;">blue</span>.</p>
```

---

## ✍️ Hands-On Exercise

Create a file called `index.html` with:
1. A proper HTML5 document structure
2. A main heading with your name
3. A paragraph introducing yourself
4. An unordered list of 5 skills you want to learn
5. An ordered list of your top 3 hobbies
6. A link to your GitHub profile
7. An image (use any placeholder image)

---

## 📝 Homework

- Read: [MDN — HTML Elements Reference](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
- Practice: Create a second HTML page called `hobbies.html` and link it from your `index.html`
- Explore: View the source code of your favorite website (Right-click → View Page Source)

---

## 💡 Tips & Best Practices

- 🏷️ Always use lowercase for tag names (`<div>` not `<DIV>`)
- 📝 Always include the `alt` attribute on images (accessibility!)
- 🔤 Use only one `<h1>` per page (for SEO)
- ✅ Always close your tags properly

---

[← Previous Day](day-02.md) | [Back to Week 1](README.md) | [Next Day →](day-04.md)
