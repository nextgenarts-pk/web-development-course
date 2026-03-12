# 📅 Day 4: Map, Set, WeakMap & Modern Data Structures 🗺️

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Objects and arrays (Week 5)
- Iterators and Symbol.iterator (Day 3)

---

## 🎯 Today's Goals

- 🗺️ Master `Map` — key-value pairs with any key type
- 📦 Master `Set` — unique value collections
- 🔒 Understand `WeakMap` and `WeakSet` for memory management
- 🆚 Know when to use Map vs Object, Set vs Array

---

## 📘 Lesson Content

### Map — Key-Value Pairs

Unlike objects, Maps can use **any value** as a key (objects, functions, numbers):

```javascript
const userRoles = new Map();

// Set entries
userRoles.set("ali@example.com", "admin");
userRoles.set("sara@example.com", "editor");
userRoles.set("karim@example.com", "viewer");

// Get values
userRoles.get("ali@example.com"); // "admin"
userRoles.has("sara@example.com"); // true
userRoles.size; // 3

// Delete
userRoles.delete("karim@example.com");

// Clear all
// userRoles.clear();

// Initialize with entries
const config = new Map([
  ["theme", "dark"],
  ["language", "en"],
  ["fontSize", 16]
]);
```

### Map with Object Keys

```javascript
const metadata = new Map();

const user1 = { id: 1, name: "Ali" };
const user2 = { id: 2, name: "Sara" };

// Objects as keys!
metadata.set(user1, { lastLogin: new Date(), visits: 42 });
metadata.set(user2, { lastLogin: new Date(), visits: 17 });

console.log(metadata.get(user1)); // { lastLogin: ..., visits: 42 }
```

### Iterating Maps

```javascript
const scores = new Map([
  ["Ali", 95],
  ["Sara", 88],
  ["Karim", 72]
]);

// for...of (entries)
for (const [name, score] of scores) {
  console.log(`${name}: ${score}`);
}

// forEach
scores.forEach((score, name) => {
  console.log(`${name}: ${score}`);
});

// keys, values, entries
console.log([...scores.keys()]);    // ["Ali", "Sara", "Karim"]
console.log([...scores.values()]);  // [95, 88, 72]
console.log([...scores.entries()]); // [["Ali", 95], ...]

// Convert to/from object
const obj = Object.fromEntries(scores);
const map = new Map(Object.entries(obj));
```

### Map vs Object — When to Use

| Feature | Map | Object |
|---------|-----|--------|
| Key types | Any value | Strings & Symbols only |
| Order | Insertion order guaranteed | Mostly ordered (quirks with numbers) |
| Size | `.size` property | `Object.keys().length` |
| Iteration | Directly iterable | Need `Object.entries()` |
| Performance | Better for frequent add/remove | Better for static data |
| Serialization | No native JSON support | Native JSON support |

### Set — Unique Values

```javascript
const uniqueNumbers = new Set([1, 2, 3, 2, 1, 4, 3]);
console.log(uniqueNumbers); // Set(4) {1, 2, 3, 4}

// Add values
uniqueNumbers.add(5);
uniqueNumbers.add(3); // ignored — already exists

// Check membership
uniqueNumbers.has(3); // true

// Remove
uniqueNumbers.delete(1);

// Size
uniqueNumbers.size; // 4

// Iterate
for (const num of uniqueNumbers) {
  console.log(num);
}
```

### Set — Practical Uses

```javascript
// Remove duplicates from array
const arr = [1, 2, 3, 2, 4, 1, 5, 3];
const unique = [...new Set(arr)]; // [1, 2, 3, 4, 5]

// Set operations
function union(setA, setB) {
  return new Set([...setA, ...setB]);
}

function intersection(setA, setB) {
  return new Set([...setA].filter(x => setB.has(x)));
}

function difference(setA, setB) {
  return new Set([...setA].filter(x => !setB.has(x)));
}

const frontend = new Set(["HTML", "CSS", "JavaScript", "React"]);
const backend = new Set(["Node.js", "Express", "JavaScript", "MongoDB"]);

console.log(union(frontend, backend));
// Set {"HTML", "CSS", "JavaScript", "React", "Node.js", "Express", "MongoDB"}

console.log(intersection(frontend, backend));
// Set {"JavaScript"}

console.log(difference(frontend, backend));
// Set {"HTML", "CSS", "React"}
```

