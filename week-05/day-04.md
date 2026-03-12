# 📅 Day 4: Functions — Declarations, Expressions & Arrows 🔧

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Variables, operators, conditionals, and loops from Days 1-3

---

## 🎯 Today's Goals

- 🔧 Write function declarations, expressions, and arrow functions
- 📥 Understand parameters, arguments, and return values
- 🏗️ Use default parameters and rest parameters
- 🔄 Understand scope and closures basics

---

## 📘 Lesson Content

### Function Declaration

```javascript
// Hoisted — can be called before definition
function greet(name) {
  return `Hello, ${name}!`;
}

console.log(greet('Ahmed')); // "Hello, Ahmed!"
```

### Function Expression

```javascript
// NOT hoisted — must be defined before use
const greet = function(name) {
  return `Hello, ${name}!`;
};

console.log(greet('Sara')); // "Hello, Sara!"
```

### Arrow Functions (ES6)

```javascript
// Standard arrow function
const greet = (name) => {
  return `Hello, ${name}!`;
};

// Concise body (single expression — implicit return)
const greet = (name) => `Hello, ${name}!`;

// Single parameter — parentheses optional
const double = n => n * 2;

// No parameters — empty parentheses required
const getRandom = () => Math.random();

// Multiple statements — need braces and explicit return
const calculateTax = (price, rate) => {
  const tax = price * rate;
  return price + tax;
};
```

### Parameters & Arguments

```javascript
// Default parameters
function createUser(name, role = 'student', active = true) {
  return { name, role, active };
}

createUser('Ahmed');                 // { name: 'Ahmed', role: 'student', active: true }
createUser('Sara', 'instructor');   // { name: 'Sara', role: 'instructor', active: true }

// Rest parameters (...) — collect remaining arguments
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}

sum(1, 2, 3);       // 6
sum(10, 20, 30, 40); // 100

// Destructured parameters
function displayUser({ name, age, city }) {
  console.log(`${name}, ${age}, from ${city}`);
}

displayUser({ name: 'Ahmed', age: 25, city: 'Gilgit' });
```

### Return Values

```javascript
// Return a value
function add(a, b) {
  return a + b;
}

// Return an object (wrap in parentheses for arrow functions)
const makeUser = (name, age) => ({ name, age });

// Return early for guard clauses
function divide(a, b) {
  if (b === 0) return 'Cannot divide by zero';
  return a / b;
}

// Functions without return → undefined
function logMessage(msg) {
  console.log(msg);
  // implicitly returns undefined
}
```

### Scope

```javascript
// Global scope
const globalVar = 'I am global';

function myFunction() {
  // Function scope
  const localVar = 'I am local';
  console.log(globalVar);  // ✅ Can access global
  console.log(localVar);   // ✅ Can access local
}

// console.log(localVar); // ❌ ReferenceError — not accessible outside

// Block scope (let/const)
if (true) {
  const blockVar = 'I am block-scoped';
  let anotherBlock = 'Also block-scoped';
}
// console.log(blockVar); // ❌ Not accessible outside the if block
```

### Higher-Order Functions

```javascript
// Functions can take other functions as arguments
function doTwice(fn, value) {
  return fn(fn(value));
}

const double = n => n * 2;
console.log(doTwice(double, 5)); // 20 (5 → 10 → 20)

// Functions can return functions
function multiplier(factor) {
  return (number) => number * factor;
}

const triple = multiplier(3);
console.log(triple(10)); // 30
```

### Callback Functions

```javascript
// A function passed as an argument
function processData(data, callback) {
  const result = data.map(item => item * 2);
  callback(result);
}

processData([1, 2, 3], (result) => {
  console.log('Processed:', result); // [2, 4, 6]
});

// setTimeout — classic callback
setTimeout(() => {
  console.log('This runs after 2 seconds');
}, 2000);
```

---

## 🛠️ Hands-On Exercise

### Build Utility Functions

```javascript
// 1. Temperature converter
const celsiusToFahrenheit = (c) => (c * 9/5) + 32;
const fahrenheitToCelsius = (f) => (f - 32) * 5/9;

console.log(celsiusToFahrenheit(100)); // 212
console.log(fahrenheitToCelsius(32));  // 0

// 2. Array sum with rest params
const sumAll = (...nums) => nums.reduce((a, b) => a + b, 0);

// 3. Repeat function
const repeat = (str, times) => str.repeat(times);
```

---

## 📝 Homework

1. Create a **`calculateBMI(weight, height)`** function that returns the BMI category (Underweight, Normal, Overweight, Obese)
2. Write a **`isPalindrome(str)`** function that checks if a string reads the same backward
3. Create a **`countWords(sentence)`** function that returns the word count
4. Build a **`generatePassword(length)`** function that returns a random password
5. Write a **higher-order function** `applyDiscount(price, discountFn)` that applies different discount strategies

---

## 💡 Pro Tips

> 🎯 **Arrow Functions:** Use for short, inline functions. Use regular functions for methods and constructors (due to `this` binding differences).

> 🔧 **Pure Functions:** A pure function always returns the same output for the same input and has no side effects. Aim for pure functions when possible.

> 📦 **const for functions:** Always use `const` when assigning function expressions or arrow functions to prevent accidental reassignment.

---

[← Day 3](day-03.md) | [Back to Week 5](README.md) | [Day 5 →](day-05.md)
