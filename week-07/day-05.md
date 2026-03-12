# 📅 Day 5: Error Handling, Debugging & Defensive Programming 🛡️

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- JavaScript fundamentals and classes (Week 5, Days 1-2)
- Understanding of `try/catch` basics

---

## 🎯 Today's Goals

- 🛡️ Master `try/catch/finally` error handling
- 🏗️ Create custom error classes
- 🔍 Debug effectively with browser DevTools
- 🧪 Write defensive code that handles edge cases

---

## 📘 Lesson Content

### try/catch/finally

```javascript
try {
  // Code that might throw an error
  const data = JSON.parse(userInput);
  processData(data);
} catch (error) {
  // Handle the error
  console.error("Failed to parse:", error.message);
} finally {
  // Always runs (cleanup)
  hideLoadingSpinner();
}
```

### Error Types

```javascript
// Built-in error types
new Error("Generic error");
new TypeError("Expected a string");
new RangeError("Value out of range");
new ReferenceError("Variable not defined");
new SyntaxError("Invalid syntax");
new URIError("Invalid URI");

// Accessing error info
try {
  null.property;
} catch (e) {
  console.log(e.name);    // "TypeError"
  console.log(e.message); // "Cannot read properties of null"
  console.log(e.stack);   // full stack trace
}
```

### Custom Error Classes

```javascript
class AppError extends Error {
  constructor(message, code, details = {}) {
    super(message);
    this.name = "AppError";
    this.code = code;
    this.details = details;
  }
}

class ValidationError extends AppError {
  constructor(field, message) {
    super(message, "VALIDATION_ERROR", { field });
    this.name = "ValidationError";
  }
}

class NotFoundError extends AppError {
  constructor(resource, id) {
    super(`${resource} with id ${id} not found`, "NOT_FOUND", { resource, id });
    this.name = "NotFoundError";
  }
}

// Usage
function findUser(id) {
  const user = users.find(u => u.id === id);
  if (!user) throw new NotFoundError("User", id);
  return user;
}

try {
  const user = findUser(999);
} catch (e) {
  if (e instanceof NotFoundError) {
    console.log(`${e.details.resource} not found`);
  } else if (e instanceof ValidationError) {
    console.log(`Invalid ${e.details.field}: ${e.message}`);
  } else {
    throw e; // re-throw unexpected errors
  }
}
```

### Throwing Errors Strategically

```javascript
// Validate function arguments
function divide(a, b) {
  if (typeof a !== "number" || typeof b !== "number") {
    throw new TypeError("Both arguments must be numbers");
  }
  if (b === 0) {
    throw new RangeError("Cannot divide by zero");
  }
  return a / b;
}

// Validate at system boundaries
function processUserInput(input) {
  if (!input || typeof input !== "string") {
    throw new ValidationError("input", "Input must be a non-empty string");
  }
  if (input.length > 1000) {
    throw new ValidationError("input", "Input exceeds maximum length of 1000");
  }
  return input.trim();
}
```

### Error Handling Patterns

```javascript
// Pattern 1: Result objects (no exceptions)
function safeParse(jsonString) {
  try {
    return { success: true, data: JSON.parse(jsonString) };
  } catch {
    return { success: false, error: "Invalid JSON" };
  }
}

const result = safeParse(input);
if (result.success) {
  console.log(result.data);
} else {
  console.error(result.error);
}

// Pattern 2: Default values with optional chaining
const username = response?.data?.user?.name ?? "Anonymous";
const port = config?.server?.port ?? 3000;

// Pattern 3: Guard clauses (early returns)
function processOrder(order) {
  if (!order) return null;
  if (!order.items?.length) return { error: "No items" };
  if (order.status !== "pending") return { error: "Order not pending" };

  // Main logic (only reached if all guards pass)
  return calculateTotal(order);
}
```

### Debugging with DevTools

```javascript
// Console methods
console.log("Basic log");
console.warn("Warning message");
console.error("Error message");
console.table([{ name: "Ali", age: 25 }, { name: "Sara", age: 30 }]);
console.group("User Operations");
console.log("Fetching user...");
console.log("Processing...");
console.groupEnd();

// Timing
console.time("operation");
heavyOperation();
console.timeEnd("operation"); // "operation: 125ms"

// Conditional logging
console.assert(value > 0, "Value must be positive!", value);

// Debugger statement (opens DevTools debugger)
function complexFunction(data) {
  debugger; // Execution pauses here when DevTools is open
  return data.map(transform);
}
```

### Global Error Handling

```javascript
// Catch unhandled errors
window.addEventListener("error", (event) => {
  console.error("Unhandled error:", event.error);
  showUserFriendlyError("Something went wrong. Please try again.");
});

// Catch unhandled promise rejections
window.addEventListener("unhandledrejection", (event) => {
  console.error("Unhandled promise rejection:", event.reason);
  event.preventDefault(); // prevent default browser logging
});
```

---

## 🛠️ Hands-On Exercise

### Form Validator with Error Handling

```javascript
class FormValidator {
  #rules = new Map();
  #errors = new Map();

  addRule(field, validator, message) {
    if (!this.#rules.has(field)) {
      this.#rules.set(field, []);
    }
    this.#rules.get(field).push({ validator, message });
    return this;
  }

  validate(data) {
    this.#errors.clear();

    for (const [field, rules] of this.#rules) {
      for (const { validator, message } of rules) {
        try {
          const value = data[field];
          if (!validator(value)) {
            if (!this.#errors.has(field)) {
              this.#errors.set(field, []);
            }
            this.#errors.get(field).push(message);
          }
        } catch (e) {
          this.#errors.set(field, [`Validation error: ${e.message}`]);
        }
      }
    }

    return {
      isValid: this.#errors.size === 0,
      errors: Object.fromEntries(this.#errors)
    };
  }
}

// Usage
const validator = new FormValidator()
  .addRule("name", v => v && v.trim().length >= 2, "Name must be at least 2 chars")
  .addRule("email", v => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v), "Invalid email")
  .addRule("password", v => v && v.length >= 8, "Password must be 8+ chars")
  .addRule("password", v => /[A-Z]/.test(v), "Must contain uppercase letter")
  .addRule("password", v => /[0-9]/.test(v), "Must contain a number");

const result = validator.validate({
  name: "A",
  email: "invalid",
  password: "weak"
});

console.log(result);
// { isValid: false, errors: { name: [...], email: [...], password: [...] } }
```

---

## 📝 Homework

1. Create a `retry(fn, attempts, delay)` function that retries a failing function up to N times
2. Build an **error boundary** system that catches errors in different app sections and displays user-friendly messages
3. Write unit tests for the `FormValidator` — test valid input, invalid input, edge cases
4. Add **structured logging** — create a Logger class with levels (debug, info, warn, error) and timestamp

---

## 💡 Pro Tips

> 🛡️ **Validate at boundaries:** Check inputs at system boundaries (user input, API responses, config files). Trust internal code — don't validate everywhere.

> 🔄 **Re-throw unknown errors:** In catch blocks, only handle errors you expect. Re-throw everything else with `throw e` so they're not silently swallowed.

> 🧪 **Use `console.table()`:** For arrays of objects, `console.table()` renders a beautiful table in DevTools — way more readable than `console.log`.

---

[← Day 4](day-04.md) | [Back to Week 7](README.md) | [Day 6 →](day-06.md)
