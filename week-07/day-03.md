# 📅 Day 3: Advanced Destructuring, Spread/Rest & Iterators 🔄

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Basic destructuring and spread (Week 5)
- Classes and OOP (Day 2)

---

## 🎯 Today's Goals

- 🔄 Master advanced destructuring patterns
- 📐 Use rest parameters and spread in complex scenarios
- 🔁 Understand iterators and the iterable protocol
- ⚡ Use generators for lazy evaluation

---

## 📘 Lesson Content

### Advanced Object Destructuring

```javascript
// Nested destructuring
const response = {
  data: {
    user: {
      name: "Ali Khan",
      address: {
        city: "Gilgit",
        country: "Pakistan"
      }
    }
  },
  status: 200
};

const {
  data: {
    user: {
      name,
      address: { city, country }
    }
  },
  status
} = response;

console.log(name, city); // "Ali Khan" "Gilgit"

// Destructuring with defaults and rename
const { data: { user: { age = 25 } } } = response;

// Destructuring function parameters
function createUser({ name, email, role = "student", active = true } = {}) {
  return { name, email, role, active, createdAt: new Date() };
}

createUser({ name: "Sara", email: "sara@example.com" });
createUser(); // works with defaults thanks to = {}
```

### Advanced Array Destructuring

```javascript
// Swap multiple variables
let [a, b, c] = [1, 2, 3];
[a, b, c] = [c, a, b]; // rotate: a=3, b=1, c=2

// Destructure from functions
function getMinMax(arr) {
  return [Math.min(...arr), Math.max(...arr)];
}
const [min, max] = getMinMax([5, 3, 9, 1, 7]);

// Nested array destructuring
const matrix = [[1, 2], [3, 4], [5, 6]];
const [[a1, a2], [b1, b2]] = matrix;
// a1=1, a2=2, b1=3, b2=4

// Destructuring with iterables
const [first, ...rest] = "Hello"; // first="H", rest=["e","l","l","o"]
const [x, , z] = new Set([10, 20, 30]); // x=10, z=30
```

### Advanced Spread & Rest

```javascript
// Deep merge objects (shallow!)
const defaults = {
  theme: "light",
  sidebar: { width: 250, visible: true },
  notifications: { email: true, sms: false }
};

const userSettings = {
  theme: "dark",
  sidebar: { width: 300 }
};

// ⚠️ Shallow merge — sidebar.visible is LOST
const merged = { ...defaults, ...userSettings };

// ✅ Deep merge (manual for nested)
const deepMerged = {
  ...defaults,
  ...userSettings,
  sidebar: { ...defaults.sidebar, ...userSettings.sidebar },
  notifications: { ...defaults.notifications }
};

// Rest in function params — collect remaining args
function log(level, ...messages) {
  const prefix = `[${level.toUpperCase()}]`;
  console.log(prefix, ...messages);
}
log("info", "Server started", "on port 3000");

// Spread for function arguments
const coords = [51.5, -0.12, 100];
const point = new Point(...coords);
```

### Computed Property Names

```javascript
const field = "email";
const value = "ali@example.com";

const obj = {
  [field]: value,
  [`${field}Verified`]: false,
  [`get${field.charAt(0).toUpperCase() + field.slice(1)}`]() {
    return this[field];
  }
};
// { email: "ali@example.com", emailVerified: false, getEmail: [Function] }

// Dynamic object building
function buildQuery(filters) {
  return Object.fromEntries(
    Object.entries(filters).filter(([, value]) => value != null)
  );
}
buildQuery({ name: "Ali", age: null, city: "Gilgit" });
// { name: "Ali", city: "Gilgit" }
```

### Iterators & The Iterable Protocol

```javascript
// Custom iterable — implements Symbol.iterator
class Range {
  constructor(start, end) {
    this.start = start;
    this.end = end;
  }

  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;

    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        }
        return { done: true };
      }
    };
  }
}

// Works with for...of, spread, destructuring
const range = new Range(1, 5);
for (const n of range) console.log(n); // 1, 2, 3, 4, 5
console.log([...range]);               // [1, 2, 3, 4, 5]
const [first, second] = range;         // 1, 2
```

### Generators

```javascript
// Generator function — uses function* and yield
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

// Lazy — values computed on demand
const fib = fibonacci();
console.log(fib.next().value); // 0
console.log(fib.next().value); // 1
console.log(fib.next().value); // 1
console.log(fib.next().value); // 2

// Take first N values
function* take(iterable, n) {
  let count = 0;
  for (const item of iterable) {
    if (count >= n) return;
    yield item;
    count++;
  }
}

console.log([...take(fibonacci(), 10)]);
// [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

// Generator for unique IDs
function* idGenerator(prefix = "id") {
  let id = 1;
  while (true) {
    yield `${prefix}_${id++}`;
  }
}

const nextId = idGenerator("user");
nextId.next().value; // "user_1"
nextId.next().value; // "user_2"
```

---

## 🛠️ Hands-On Exercise

### Configuration Manager

```javascript
class ConfigManager {
  #config = {};

  constructor(defaults = {}) {
    this.#config = this.#deepClone(defaults);
  }

  #deepClone(obj) {
    return JSON.parse(JSON.stringify(obj));
  }

  get(path, defaultValue = undefined) {
    return path.split(".").reduce((obj, key) => obj?.[key], this.#config) ?? defaultValue;
  }

  set(path, value) {
    const keys = path.split(".");
    const lastKey = keys.pop();
    const target = keys.reduce((obj, key) => {
      if (!(key in obj)) obj[key] = {};
      return obj[key];
    }, this.#config);
    target[lastKey] = value;
  }

  merge(overrides) {
    this.#deepMerge(this.#config, overrides);
  }

  #deepMerge(target, source) {
    for (const [key, value] of Object.entries(source)) {
      if (value && typeof value === "object" && !Array.isArray(value)) {
        target[key] = target[key] || {};
        this.#deepMerge(target[key], value);
      } else {
        target[key] = value;
      }
    }
  }

  // Make config iterable
  *[Symbol.iterator]() {
    yield* Object.entries(this.#config);
  }
}

const config = new ConfigManager({
  app: { name: "MyApp", version: "1.0" },
  db: { host: "localhost", port: 5432 }
});

config.set("app.debug", true);
console.log(config.get("app.name"));    // "MyApp"
console.log(config.get("app.debug"));   // true
console.log(config.get("cache.ttl", 3600)); // 3600 (default)

for (const [key, value] of config) {
  console.log(key, value);
}
```

---

## 📝 Homework

1. Create a `DeepMerge` utility function that handles arrays, objects, and primitives
2. Build a `LinkedList` class that implements the iterable protocol
3. Create a generator `paginate(items, pageSize)` that yields one page at a time
4. Write a `pick(obj, keys)` and `omit(obj, keys)` utility using destructuring

---

## 💡 Pro Tips

> 🔄 **Spread is shallow:** `{...obj}` and `[...arr]` only copy the first level. Nested objects/arrays are still references. Use `structuredClone()` for deep copies.

> ⚡ **Generators for performance:** Use generators when working with large datasets — they compute values lazily (on demand) instead of creating entire arrays in memory.

> 🧩 **Destructuring in loops:** `for (const { name, age } of users)` is cleaner than `for (const user of users) { const name = user.name; ... }`

---

[← Day 2](day-02.md) | [Back to Week 7](README.md) | [Day 4 →](day-04.md)
