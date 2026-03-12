# 📅 Day 2: Conditionals & Control Flow 🔀

> **Week 5 — JavaScript Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Variables, data types, and operators from Day 1

---

## 🎯 Today's Goals

- 🔀 Use `if`, `else if`, `else` statements
- 🔄 Master the `switch` statement
- ❓ Use the ternary operator for concise conditionals
- 🛡️ Handle truthy and falsy values

---

## 📘 Lesson Content

### if / else if / else

```javascript
const score = 85;

if (score >= 90) {
  console.log('Grade: A');
} else if (score >= 80) {
  console.log('Grade: B');
} else if (score >= 70) {
  console.log('Grade: C');
} else if (score >= 60) {
  console.log('Grade: D');
} else {
  console.log('Grade: F');
}
```

### Ternary Operator

```javascript
// condition ? valueIfTrue : valueIfFalse
const age = 20;
const status = age >= 18 ? 'Adult' : 'Minor';
console.log(status); // "Adult"

// Nested ternary (use sparingly)
const grade = score >= 90 ? 'A' : score >= 80 ? 'B' : 'C';
```

### Switch Statement

```javascript
const day = 'Monday';

switch (day) {
  case 'Monday':
  case 'Tuesday':
  case 'Wednesday':
  case 'Thursday':
  case 'Friday':
    console.log('Weekday — Time to code! 💻');
    break;
  case 'Saturday':
    console.log('Weekend — Review & practice! 📚');
    break;
  case 'Sunday':
    console.log('Rest day 😴');
    break;
  default:
    console.log('Invalid day');
}
```

### Truthy & Falsy Values

```javascript
// FALSY values (evaluate to false):
// false, 0, -0, '', "", ``, null, undefined, NaN

// TRUTHY values (everything else):
// true, any number (except 0), any string (except ''), [], {}, functions

// Practical usage
const username = '';
if (username) {
  console.log(`Welcome, ${username}`);
} else {
  console.log('Please enter a username');
}

// Short-circuit evaluation
const displayName = username || 'Guest';  // Fallback to 'Guest'

// Nullish coalescing (??) — only null/undefined trigger fallback
const value = null ?? 'default';  // 'default'
const zero = 0 ?? 'default';     // 0 (not 'default'!)
```

### Optional Chaining (?.)

```javascript
const user = {
  name: 'Ahmed',
  address: {
    city: 'Gilgit'
  }
};

// Without optional chaining — crashes if property missing
// const zip = user.address.zipCode.toString(); // ERROR!

// With optional chaining — returns undefined safely
const zip = user.address?.zipCode?.toString(); // undefined
console.log(zip); // undefined (no crash!)
```

### Combining Conditions

```javascript
const age = 25;
const hasLicense = true;
const hasInsurance = true;

// AND — all must be true
if (age >= 18 && hasLicense && hasInsurance) {
  console.log('Can drive');
}

// OR — at least one must be true
const isWeekend = true;
const isHoliday = false;
if (isWeekend || isHoliday) {
  console.log('Day off!');
}

// Negation
if (!isLoggedIn) {
  console.log('Please log in');
}
```

---

## 🛠️ Hands-On Exercise

### Build a Grade Calculator

```javascript
function calculateGrade(score) {
  if (score < 0 || score > 100) {
    return 'Invalid score';
  }
  if (score >= 90) return 'A';
  if (score >= 80) return 'B';
  if (score >= 70) return 'C';
  if (score >= 60) return 'D';
  return 'F';
}

// Test it
console.log(calculateGrade(95));  // A
console.log(calculateGrade(73));  // C
console.log(calculateGrade(42));  // F
```

### Build a Simple Login Check

```javascript
const correctUsername = 'admin';
const correctPassword = 'nextgen2025';

const inputUser = 'admin';
const inputPass = 'nextgen2025';

if (inputUser === correctUsername && inputPass === correctPassword) {
  console.log('✅ Login successful! Welcome, admin.');
} else if (inputUser !== correctUsername) {
  console.log('❌ Username not found.');
} else {
  console.log('❌ Incorrect password.');
}
```

---

## 📝 Homework

1. Build a **number guessing** logic: compare a guess to a secret number, print "Too high", "Too low", or "Correct!"
2. Create a **ticket pricing** system: Child (0-12) = $5, Teen (13-17) = $8, Adult (18-64) = $12, Senior (65+) = $6
3. Write a **day of the week** function using switch that returns activities for each day
4. Practice **truthy/falsy**: create 5 examples using `||`, `??`, and `?.`

---

## 💡 Pro Tips

> 🛡️ **Nullish Coalescing (`??`)** is better than `||` for defaults when `0` or `''` are valid values. `0 || 'default'` gives `'default'`, but `0 ?? 'default'` gives `0`.

> 🔗 **Optional Chaining (`?.`)** prevents "Cannot read property of undefined" errors. Use it when accessing deeply nested properties.

> 🧹 **Early returns** reduce nesting: Instead of deeply nested if/else, return early for edge cases.

---

[← Day 1](day-01.md) | [Back to Week 5](README.md) | [Day 3 →](day-03.md)
