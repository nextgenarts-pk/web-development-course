# 📅 Day 1: Variables, Data Types & Operators ⚡

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- HTML & CSS fundamentals (Weeks 1-4)
- Browser DevTools and VS Code setup

---

## 🎯 Today's Goals

- ⚡ Understand what JavaScript is and how it runs
- 📦 Declare variables with `let`, `const`, and `var`
- 🔢 Learn JavaScript data types
- ➕ Use arithmetic, comparison, and logical operators

---

## 📘 Lesson Content

### Adding JavaScript to HTML

```html
<!-- Method 1: Inline (avoid in production) -->
<button onclick="alert('Hello!')">Click Me</button>

<!-- Method 2: Internal script -->
<script>
  console.log('Hello from JavaScript!');
</script>

<!-- Method 3: External file (RECOMMENDED) -->
<script src="script.js" defer></script>
```

> 💡 Always use `defer` or place `<script>` before `</body>` to avoid blocking HTML parsing.

### Variables

```javascript
// const — cannot be reassigned (use by default)
const name = 'Ahmed';
const PI = 3.14159;

// let — can be reassigned (use when value changes)
let age = 20;
age = 21; // ✅ OK

// var — old way (avoid in modern JS)
var score = 100; // ❌ Function-scoped, hoisted — prefer let/const
```

**Rule:** Use `const` by default. Only use `let` when you need to reassign.

### Data Types

```javascript
// === Primitive Types ===
const myString = 'Hello World';       // String
const myNumber = 42;                   // Number
const myFloat = 3.14;                 // Number (JS has no separate float)
const myBoolean = true;               // Boolean
const myNull = null;                   // Null (intentional absence)
let myUndefined;                       // Undefined (not yet assigned)
const myBigInt = 9007199254740991n;   // BigInt (large integers)
const mySymbol = Symbol('id');         // Symbol (unique identifier)

// === Reference Types ===
const myArray = [1, 2, 3];            // Array (ordered list)
const myObject = { name: 'Ahmed' };   // Object (key-value pairs)

// Check type
typeof myString;   // "string"
typeof myNumber;   // "number"
typeof myBoolean;  // "boolean"
typeof myNull;     // "object" (historical bug!)
typeof myArray;    // "object"
```

### String Operations

```javascript
const firstName = 'Ahmed';
const lastName = 'Khan';

// Concatenation
const fullName = firstName + ' ' + lastName;

// Template literals (PREFERRED)
const greeting = `Hello, ${firstName}! You are ${age} years old.`;

// String methods
firstName.length;            // 5
firstName.toUpperCase();     // "AHMED"
firstName.toLowerCase();     // "ahmed"
firstName.includes('hm');   // true
firstName.startsWith('A');   // true
firstName.slice(0, 3);      // "Ahm"
firstName.trim();            // Removes whitespace
```

### Number Operations

```javascript
const a = 10;
const b = 3;

// Arithmetic
a + b;   // 13 (addition)
a - b;   // 7  (subtraction)
a * b;   // 30 (multiplication)
a / b;   // 3.333... (division)
a % b;   // 1  (modulo/remainder)
a ** b;  // 1000 (exponentiation)

// Increment/Decrement
let count = 0;
count++;  // 1
count--;  // 0
count += 5; // 5
count *= 2; // 10

// Useful methods
parseInt('42px');     // 42
parseFloat('3.14');   // 3.14
Number('42');         // 42
Math.round(4.6);     // 5
Math.floor(4.9);     // 4
Math.ceil(4.1);      // 5
Math.random();       // 0 to 0.999...
Math.max(1, 5, 3);   // 5
```

### Comparison Operators

```javascript
// Equality
5 == '5';     // true  (loose — converts types) ❌ AVOID
5 === '5';    // false (strict — no conversion) ✅ ALWAYS USE
5 !== '5';    // true

// Comparison
10 > 5;       // true
10 < 5;       // false
10 >= 10;     // true
10 <= 9;      // false
```

### Logical Operators

```javascript
// AND (&&) — both must be true
true && true;    // true
true && false;   // false

// OR (||) — at least one must be true
true || false;   // true
false || false;  // false

// NOT (!) — flips the value
!true;   // false
!false;  // true

// Practical use
const isLoggedIn = true;
const isAdmin = false;
if (isLoggedIn && isAdmin) {
  console.log('Admin access granted');
}
```

### Type Conversion

```javascript
// To String
String(42);         // "42"
(42).toString();    // "42"

// To Number
Number('42');       // 42
Number('hello');    // NaN
Number(true);      // 1
Number(false);     // 0
+'42';             // 42 (unary plus shortcut)

// To Boolean
Boolean(0);        // false
Boolean('');       // false
Boolean(null);     // false
Boolean(undefined);// false
Boolean('hello');  // true
Boolean(42);       // true
```

---

## 🛠️ Hands-On Exercise

Open the browser console (F12 → Console) and practice:

1. Declare variables for your name, age, and city using `const`
2. Create a greeting message using template literals
3. Perform math operations and log the results
4. Use `typeof` to check data types of different values
5. Compare values with `===` and `!==`

---

## 📝 Homework

1. Create variables for: `firstName`, `lastName`, `age`, `isStudent`, `courses` (array of 3 course names)
2. Build a **bio string** using template literals: `"Hi, I'm [name], age [age]. I study [courses]"`
3. Write expressions that evaluate to `true` and `false` using comparison and logical operators
4. Create a `script.js` file linked to an HTML page and log all results to the console

---

## 💡 Pro Tips

> ✅ **Always use `===`** instead of `==`. Strict equality prevents bugs from type coercion.

> 📦 **const by default:** Use `const` for everything unless you specifically need to reassign. It signals intent and prevents accidental mutations.

> 🔍 **Console is your friend:** Use `console.log()`, `console.table()`, and `console.dir()` extensively while learning.

---

[← Back to Week 5](README.md) | [Day 2 →](day-02.md)
