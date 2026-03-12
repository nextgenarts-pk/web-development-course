# 📅 Day 1: CSS Syntax, Selectors & Specificity 🎯

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- Completed Week 1 HTML fundamentals
- Personal bio page built in HTML

## 🎯 Today's Goals
- Understand how CSS works with HTML
- Learn 3 ways to add CSS to HTML
- Master CSS selectors and specificity

---

## 📖 Lesson Content

### 1. What is CSS?
**CSS** (Cascading Style Sheets) controls the **visual presentation** of HTML elements — colors, fonts, layouts, animations.

### 2. Three Ways to Add CSS

```html
<!-- 1. Inline CSS (avoid for production) -->
<p style="color: blue; font-size: 18px;">Hello World</p>

<!-- 2. Internal CSS (in the <head>) -->
<head>
    <style>
        p { color: blue; font-size: 18px; }
    </style>
</head>

<!-- 3. External CSS (recommended!) -->
<head>
    <link rel="stylesheet" href="styles.css">
</head>
```

### 3. CSS Syntax

```css
/* selector { property: value; } */
h1 {
    color: #333333;
    font-size: 32px;
    text-align: center;
}
```

### 4. CSS Selectors

```css
/* Element selector */
p { color: gray; }

/* Class selector (reusable) */
.highlight { background-color: yellow; }

/* ID selector (unique) */
#main-title { font-size: 48px; }

/* Descendant selector */
nav a { text-decoration: none; }

/* Group selector */
h1, h2, h3 { font-family: 'Arial', sans-serif; }

/* Attribute selector */
input[type="email"] { border: 2px solid blue; }

/* Pseudo-class selectors */
a:hover { color: red; }
li:first-child { font-weight: bold; }
input:focus { outline: 2px solid blue; }

/* Pseudo-element selectors */
p::first-line { font-weight: bold; }
h1::before { content: "🚀 "; }
```

### 5. Specificity Hierarchy

```
!important  → 10,000 (avoid!)
Inline      → 1,000
#id         → 100
.class      → 10
element     → 1
```

```css
/* Specificity: 1 */
p { color: black; }

/* Specificity: 10 */
.text { color: blue; }

/* Specificity: 100 */
#intro { color: red; }

/* Specificity: 11 */
p.text { color: green; }
```

---

## ✍️ Hands-On Exercise
1. Create an external `styles.css` file
2. Link it to your bio page from Week 1
3. Style headings with different colors using element selectors
4. Add a `.highlight` class and apply it to key paragraphs
5. Use pseudo-classes to change link colors on hover

---

## 📝 Homework
- Read: [MDN — CSS Selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors)
- Practice: Style 5 different elements using 5 different selector types
- Challenge: Calculate the specificity of 5 different CSS rules

---

## 💡 Tips & Best Practices
- 📁 Always use **external CSS** files for real projects
- 🏷️ Prefer **class selectors** over IDs for styling
- ⚠️ Avoid `!important` — it makes debugging difficult
- 📝 Use meaningful class names: `.nav-link` not `.blue-text`

---

[← Previous Day](../week-01/day-06.md) | [Back to Week 2](README.md) | [Next Day →](day-02.md)
