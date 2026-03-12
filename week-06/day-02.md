# 📅 Day 2: DOM Manipulation — Create, Modify, Delete ✏️

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- DOM selection and traversal from Day 1
- `querySelector`, `querySelectorAll`, DOM tree concepts

---

## 🎯 Today's Goals

- ✏️ Modify element content, attributes, and styles
- ➕ Create new elements and add them to the DOM
- ✂️ Remove and replace elements
- 🎨 Manipulate CSS classes dynamically

---

## 📘 Lesson Content

### Modifying Content

```javascript
const heading = document.querySelector("h1");

// textContent — plain text (secure, no HTML parsing)
heading.textContent = "Hello World";

// innerHTML — parses HTML (use cautiously, XSS risk with user input)
heading.innerHTML = "Hello <em>World</em>";

// innerText — visible text only (respects CSS display)
console.log(heading.innerText);
```

### Modifying Attributes

```javascript
const link = document.querySelector("a");

// Get/Set attributes
link.getAttribute("href");           // returns value
link.setAttribute("href", "#new");   // set value
link.removeAttribute("target");      // remove
link.hasAttribute("rel");            // true/false

// Direct property access (common attributes)
link.href = "https://nextgenarts.pk";
link.id = "main-link";
link.title = "Visit NextGen Arts";

// Data attributes
const card = document.querySelector(".card");
card.dataset.id;                     // reads data-id
card.dataset.category = "web";       // sets data-category
// <div class="card" data-id="1" data-category="web">
```

### Modifying Styles

```javascript
const box = document.querySelector(".box");

// Inline styles (camelCase)
box.style.backgroundColor = "#3498db";
box.style.padding = "2rem";
box.style.borderRadius = "12px";
box.style.transform = "scale(1.1)";

// Remove inline style
box.style.backgroundColor = "";

// Set multiple styles at once
Object.assign(box.style, {
  color: "white",
  fontSize: "1.2rem",
  transition: "all 0.3s ease"
});
```

### Class Manipulation

```javascript
const element = document.querySelector(".card");

// classList methods
element.classList.add("active");            // add class
element.classList.remove("hidden");         // remove class
element.classList.toggle("expanded");       // toggle class
element.classList.contains("active");       // check — returns boolean
element.classList.replace("old", "new");    // replace class

// Add multiple classes
element.classList.add("animate", "fade-in", "delay-1");

// className — replaces ALL classes (avoid usually)
element.className = "card active highlighted";
```

### Creating Elements

```javascript
// Create a new element
const card = document.createElement("div");
card.className = "card";
card.innerHTML = `
  <h3 class="card__title">New Card</h3>
  <p class="card__text">This card was created with JavaScript</p>
`;

// Append to parent
const container = document.querySelector(".container");
container.appendChild(card);        // add to end
container.prepend(card);            // add to beginning
container.append(card, otherCard);  // add multiple to end

// Insert at specific position
const reference = document.querySelector(".card:nth-child(2)");
container.insertBefore(card, reference); // insert before reference

// insertAdjacentHTML — flexible insertion
container.insertAdjacentHTML("beforebegin", "<p>Before container</p>");
container.insertAdjacentHTML("afterbegin", "<p>First child</p>");
container.insertAdjacentHTML("beforeend", "<p>Last child</p>");
container.insertAdjacentHTML("afterend", "<p>After container</p>");
```

### Removing & Replacing Elements

```javascript
// Remove element
const item = document.querySelector(".item");
item.remove();

// Remove child from parent
const parent = document.querySelector(".list");
const child = parent.querySelector(".item");
parent.removeChild(child);

// Replace element
const oldEl = document.querySelector(".old");
const newEl = document.createElement("div");
newEl.textContent = "Replaced!";
oldEl.replaceWith(newEl);

// Clone element
const original = document.querySelector(".template");
const clone = original.cloneNode(true);  // true = deep clone (includes children)
container.appendChild(clone);
```

### Document Fragments (Performance)

```javascript
// When adding many elements, use a fragment to avoid multiple reflows
const fragment = document.createDocumentFragment();

for (let i = 0; i < 100; i++) {
  const li = document.createElement("li");
  li.textContent = `Item ${i + 1}`;
  fragment.appendChild(li);
}

// Single DOM insert
document.querySelector("ul").appendChild(fragment);
```

---

## 🛠️ Hands-On Exercise

### Dynamic Card Builder

Create a page where users can add, edit, and delete cards dynamically:

```javascript
function createCard(title, description) {
  const card = document.createElement("article");
  card.className = "card";
  card.innerHTML = `
    <h3 class="card__title">${escapeHtml(title)}</h3>
    <p class="card__text">${escapeHtml(description)}</p>
    <div class="card__actions">
      <button class="btn btn--edit">Edit</button>
      <button class="btn btn--delete">Delete</button>
    </div>
  `;
  return card;
}

// Escape HTML to prevent XSS
function escapeHtml(text) {
  const div = document.createElement("div");
  div.textContent = text;
  return div.innerHTML;
}

// Add card to grid
function addCard(title, description) {
  const grid = document.querySelector(".card-grid");
  const card = createCard(title, description);
  grid.appendChild(card);
}

// Delete card
function deleteCard(button) {
  const card = button.closest(".card");
  card.classList.add("fade-out");
  card.addEventListener("animationend", () => card.remove());
}
```

---

## 📝 Homework

1. Build a **dynamic table** where users can add/remove rows and columns
2. Create a **theme builder** — buttons that change background, text color, font size dynamically
3. Build a **notification system** — `showNotification(message, type)` that creates and auto-removes toast notifications
4. Create a **star rating component** — hovering highlights stars, clicking sets the rating

---

## 💡 Pro Tips

> 🔒 **Security:** NEVER insert user input with `innerHTML`. Always use `textContent` or escape HTML first to prevent XSS attacks.

> ⚡ **Performance:** Use `DocumentFragment` when adding many elements. Each DOM modification causes the browser to recalculate layout — fragments batch these into one operation.

> 🎨 **Prefer `classList`:** Always use `classList.add/remove/toggle` over manipulating `className` directly. It's safer and won't accidentally remove other classes.

---

[← Day 1](day-01.md) | [Back to Week 6](README.md) | [Day 3 →](day-03.md)
