# 📅 Day 3: Events — Listeners, Types & Event Object 🎯

> **Week 6 — DOM Manipulation & Events** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- DOM selection, traversal, and manipulation (Days 1-2)
- JavaScript functions and objects (Week 5)

---

## 🎯 Today's Goals

- 🎯 Add and remove event listeners
- 🖱️ Handle mouse, keyboard, and input events
- 📦 Understand the event object and its properties
- 🛑 Use `preventDefault()` and `stopPropagation()`

---

## 📘 Lesson Content

### Adding Event Listeners

```javascript
const button = document.querySelector(".btn");

// addEventListener (recommended)
button.addEventListener("click", function (e) {
  console.log("Button clicked!", e);
});

// Arrow function
button.addEventListener("click", (e) => {
  console.log("Clicked at:", e.clientX, e.clientY);
});

// Named function (enables removal)
function handleClick(e) {
  console.log("Handled!");
}
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);

// Once option — fires only once
button.addEventListener("click", () => {
  console.log("This fires once!");
}, { once: true });
```

### Common Event Types

```javascript
// Mouse Events
element.addEventListener("click", handler);       // single click
element.addEventListener("dblclick", handler);     // double click
element.addEventListener("mouseenter", handler);   // hover in (no bubble)
element.addEventListener("mouseleave", handler);   // hover out (no bubble)
element.addEventListener("mouseover", handler);    // hover in (bubbles)
element.addEventListener("mouseout", handler);     // hover out (bubbles)
element.addEventListener("mousedown", handler);    // button pressed
element.addEventListener("mouseup", handler);      // button released
element.addEventListener("mousemove", handler);    // mouse moving
element.addEventListener("contextmenu", handler);  // right-click

// Keyboard Events
document.addEventListener("keydown", handler);     // key pressed
document.addEventListener("keyup", handler);       // key released

// Form/Input Events
input.addEventListener("input", handler);          // value changes (real-time)
input.addEventListener("change", handler);         // value committed (blur/enter)
input.addEventListener("focus", handler);          // element gains focus
input.addEventListener("blur", handler);           // element loses focus
form.addEventListener("submit", handler);          // form submitted

// Window/Document Events
window.addEventListener("load", handler);          // page fully loaded
window.addEventListener("DOMContentLoaded", handler); // DOM ready
window.addEventListener("resize", handler);        // window resized
window.addEventListener("scroll", handler);        // page scrolled
```

### The Event Object

```javascript
// Mouse event
document.addEventListener("click", (e) => {
  e.target;          // element that was clicked
  e.currentTarget;   // element the listener is attached to
  e.type;            // "click"
  e.clientX;         // X position relative to viewport
  e.clientY;         // Y position relative to viewport
  e.pageX;           // X position relative to document
  e.pageY;           // Y position relative to document
  e.button;          // 0=left, 1=middle, 2=right
  e.shiftKey;        // true if Shift was held
  e.ctrlKey;         // true if Ctrl was held
  e.altKey;          // true if Alt was held
});

// Keyboard event
document.addEventListener("keydown", (e) => {
  e.key;             // "Enter", "a", "ArrowUp", " "
  e.code;            // "Enter", "KeyA", "ArrowUp", "Space"
  e.repeat;          // true if key is held down
  e.shiftKey;
  e.ctrlKey;
  e.altKey;
});

// Input event
input.addEventListener("input", (e) => {
  e.target.value;    // current input value
  e.data;            // character that was entered
  e.inputType;       // "insertText", "deleteContentBackward", etc.
});
```

### Preventing Default Behavior

```javascript
// Prevent form submission (page reload)
const form = document.querySelector("form");
form.addEventListener("submit", (e) => {
  e.preventDefault();
  const data = new FormData(form);
  console.log("Form data:", Object.fromEntries(data));
});

// Prevent link navigation
const link = document.querySelector("a");
link.addEventListener("click", (e) => {
  e.preventDefault();
  console.log("Link click intercepted");
});

// Prevent right-click menu
document.addEventListener("contextmenu", (e) => {
  e.preventDefault();
  showCustomMenu(e.clientX, e.clientY);
});
```

---

## 🛠️ Hands-On Exercise

### Interactive Event Playground

```html
<div class="playground">
  <div class="event-log" id="log"></div>
  <div class="controls">
    <button id="clickBtn">Click Me</button>
    <input type="text" id="textInput" placeholder="Type something...">
    <div id="moveArea" style="width: 300px; height: 200px; background: #ecf0f1;">
      Move your mouse here
    </div>
  </div>
</div>

<script>
  const log = document.getElementById("log");

  function logEvent(message) {
    const entry = document.createElement("div");
    entry.className = "log-entry";
    entry.textContent = `${new Date().toLocaleTimeString()} — ${message}`;
    log.prepend(entry);

    // Keep only last 20 entries
    while (log.children.length > 20) {
      log.lastElementChild.remove();
    }
  }

  // Click events
  document.getElementById("clickBtn").addEventListener("click", (e) => {
    logEvent(`Click at (${e.clientX}, ${e.clientY})`);
  });

  // Input events
  document.getElementById("textInput").addEventListener("input", (e) => {
    logEvent(`Input: "${e.target.value}"`);
  });

  // Keyboard events
  document.addEventListener("keydown", (e) => {
    const modifiers = [
      e.ctrlKey && "Ctrl",
      e.shiftKey && "Shift",
      e.altKey && "Alt"
    ].filter(Boolean).join("+");
    logEvent(`Key: ${modifiers ? modifiers + "+" : ""}${e.key}`);
  });

  // Mouse move (throttled)
  let lastMove = 0;
  document.getElementById("moveArea").addEventListener("mousemove", (e) => {
    const now = Date.now();
    if (now - lastMove < 100) return; // throttle to 10fps
    lastMove = now;
    logEvent(`Mouse: (${e.offsetX}, ${e.offsetY})`);
  });
</script>
```

---

## 📝 Homework

1. Build a **keyboard visualizer** — show which key is pressed with a visual keyboard layout
2. Create a **custom right-click context menu** that appears at the mouse position
3. Build a **drag-to-draw** canvas using `mousedown`, `mousemove`, `mouseup`
4. Create **keyboard shortcuts** — e.g., Ctrl+S shows a save notification, Ctrl+K opens a search modal

---

## 💡 Pro Tips

> 🎯 **`e.target` vs `e.currentTarget`:** `target` is the element that was actually clicked (even if nested). `currentTarget` is the element the listener is on. Use `target.closest()` for delegation.

> ⚡ **Throttle scroll/resize:** These events fire many times per second. Use throttling or `requestAnimationFrame` to limit execution frequency.

> 🖱️ **Use `key` not `keyCode`:** `e.keyCode` is deprecated. Always use `e.key` (logical key) or `e.code` (physical key).

---

[← Day 2](day-02.md) | [Back to Week 6](README.md) | [Day 4 →](day-04.md)
