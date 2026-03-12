# 📅 Day 4: Event Delegation, Bubbling & Forms 📋

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Event listeners and the event object (Day 3)
- DOM manipulation methods (Day 2)

---

## 🎯 Today's Goals

- 🧊 Understand event bubbling and capturing phases
- 🎯 Master event delegation pattern
- 📋 Handle form inputs and validation
- 🔄 Build dynamic lists with event delegation

---

## 📘 Lesson Content

### Event Propagation: Bubbling & Capturing

When you click a nested element, the event travels through three phases:

```
1. CAPTURING  — document → html → body → ... → target (top-down)
2. TARGET     — event fires on the clicked element
3. BUBBLING   — target → ... → body → html → document (bottom-up)
```

```javascript
// By default, listeners fire during BUBBLING phase
document.querySelector(".parent").addEventListener("click", (e) => {
  console.log("Parent clicked (bubbling)");
});

// Use third argument for CAPTURING phase
document.querySelector(".parent").addEventListener("click", (e) => {
  console.log("Parent clicked (capturing)");
}, true); // or { capture: true }

// Stop propagation — prevents event from continuing
element.addEventListener("click", (e) => {
  e.stopPropagation(); // stops bubbling up
});

// Stop all handlers on this element too
element.addEventListener("click", (e) => {
  e.stopImmediatePropagation();
});
```

### Event Delegation

Instead of adding listeners to every child, add ONE listener to the parent:

```javascript
// ❌ Bad — listener on every item (slow, doesn't handle new items)
document.querySelectorAll(".list-item").forEach(item => {
  item.addEventListener("click", () => {
    console.log("Item clicked:", item.textContent);
  });
});

// ✅ Good — ONE listener on parent (fast, handles dynamic items)
document.querySelector(".list").addEventListener("click", (e) => {
  const item = e.target.closest(".list-item");
  if (!item) return; // click wasn't on an item

  console.log("Item clicked:", item.textContent);
});
```

### Why Delegation is Better

```javascript
const list = document.querySelector("#todo-list");

// Works for ALL items, including dynamically added ones!
list.addEventListener("click", (e) => {
  // Handle delete button
  if (e.target.closest(".btn-delete")) {
    const item = e.target.closest(".todo-item");
    item.remove();
  }

  // Handle complete checkbox
  if (e.target.closest(".checkbox")) {
    const item = e.target.closest(".todo-item");
    item.classList.toggle("completed");
  }

  // Handle edit button
  if (e.target.closest(".btn-edit")) {
    const item = e.target.closest(".todo-item");
    const title = item.querySelector(".todo-title");
    title.contentEditable = true;
    title.focus();
  }
});
```

### Form Handling

```javascript
const form = document.querySelector("#signup-form");

form.addEventListener("submit", (e) => {
  e.preventDefault();

  // Method 1: FormData API
  const formData = new FormData(form);
  const data = Object.fromEntries(formData);
  console.log(data); // { name: "Ali", email: "ali@example.com", ... }

  // Method 2: Individual access
  const name = form.elements.name.value;
  const email = form.elements.email.value;

  // Method 3: querySelector
  const password = form.querySelector("#password").value;
});
```

### Form Validation

```javascript
function validateForm(data) {
  const errors = {};

  if (!data.name || data.name.trim().length < 2) {
    errors.name = "Name must be at least 2 characters";
  }

  if (!data.email || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(data.email)) {
    errors.email = "Please enter a valid email address";
  }

  if (!data.password || data.password.length < 8) {
    errors.password = "Password must be at least 8 characters";
  }

  return {
    isValid: Object.keys(errors).length === 0,
    errors
  };
}

// Show/clear errors
function showErrors(errors) {
  // Clear previous errors
  document.querySelectorAll(".error-message").forEach(el => el.remove());
  document.querySelectorAll(".input-error").forEach(el => el.classList.remove("input-error"));

  for (const [field, message] of Object.entries(errors)) {
    const input = document.querySelector(`[name="${field}"]`);
    input.classList.add("input-error");

    const errorEl = document.createElement("span");
    errorEl.className = "error-message";
    errorEl.textContent = message;
    input.parentElement.appendChild(errorEl);
  }
}

// Real-time validation
form.addEventListener("input", (e) => {
  const field = e.target.name;
  if (!field) return;

  const data = Object.fromEntries(new FormData(form));
  const { errors } = validateForm(data);

  // Clear this field's error if fixed
  const input = form.querySelector(`[name="${field}"]`);
  const existingError = input.parentElement.querySelector(".error-message");
  if (!errors[field] && existingError) {
    input.classList.remove("input-error");
    existingError.remove();
  }
});
```

---

## 🛠️ Hands-On Exercise

### Dynamic Contact List with Delegation

```html
<div class="contact-app">
  <form id="addContact">
    <input type="text" name="name" placeholder="Name" required>
    <input type="email" name="email" placeholder="Email" required>
    <input type="tel" name="phone" placeholder="Phone">
    <button type="submit">Add Contact</button>
  </form>
  <ul id="contactList" class="contact-list"></ul>
</div>

<script>
  const form = document.getElementById("addContact");
  const list = document.getElementById("contactList");

  // Add contact
  form.addEventListener("submit", (e) => {
    e.preventDefault();
    const data = Object.fromEntries(new FormData(form));

    const li = document.createElement("li");
    li.className = "contact-item";
    li.innerHTML = `
      <div class="contact-info">
        <strong>${escapeHtml(data.name)}</strong>
        <span>${escapeHtml(data.email)}</span>
        <span>${escapeHtml(data.phone || "—")}</span>
      </div>
      <div class="contact-actions">
        <button class="btn-edit">Edit</button>
        <button class="btn-delete">Delete</button>
      </div>
    `;
    list.appendChild(li);
    form.reset();
    form.elements.name.focus();
  });

  function escapeHtml(text) {
    const div = document.createElement("div");
    div.textContent = text;
    return div.innerHTML;
  }

  // Event delegation for all actions
  list.addEventListener("click", (e) => {
    const item = e.target.closest(".contact-item");
    if (!item) return;

    if (e.target.closest(".btn-delete")) {
      item.remove();
    }

    if (e.target.closest(".btn-edit")) {
      const name = item.querySelector("strong");
      const newName = prompt("Edit name:", name.textContent);
      if (newName !== null && newName.trim()) {
        name.textContent = newName.trim();
      }
    }
  });
</script>
```

---

## 📝 Homework

1. Build a **registration form** with real-time validation (name, email, password, confirm password)
2. Add **search/filter** functionality to the contact list using `input` event on a search field
3. Create a **FAQ accordion** using event delegation — click a question to show/hide its answer
4. Build a **sortable table** — click column headers to sort rows by that column

---

## 💡 Pro Tips

> 🎯 **Always delegate:** If a list can have items added dynamically, use event delegation on the parent. Individual listeners on each item won't work for new items.

> 📋 **FormData:** Use `new FormData(form)` and `Object.fromEntries()` instead of manually reading each input — it's cleaner and handles all input types.

> 🔒 **Always escape HTML:** When inserting user content into the DOM, use `textContent` or escape HTML entities to prevent XSS injection.

---

[← Day 3](day-03.md) | [Back to Week 6](README.md) | [Day 5 →](day-05.md)
