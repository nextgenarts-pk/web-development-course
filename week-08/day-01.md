# 📅 Day 1: Callbacks, Promises & Microtask Queue ⏳

> **Week 8 — Asynchronous JavaScript & APIs** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- JavaScript functions, closures, and error handling (Weeks 5, 7)
- ES6 classes and modules

---

## 🎯 Today's Goals

- ⏳ Understand synchronous vs asynchronous execution
- 🔁 Learn the event loop and task queues
- 📞 Work with callbacks and understand callback hell
- 🤝 Create and use Promises

---

## 📘 Lesson Content

### Synchronous vs Asynchronous

```javascript
// Synchronous — blocks execution
console.log("1. Start");
console.log("2. Middle"); // Waits for this to finish
console.log("3. End");    // Then runs this

// Asynchronous — non-blocking
console.log("1. Start");
setTimeout(() => console.log("2. Async"), 0);
console.log("3. End");
// Output: "1. Start", "3. End", "2. Async"
```

### The Event Loop

```
┌─────────────────────┐
│     Call Stack       │  ← Executes synchronous code
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│   Web APIs           │  ← setTimeout, fetch, DOM events
│   (Browser/Node)     │
└──────────┬──────────┘
           │
     ┌─────┴─────┐
     │            │
     ▼            ▼
┌─────────┐ ┌──────────┐
│Microtask│ │  Task     │
│ Queue   │ │  Queue    │
│(Promise)│ │(setTimeout│
│         │ │ events)   │
└────┬────┘ └─────┬────┘
     │            │
     └──────┬─────┘
            ▼
   Event Loop checks:
   1. Microtask queue (all)
   2. One task from task queue
   3. Render if needed
   4. Repeat
```

```javascript
console.log("1");

setTimeout(() => console.log("2"), 0);     // Task queue

Promise.resolve().then(() => console.log("3")); // Microtask queue

console.log("4");

// Output: 1, 4, 3, 2
// Microtasks run BEFORE tasks!
```

### Callbacks

```javascript
// Callback pattern
function loadData(url, onSuccess, onError) {
  // Simulate async operation
  setTimeout(() => {
    const success = Math.random() > 0.3;
    if (success) {
      onSuccess({ id: 1, name: "Ali" });
    } else {
      onError(new Error("Failed to load"));
    }
  }, 1000);
}

loadData(
  "/api/user",
  (data) => console.log("Success:", data),
  (err) => console.error("Error:", err)
);
```

### Callback Hell

```javascript
// ❌ Nested callbacks — hard to read and maintain
getUser(userId, (user) => {
  getOrders(user.id, (orders) => {
    getOrderDetails(orders[0].id, (details) => {
      getProductInfo(details.productId, (product) => {
        console.log(product); // 4 levels deep!
      }, handleError);
    }, handleError);
  }, handleError);
}, handleError);
```

### Promises — The Solution

```javascript
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
  const success = true;
  setTimeout(() => {
    if (success) {
      resolve({ id: 1, name: "Ali" }); // fulfilled
    } else {
      reject(new Error("Something failed")); // rejected
    }
  }, 1000);
});

// Consuming a Promise
myPromise
  .then(data => console.log("Success:", data))
  .catch(err => console.error("Error:", err))
  .finally(() => console.log("Done!"));
```

### Promise Chaining

```javascript
// ✅ Flat chain instead of nested callbacks
getUser(userId)
  .then(user => getOrders(user.id))
  .then(orders => getOrderDetails(orders[0].id))
  .then(details => getProductInfo(details.productId))
  .then(product => console.log(product))
  .catch(err => console.error("Error at any step:", err));
```

### Creating Utility Promises

```javascript
// Delay function
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

// Timeout wrapper
function withTimeout(promise, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error("Timed out")), ms)
  );
  return Promise.race([promise, timeout]);
}

// Usage
await delay(2000); // wait 2 seconds
const data = await withTimeout(fetchData(), 5000); // 5s timeout
```

### Promise States

```javascript
// A Promise is always in one of three states:
// 1. PENDING   — initial state, neither fulfilled nor rejected
// 2. FULFILLED — operation completed successfully (resolved)
// 3. REJECTED  — operation failed

// Once settled (fulfilled/rejected), a promise cannot change state

const p = new Promise((resolve) => {
  resolve("first");  // Promise is now FULFILLED
  resolve("second"); // Ignored — already settled
});
```

---

## 🛠️ Hands-On Exercise

### Promise-based Timer App

```javascript
class Timer {
  #duration;
  #onTick;

  constructor(duration, onTick) {
    this.#duration = duration;
    this.#onTick = onTick;
  }

  start() {
    return new Promise((resolve) => {
      let remaining = this.#duration;

      const tick = () => {
        this.#onTick?.(remaining);

        if (remaining <= 0) {
          resolve("Timer complete!");
          return;
        }

        remaining--;
        setTimeout(tick, 1000);
      };

      tick();
    });
  }
}

// Usage
const timer = new Timer(10, (remaining) => {
  document.getElementById("display").textContent =
    `${remaining}s remaining`;
});

timer.start().then(message => {
  document.getElementById("display").textContent = message;
  console.log("Done!");
});
```

---

## 📝 Homework

1. Convert callback-based functions to Promise-based versions
2. Create a `retry(fn, attempts)` that retries a failing promise up to N times
3. Build a **traffic light simulator** using chained promises with `delay()`
4. Create a **promise queue** that runs promises sequentially (not in parallel)

---

## 💡 Pro Tips

> 🔄 **Always return in `.then()`:** If you forget to return, the next `.then()` receives `undefined` instead of your value.

> 🛡️ **Always `.catch()`:** Unhandled promise rejections crash Node.js and show warnings in browsers. Always handle errors.

> ⏱️ **Microtasks first:** Promise callbacks (`.then`) run before `setTimeout` callbacks, even with `setTimeout(fn, 0)`. This is the microtask queue priority.

---

[← Back to Week 8](README.md) | [Day 2 →](day-02.md)
