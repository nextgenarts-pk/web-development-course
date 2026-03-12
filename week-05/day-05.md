# 📅 Day 5: Arrays & Array Methods 📦

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Variables, data types, and operators (Day 1)
- Control flow and loops (Days 2-3)
- Functions — declarations, expressions, arrows (Day 4)

---

## 🎯 Today's Goals

- 📦 Create and manipulate arrays
- 🔄 Master essential array methods
- 🔗 Chain array methods together
- 🧩 Use destructuring with arrays
- 🔍 Search, sort, and transform arrays

---

## 📘 Lesson Content

### Creating Arrays

```javascript
// Array literal (preferred)
const fruits = ["apple", "banana", "cherry"];

// Array constructor
const numbers = new Array(1, 2, 3, 4, 5);

// Empty array
const empty = [];

// Mixed types (avoid in practice)
const mixed = [1, "hello", true, null, { name: "Ali" }];

// Array.from() — create from iterable
const chars = Array.from("hello"); // ["h", "e", "l", "l", "o"]
const range = Array.from({ length: 5 }, (_, i) => i + 1); // [1, 2, 3, 4, 5]
```

### Accessing & Modifying Elements

```javascript
const colors = ["red", "green", "blue"];

// Access by index (0-based)
console.log(colors[0]);       // "red"
console.log(colors.length);   // 3
console.log(colors.at(-1));   // "blue" (last element)

// Modify
colors[1] = "yellow";         // ["red", "yellow", "blue"]
```

### Adding & Removing Elements

```javascript
const arr = [1, 2, 3];

// End operations
arr.push(4);        // [1, 2, 3, 4]  — add to end
arr.pop();          // [1, 2, 3]     — remove from end

// Start operations
arr.unshift(0);     // [0, 1, 2, 3]  — add to start
arr.shift();        // [1, 2, 3]     — remove from start

// splice(start, deleteCount, ...items)
arr.splice(1, 1);           // [1, 3]       — remove 1 at index 1
arr.splice(1, 0, 2);        // [1, 2, 3]    — insert 2 at index 1
arr.splice(1, 1, 20, 30);   // [1, 20, 30, 3] — replace + insert
```

### Searching Arrays

```javascript
const fruits = ["apple", "banana", "cherry", "banana"];

fruits.indexOf("banana");     // 1 (first occurrence)
fruits.lastIndexOf("banana"); // 3 (last occurrence)
fruits.includes("cherry");    // true

// find() — returns first matching element
const users = [
  { name: "Ali", age: 25 },
  { name: "Sara", age: 30 },
  { name: "Karim", age: 20 }
];

const found = users.find(user => user.age > 22);
// { name: "Ali", age: 25 }

const index = users.findIndex(user => user.name === "Sara");
// 1
```

### Transformation Methods

```javascript
const numbers = [1, 2, 3, 4, 5];

// map() — transform each element
const doubled = numbers.map(n => n * 2);
// [2, 4, 6, 8, 10]

// filter() — keep elements that pass a test
const evens = numbers.filter(n => n % 2 === 0);
// [2, 4]

// reduce() — accumulate to single value
const sum = numbers.reduce((acc, n) => acc + n, 0);
// 15

// sort() — sorts in place
const names = ["Charlie", "Alice", "Bob"];
names.sort(); // ["Alice", "Bob", "Charlie"]

// Numeric sort (default sort is alphabetical!)
const nums = [10, 5, 20, 1];
nums.sort((a, b) => a - b); // [1, 5, 10, 20]

// reverse()
nums.reverse(); // [20, 10, 5, 1]
```

### Iterating Over Arrays

```javascript
const fruits = ["apple", "banana", "cherry"];

// forEach — no return value
fruits.forEach((fruit, index) => {
  console.log(`${index}: ${fruit}`);
});

// for...of — cleaner syntax
for (const fruit of fruits) {
  console.log(fruit);
}

// entries() — index + value
for (const [index, fruit] of fruits.entries()) {
  console.log(`${index}: ${fruit}`);
}
```

### Chaining Methods

```javascript
const students = [
  { name: "Ali", grade: 85 },
  { name: "Sara", grade: 45 },
  { name: "Karim", grade: 92 },
  { name: "Fatima", grade: 67 },
  { name: "Basit", grade: 38 }
];

// Get names of passing students, sorted by grade
const passingStudents = students
  .filter(s => s.grade >= 50)        // filter passing
  .sort((a, b) => b.grade - a.grade) // sort high to low
  .map(s => `${s.name}: ${s.grade}%`); // format output

console.log(passingStudents);
// ["Karim: 92%", "Ali: 85%", "Fatima: 67%"]
```

### Array Destructuring

```javascript
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first = 1, second = 2, rest = [3, 4, 5]

// Swap variables
let a = 1, b = 2;
[a, b] = [b, a]; // a = 2, b = 1

// Skip elements
const [, , third] = [10, 20, 30]; // third = 30

// Default values
const [x = 0, y = 0] = [5]; // x = 5, y = 0
```

### Spread Operator with Arrays

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Combine arrays
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// Copy array (shallow)
const copy = [...arr1]; // [1, 2, 3]

// Spread into function arguments
Math.max(...arr1); // 3
```

---

## 🛠️ Hands-On Exercise

### Student Grade Manager

```javascript
const students = [
  { name: "Ali Khan", grades: [85, 90, 78] },
  { name: "Sara Ahmed", grades: [92, 88, 95] },
  { name: "Karim Shah", grades: [67, 72, 58] },
  { name: "Fatima Bibi", grades: [45, 52, 38] },
  { name: "Basit Ullah", grades: [88, 91, 85] }
];

// 1. Calculate average grade for each student
const withAverages = students.map(student => ({
  ...student,
  average: student.grades.reduce((sum, g) => sum + g, 0) / student.grades.length
}));

// 2. Filter passing students (average >= 50)
const passing = withAverages.filter(s => s.average >= 50);

// 3. Sort by average (highest first)
const ranked = passing.sort((a, b) => b.average - a.average);

// 4. Create report
const report = ranked.map((s, i) =>
  `${i + 1}. ${s.name} — ${s.average.toFixed(1)}%`
);

console.log("=== Grade Report ===");
report.forEach(line => console.log(line));
```

---

## 📝 Homework

1. **Flatmap Exercise:** Given an array of sentences, split each into words and create a flat array of all words using `flatMap()`
2. **Unique Values:** Write a function that removes duplicates from an array (use `Set` or `filter`)
3. **Group By:** Write a function `groupBy(arr, key)` that groups an array of objects by a key
4. **Array Rotation:** Write a function that rotates an array by `n` positions

---

## 💡 Pro Tips

> 🔄 **Immutability:** Prefer methods that return new arrays (`map`, `filter`, `slice`, `concat`, spread) over methods that mutate (`push`, `pop`, `splice`, `sort`, `reverse`). This becomes crucial in React.

> ⚡ **Performance:** `for` loops are fastest, but `map/filter/reduce` are more readable. Use the readable option unless you're processing millions of items.

> 🧪 **`reduce` is king:** Almost any array transformation can be done with `reduce`. It's the Swiss Army knife of array methods!

---

[← Day 4](day-04.md) | [Back to Week 5](README.md) | [Day 6 →](day-06.md)
