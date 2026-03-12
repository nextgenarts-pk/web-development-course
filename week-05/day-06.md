# 📅 Day 6: Objects & Project — Calculator App 🎮

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- All JavaScript fundamentals from Days 1-5
- Arrays and array methods

---

## 🎯 Today's Goals

- 📦 Master JavaScript objects — creation, access, methods
- 🔗 Understand `this` keyword in objects
- 🔄 Object destructuring and spread
- 🎮 Build a Calculator App using HTML, CSS, and JavaScript

---

## 📘 Lesson Content

### Creating Objects

```javascript
// Object literal
const person = {
  firstName: "Ali",
  lastName: "Khan",
  age: 25,
  city: "Gilgit",
  hobbies: ["coding", "hiking", "photography"]
};

// Accessing properties
console.log(person.firstName);       // "Ali" — dot notation
console.log(person["lastName"]);     // "Khan" — bracket notation

// Dynamic property access
const key = "city";
console.log(person[key]);           // "Gilgit"
```

### Adding, Modifying & Deleting Properties

```javascript
const user = { name: "Sara" };

// Add
user.email = "sara@example.com";
user["phone"] = "+92-345-1234567";

// Modify
user.name = "Sara Ahmed";

// Delete
delete user.phone;

// Check if property exists
console.log("email" in user);             // true
console.log(user.hasOwnProperty("name")); // true
```

### Object Methods & `this`

```javascript
const calculator = {
  result: 0,

  add(value) {
    this.result += value;
    return this; // enables chaining
  },

  subtract(value) {
    this.result -= value;
    return this;
  },

  multiply(value) {
    this.result *= value;
    return this;
  },

  reset() {
    this.result = 0;
    return this;
  },

  getResult() {
    return this.result;
  }
};

// Method chaining
calculator.add(10).multiply(2).subtract(5);
console.log(calculator.getResult()); // 15
```

### Object Destructuring

```javascript
const student = {
  name: "Karim Shah",
  age: 22,
  grades: { math: 90, english: 85, science: 92 }
};

// Basic destructuring
const { name, age } = student;

// Rename variables
const { name: studentName, age: studentAge } = student;

// Default values
const { name: n, country = "Pakistan" } = student;

// Nested destructuring
const { grades: { math, english } } = student;
console.log(math); // 90

// Rest pattern
const { name: nm, ...rest } = student;
// rest = { age: 22, grades: { math: 90, english: 85, science: 92 } }
```

### Spread Operator with Objects

```javascript
const defaults = { theme: "light", language: "en", fontSize: 16 };
const userPrefs = { theme: "dark", fontSize: 18 };

// Merge (later properties override)
const settings = { ...defaults, ...userPrefs };
// { theme: "dark", language: "en", fontSize: 18 }

// Shallow copy
const copy = { ...defaults };
```

### Useful Object Methods

```javascript
const product = { name: "Laptop", price: 999, stock: 15 };

// Object.keys() — array of keys
Object.keys(product); // ["name", "price", "stock"]

// Object.values() — array of values
Object.values(product); // ["Laptop", 999, 15]

// Object.entries() — array of [key, value] pairs
Object.entries(product); // [["name", "Laptop"], ["price", 999], ["stock", 15]]

// Iterate
for (const [key, value] of Object.entries(product)) {
  console.log(`${key}: ${value}`);
}

// Object.assign() — merge objects
const updated = Object.assign({}, product, { price: 899 });

// Object.freeze() — make immutable
const config = Object.freeze({ apiUrl: "https://api.example.com" });
config.apiUrl = "hack"; // Silently fails (throws in strict mode)
```

### Optional Chaining & Nullish Coalescing

```javascript
const user = {
  name: "Ali",
  address: {
    city: "Gilgit"
  }
};

// Optional chaining — safe property access
console.log(user.address?.city);    // "Gilgit"
console.log(user.address?.zip);     // undefined (no error)
console.log(user.phone?.mobile);    // undefined (no error)

// Nullish coalescing — default for null/undefined
const port = null;
const serverPort = port ?? 3000;    // 3000
```

---

## 🛠️ Hands-On Project: Calculator App

### HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Calculator</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="calculator">
    <div class="display">
      <div class="previous-operand" id="previousOperand"></div>
      <div class="current-operand" id="currentOperand">0</div>
    </div>
    <div class="buttons">
      <button class="btn btn--span-two" data-action="clear">AC</button>
      <button class="btn" data-action="delete">DEL</button>
      <button class="btn btn--operator" data-action="operator" data-value="÷">÷</button>
      <button class="btn" data-value="7">7</button>
      <button class="btn" data-value="8">8</button>
      <button class="btn" data-value="9">9</button>
      <button class="btn btn--operator" data-action="operator" data-value="×">×</button>
      <button class="btn" data-value="4">4</button>
      <button class="btn" data-value="5">5</button>
      <button class="btn" data-value="6">6</button>
      <button class="btn btn--operator" data-action="operator" data-value="-">-</button>
      <button class="btn" data-value="1">1</button>
      <button class="btn" data-value="2">2</button>
      <button class="btn" data-value="3">3</button>
      <button class="btn btn--operator" data-action="operator" data-value="+">+</button>
      <button class="btn btn--span-two" data-value="0">0</button>
      <button class="btn" data-value=".">.</button>
      <button class="btn btn--equals" data-action="equals">=</button>
    </div>
  </div>

  <script src="script.js"></script>
</body>
</html>
```

### JavaScript Logic

```javascript
// Calculator state object
const calc = {
  currentOperand: "0",
  previousOperand: "",
  operator: null,

  appendNumber(number) {
    if (number === "." && this.currentOperand.includes(".")) return;
    if (this.currentOperand === "0" && number !== ".") {
      this.currentOperand = number;
    } else {
      this.currentOperand += number;
    }
  },

  chooseOperator(op) {
    if (this.currentOperand === "") return;
    if (this.previousOperand !== "") {
      this.compute();
    }
    this.operator = op;
    this.previousOperand = this.currentOperand + " " + op;
    this.currentOperand = "0";
  },

  compute() {
    const prev = parseFloat(this.previousOperand);
    const current = parseFloat(this.currentOperand);
    if (isNaN(prev) || isNaN(current)) return;

    const operations = {
      "+": (a, b) => a + b,
      "-": (a, b) => a - b,
      "×": (a, b) => a * b,
      "÷": (a, b) => (b !== 0 ? a / b : "Error")
    };

    const result = operations[this.operator]?.(prev, current);
    this.currentOperand = result !== undefined ? String(result) : "Error";
    this.operator = null;
    this.previousOperand = "";
  },

  clear() {
    this.currentOperand = "0";
    this.previousOperand = "";
    this.operator = null;
  },

  deleteLast() {
    this.currentOperand = this.currentOperand.slice(0, -1) || "0";
  }
};

// DOM elements
const currentDisplay = document.getElementById("currentOperand");
const previousDisplay = document.getElementById("previousOperand");

function updateDisplay() {
  currentDisplay.textContent = calc.currentOperand;
  previousDisplay.textContent = calc.previousOperand;
}

// Event delegation for all buttons
document.querySelector(".buttons").addEventListener("click", (e) => {
  const button = e.target.closest(".btn");
  if (!button) return;

  const action = button.dataset.action;
  const value = button.dataset.value;

  if (action === "clear") calc.clear();
  else if (action === "delete") calc.deleteLast();
  else if (action === "equals") calc.compute();
  else if (action === "operator") calc.chooseOperator(value);
  else if (value) calc.appendNumber(value);

  updateDisplay();
});
```

---

## 📝 Homework

1. **Style the calculator** with CSS (dark theme, button hover effects, grid layout)
2. **Add keyboard support** — listen for `keydown` events for numbers, operators, Enter, Backspace
3. **Add a history feature** — store past calculations in an array and display them
4. **Percentage button** — add a `%` button that divides the current number by 100

---

## 💡 Pro Tips

> 🔑 **`this` in methods:** Always use regular functions (not arrow functions) for object methods, because arrow functions don't get their own `this`.

> 🧊 **Immutability:** Use `Object.freeze()` for config objects that shouldn't change. Use spread `{...obj}` to create copies before modifying.

> 🔗 **Optional Chaining:** Use `?.` liberally for accessing nested data from APIs — it prevents crashes from `undefined` properties.

---

[← Day 5](day-05.md) | [Back to Week 5](README.md)
