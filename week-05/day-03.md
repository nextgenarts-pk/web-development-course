# 📅 Day 3: Loops & Iteration 🔁

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Variables, data types, operators, and conditionals from Days 1-2

---

## 🎯 Today's Goals

- 🔁 Master `for`, `while`, and `do...while` loops
- 🔄 Use `for...of` and `for...in` for collections
- 🛑 Control loops with `break` and `continue`
- 🎯 Solve looping problems and patterns

---

## 📘 Lesson Content

### for Loop

```javascript
// Basic for loop
for (let i = 0; i < 5; i++) {
  console.log(`Iteration ${i}`); // 0, 1, 2, 3, 4
}

// Counting backwards
for (let i = 10; i >= 0; i--) {
  console.log(i); // 10, 9, 8, ... 0
}

// Skip by 2
for (let i = 0; i <= 20; i += 2) {
  console.log(i); // 0, 2, 4, 6, ... 20
}

// Loop through array
const fruits = ['Apple', 'Banana', 'Cherry'];
for (let i = 0; i < fruits.length; i++) {
  console.log(`${i + 1}. ${fruits[i]}`);
}
```

### while Loop

```javascript
// Execute while condition is true
let count = 0;
while (count < 5) {
  console.log(`Count: ${count}`);
  count++;
}

// Useful for unknown iterations
let input = '';
// Imagine getting actual user input — loop until valid
// while (input !== 'quit') { ... }
```

### do...while Loop

```javascript
// Executes at least once, then checks condition
let num = 0;
do {
  console.log(`Number: ${num}`);
  num++;
} while (num < 5);
```

### for...of (Arrays, Strings)

```javascript
// Best for iterating over arrays
const colors = ['red', 'green', 'blue'];
for (const color of colors) {
  console.log(color);
}

// Works with strings too
const word = 'Hello';
for (const char of word) {
  console.log(char); // H, e, l, l, o
}
```

### for...in (Objects)

```javascript
// Iterate over object keys
const student = {
  name: 'Sara',
  age: 22,
  city: 'Gilgit'
};

for (const key in student) {
  console.log(`${key}: ${student[key]}`);
}
// name: Sara
// age: 22
// city: Gilgit
```

### break & continue

```javascript
// break — exit the loop entirely
for (let i = 0; i < 10; i++) {
  if (i === 5) break;
  console.log(i); // 0, 1, 2, 3, 4
}

// continue — skip current iteration
for (let i = 0; i < 10; i++) {
  if (i % 2 === 0) continue; // Skip even numbers
  console.log(i); // 1, 3, 5, 7, 9
}
```

### Nested Loops

```javascript
// Multiplication table
for (let i = 1; i <= 5; i++) {
  let row = '';
  for (let j = 1; j <= 5; j++) {
    row += `${i * j}\t`;
  }
  console.log(row);
}

// Star pattern
for (let i = 1; i <= 5; i++) {
  console.log('*'.repeat(i));
}
// *
// **
// ***
// ****
// *****
```

### Common Loop Patterns

```javascript
// Sum of numbers
let sum = 0;
for (let i = 1; i <= 100; i++) {
  sum += i;
}
console.log(`Sum: ${sum}`); // 5050

// Find max value
const numbers = [34, 67, 23, 89, 12, 56];
let max = numbers[0];
for (const num of numbers) {
  if (num > max) max = num;
}
console.log(`Max: ${max}`); // 89

// Count occurrences
const text = 'hello world';
let vowelCount = 0;
for (const char of text) {
  if ('aeiou'.includes(char)) vowelCount++;
}
console.log(`Vowels: ${vowelCount}`); // 3
```

---

## 🛠️ Hands-On Exercise

### FizzBuzz Challenge

```javascript
for (let i = 1; i <= 100; i++) {
  if (i % 3 === 0 && i % 5 === 0) {
    console.log('FizzBuzz');
  } else if (i % 3 === 0) {
    console.log('Fizz');
  } else if (i % 5 === 0) {
    console.log('Buzz');
  } else {
    console.log(i);
  }
}
```

---

## 📝 Homework

1. Print the **multiplication table** for any number (1-12)
2. Write a loop that prints the **Fibonacci sequence** up to 100: `0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89`
3. Create a **pyramid pattern** of stars that goes up and then down
4. Loop through an array of names and build an **HTML unordered list** string
5. Solve **FizzBuzz** (above) and add "FizzBuzzBam" for multiples of 3, 5, AND 7

---

## 💡 Pro Tips

> 🔄 **for...of vs for...in:** Use `for...of` for arrays (values), `for...in` for objects (keys). Never use `for...in` on arrays — it iterates over keys (indices as strings), not values.

> ⚡ **Performance:** For simple array iteration, `for...of` is cleaner but a traditional `for` loop is faster for performance-critical code.

> 🚫 **Avoid infinite loops:** Always ensure your condition will eventually become false. If using `while`, make sure the control variable changes inside the loop!

---

[← Day 2](day-02.md) | [Back to Week 5](README.md) | [Day 4 →](day-04.md)