### Tracking Unique Items

```javascript
// Track visited pages
const visitedPages = new Set();

function trackPageVisit(page) {
  const isNewVisit = !visitedPages.has(page);
  visitedPages.add(page);
  return isNewVisit;
}

trackPageVisit("/home");     // true — first visit
trackPageVisit("/about");    // true
trackPageVisit("/home");     // false — already visited
```

### WeakMap & WeakSet

Keys must be objects. They don't prevent garbage collection — perfect for caching and metadata:

```javascript
// WeakMap — private data store for classes
const privateData = new WeakMap();

class Person {
  constructor(name, age) {
    privateData.set(this, { name, age });
  }

  getName() {
    return privateData.get(this).name;
  }

  getAge() {
    return privateData.get(this).age;
  }
}

// When Person instance is garbage collected,
// its WeakMap entry is automatically removed!

// WeakMap for caching
const cache = new WeakMap();

function expensiveCompute(obj) {
  if (cache.has(obj)) return cache.get(obj);

  const result = /* heavy computation */ obj.value * 42;
  cache.set(obj, result);
  return result;
}
```

---

## 🛠️ Hands-On Exercise

### Analytics Tracker

```javascript
class AnalyticsTracker {
  #events = new Map();      // event name → count
  #uniqueUsers = new Set(); // unique user IDs
  #userEvents = new Map();  // user → Set of events

  trackEvent(userId, eventName) {
    // Track unique users
    this.#uniqueUsers.add(userId);

    // Count events
    const count = this.#events.get(eventName) || 0;
    this.#events.set(eventName, count + 1);

    // Track events per user
    if (!this.#userEvents.has(userId)) {
      this.#userEvents.set(userId, new Set());
    }
    this.#userEvents.get(userId).add(eventName);
  }

  getEventCount(eventName) {
    return this.#events.get(eventName) || 0;
  }

  get uniqueUserCount() {
    return this.#uniqueUsers.size;
  }

  getUserEvents(userId) {
    return [...(this.#userEvents.get(userId) || [])];
  }

  getTopEvents(n = 5) {
    return [...this.#events.entries()]
      .sort(([, a], [, b]) => b - a)
      .slice(0, n)
      .map(([name, count]) => ({ name, count }));
  }

  get report() {
    return {
      totalEvents: [...this.#events.values()].reduce((sum, n) => sum + n, 0),
      uniqueEvents: this.#events.size,
      uniqueUsers: this.#uniqueUsers.size,
      topEvents: this.getTopEvents()
    };
  }
}

// Usage
const analytics = new AnalyticsTracker();
analytics.trackEvent("user1", "page_view");
analytics.trackEvent("user1", "btn_click");
analytics.trackEvent("user2", "page_view");
analytics.trackEvent("user2", "signup");
analytics.trackEvent("user3", "page_view");

console.log(analytics.report);
```

---

## 📝 Homework

1. Build a **word frequency counter** using Map — input text, output sorted word counts
2. Create a **tag manager** using Set — add, remove, toggle tags on items; find items with common tags
3. Implement a **LRU Cache** (Least Recently Used) using Map — oldest entries are evicted when capacity is reached
4. Build a **vocabulary quiz** that tracks which words have been tested using Set (never repeat a word)

---

## 💡 Pro Tips

> 🗺️ **Map for dynamic keys:** If your keys are dynamic, computed at runtime, or non-string values, always use Map over Object.

> 📦 **Set for membership testing:** `set.has(x)` is O(1) — much faster than `array.includes(x)` which is O(n). Use Set when you frequently check "does this collection contain X?"

> 🧹 **WeakMap for memory safety:** Use WeakMap to associate data with DOM elements or class instances. When the element/instance is garbage collected, the associated data is automatically cleaned up.

---

[← Day 3](day-03.md) | [Back to Week 7](README.md) | [Day 5 →](day-05.md)
