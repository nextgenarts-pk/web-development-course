# 📅 Day 1: DOM Selection & Traversal 🔍

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- All JavaScript fundamentals (Week 5)
- Understanding of HTML structure and elements

---

## 🎯 Today's Goals

- 🌐 Understand what the DOM is and how it works
- 🔍 Select elements by ID, class, tag, and CSS selector
- 🌳 Traverse DOM nodes (parent, children, siblings)
- 📏 Read element dimensions and positions

---

## 📘 Lesson Content

### What is the DOM?

The **Document Object Model** is a tree-like representation of your HTML document. Every HTML element becomes a **node** in this tree, and JavaScript can access and manipulate every node.

```
document
└── html
    ├── head
    │   └── title
    └── body
        ├── header
        ├── main
        │   ├── h1
        │   └── p
        └── footer
```

### Selecting Elements

```javascript
// By ID — returns single element
const header = document.getElementById("header");

// By class — returns HTMLCollection (live)
const cards = document.getElementsByClassName("card");

// By tag — returns HTMLCollection (live)
const paragraphs = document.getElementsByTagName("p");

// querySelector — returns FIRST match (CSS selector)
const firstCard = document.querySelector(".card");
const nav = document.querySelector("nav.main-nav");
const input = document.querySelector('input[type="email"]');

// querySelectorAll — returns ALL matches (NodeList, static)
const allCards = document.querySelectorAll(".card");
const listItems = document.querySelectorAll("ul.menu > li");
```

### HTMLCollection vs NodeList

```javascript
// HTMLCollection — live (updates when DOM changes), no forEach
const liveList = document.getElementsByClassName("item");

// NodeList — static (snapshot), has forEach
const staticList = document.querySelectorAll(".item");
staticList.forEach(item => console.log(item));

// Convert HTMLCollection to array for array methods
const arr = Array.from(liveList);
const arr2 = [...liveList];
```

### DOM Traversal

```javascript
const main = document.querySelector("main");

// Parent
main.parentElement;            // parent element
main.parentNode;               // parent node

// Children
main.children;                 // HTMLCollection of child elements
main.childElementCount;        // number of children
main.firstElementChild;        // first child element
main.lastElementChild;         // last child element

// Siblings
main.nextElementSibling;       // next sibling element
main.previousElementSibling;   // previous sibling element

// Closest ancestor matching selector (searches up)
const card = document.querySelector(".card__title");
const parentCard = card.closest(".card"); // finds nearest .card ancestor
```

### Reading Element Properties

```javascript
const box = document.querySelector(".box");

// Dimensions
box.offsetWidth;    // width including border + padding
box.offsetHeight;   // height including border + padding
box.clientWidth;    // width including padding (no border)
box.clientHeight;   // height including padding (no border)

// Position relative to viewport
const rect = box.getBoundingClientRect();
rect.top;      // distance from top of viewport
rect.left;     // distance from left
rect.width;    // element width
rect.height;   // element height

// Computed styles
const styles = getComputedStyle(box);
styles.backgroundColor;  // RGB value
styles.fontSize;         // "16px"
```

---

## 🛠️ Hands-On Exercise

### DOM Explorer Tool

Build an interactive page that lets you explore DOM selection:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>DOM Explorer</title>
  <style>
    .highlight { outline: 3px solid #e74c3c; background: rgba(231,76,60,0.1); }
    .info-panel { position: fixed; bottom: 0; left: 0; right: 0; background: #2c3e50; color: white; padding: 1rem; }
  </style>
</head>
<body>
  <header id="main-header">
    <h1>DOM Explorer</h1>
    <nav>
      <ul class="nav-list">
        <li><a href="#">Home</a></li>
        <li><a href="#">About</a></li>
        <li><a href="#">Contact</a></li>
      </ul>
    </nav>
  </header>

  <main>
    <section class="cards">
      <div class="card" data-id="1"><h3>Card 1</h3><p>Description</p></div>
      <div class="card" data-id="2"><h3>Card 2</h3><p>Description</p></div>
      <div class="card" data-id="3"><h3>Card 3</h3><p>Description</p></div>
    </section>
  </main>

  <div class="info-panel" id="info">Hover over any element to inspect it</div>

  <script>
    const infoPanel = document.getElementById("info");

    document.addEventListener("mouseover", (e) => {
      // Remove previous highlight
      document.querySelectorAll(".highlight").forEach(el => el.classList.remove("highlight"));

      // Add highlight to hovered element
      e.target.classList.add("highlight");

      // Show info
      const tag = e.target.tagName.toLowerCase();
      const id = e.target.id ? `#${e.target.id}` : "";
      const classes = e.target.className.replace("highlight", "").trim();
      const classStr = classes ? `.${classes.split(" ").join(".")}` : "";
      const parent = e.target.parentElement?.tagName.toLowerCase() || "none";
      const children = e.target.children.length;

      infoPanel.textContent = `<${tag}${id}${classStr}> | Parent: ${parent} | Children: ${children}`;
    });
  </script>
</body>
</html>
```

---

## 📝 Homework

1. Write a function `selectAll(selector)` that returns an array of matching elements
2. Build a **breadcrumb generator** — given any element, traverse up and show the path from `<body>`
3. Write a function that finds all elements with a specific `data-*` attribute
4. Count all elements on a page, grouped by tag name — display the results

---

## 💡 Pro Tips

> 🎯 **Best Practice:** Always prefer `querySelector` / `querySelectorAll` over older methods — they use CSS selector syntax which is more powerful and flexible.

> 🌳 **`closest()` is essential:** When handling events on nested elements, `e.target.closest(".card")` finds the nearest ancestor matching the selector — works perfectly with event delegation.

> ⚡ **Cache selectors:** Don't call `querySelector` inside loops or frequent event handlers. Store the element in a variable first.

---

[← Back to Week 6](README.md) | [Day 2 →](day-02.md)
