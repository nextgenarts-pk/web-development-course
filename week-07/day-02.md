# 📅 Day 2: Classes & Object-Oriented Programming 🏗️

> **Week 7 — Advanced ES6+ & Modern JavaScript** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Objects, methods, and `this` keyword (Week 5)
- ES6 modules (Day 1)

---

## 🎯 Today's Goals

- 🏗️ Define classes with constructors, methods, and properties
- 🔒 Use private fields and methods
- 🧬 Implement inheritance with `extends` and `super`
- 🔧 Use static methods and properties
- 📐 Understand getters and setters

---

## 📘 Lesson Content

### Class Basics

```javascript
class User {
  constructor(name, email) {
    this.name = name;
    this.email = email;
    this.createdAt = new Date();
  }

  // Instance method
  greet() {
    return `Hi, I'm ${this.name}`;
  }

  getInfo() {
    return `${this.name} (${this.email})`;
  }
}

const user = new User("Ali Khan", "ali@example.com");
console.log(user.greet());   // "Hi, I'm Ali Khan"
console.log(user.getInfo()); // "Ali Khan (ali@example.com)"
```

### Private Fields & Methods

```javascript
class BankAccount {
  // Private fields (start with #)
  #balance;
  #pin;
  #transactionHistory;

  constructor(owner, initialBalance, pin) {
    this.owner = owner;
    this.#balance = initialBalance;
    this.#pin = pin;
    this.#transactionHistory = [];
  }

  // Private method
  #recordTransaction(type, amount) {
    this.#transactionHistory.push({
      type,
      amount,
      date: new Date().toISOString(),
      balance: this.#balance
    });
  }

  deposit(amount) {
    if (amount <= 0) throw new Error("Deposit must be positive");
    this.#balance += amount;
    this.#recordTransaction("deposit", amount);
    return this.#balance;
  }

  withdraw(amount, pin) {
    if (pin !== this.#pin) throw new Error("Invalid PIN");
    if (amount > this.#balance) throw new Error("Insufficient funds");
    this.#balance -= amount;
    this.#recordTransaction("withdrawal", amount);
    return this.#balance;
  }

  get balance() {
    return this.#balance;
  }

  get history() {
    return [...this.#transactionHistory]; // return copy
  }
}

const account = new BankAccount("Ali", 1000, "1234");
account.deposit(500);
account.withdraw(200, "1234");
console.log(account.balance);  // 1300
// account.#balance;  // SyntaxError — private!
```

### Getters & Setters

```javascript
class Temperature {
  #celsius;

  constructor(celsius) {
    this.#celsius = celsius;
  }

  get celsius() {
    return this.#celsius;
  }

  set celsius(value) {
    if (value < -273.15) throw new Error("Below absolute zero!");
    this.#celsius = value;
  }

  get fahrenheit() {
    return this.#celsius * 9 / 5 + 32;
  }

  set fahrenheit(value) {
    this.#celsius = (value - 32) * 5 / 9;
  }
}

const temp = new Temperature(100);
console.log(temp.fahrenheit); // 212
temp.fahrenheit = 32;
console.log(temp.celsius);    // 0
```

### Inheritance

```javascript
class Animal {
  constructor(name, sound) {
    this.name = name;
    this.sound = sound;
  }

  speak() {
    return `${this.name} says ${this.sound}!`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name, "Woof");  // Call parent constructor
    this.breed = breed;
  }

  // Override parent method
  speak() {
    return `🐕 ${super.speak()} [${this.breed}]`;
  }

  fetch(item) {
    return `${this.name} fetches the ${item}!`;
  }
}

class Cat extends Animal {
  constructor(name, indoor = true) {
    super(name, "Meow");
    this.indoor = indoor;
  }
}

const dog = new Dog("Rex", "German Shepherd");
console.log(dog.speak());  // "🐕 Rex says Woof! [German Shepherd]"
console.log(dog.fetch("ball")); // "Rex fetches the ball!"

console.log(dog instanceof Dog);    // true
console.log(dog instanceof Animal); // true
```

### Static Methods & Properties

```javascript
class MathUtils {
  static PI = 3.14159;

  static circle(radius) {
    return {
      area: MathUtils.PI * radius ** 2,
      circumference: 2 * MathUtils.PI * radius
    };
  }

  static random(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  static clamp(value, min, max) {
    return Math.min(Math.max(value, min), max);
  }
}

// Called on the class, not instances
console.log(MathUtils.circle(5));   // { area: 78.53..., circumference: 31.41... }
console.log(MathUtils.random(1, 10));
```

### Factory Pattern with Classes

```javascript
class Notification {
  #type;
  #message;
  #duration;

  constructor(type, message, duration = 3000) {
    this.#type = type;
    this.#message = message;
    this.#duration = duration;
  }

  // Factory methods
  static success(message) {
    return new Notification("success", message);
  }

  static error(message) {
    return new Notification("error", message, 5000);
  }

  static warning(message) {
    return new Notification("warning", message, 4000);
  }

  show() {
    const el = document.createElement("div");
    el.className = `notification notification--${this.#type}`;
    el.textContent = this.#message;
    document.body.appendChild(el);

    setTimeout(() => el.remove(), this.#duration);
  }
}

// Usage
Notification.success("Saved successfully!").show();
Notification.error("Something went wrong!").show();
```

---

## 🛠️ Hands-On Exercise

### Build a Task Manager with OOP

```javascript
class Task {
  static #idCounter = 0;

  constructor(title, priority = "medium") {
    this.id = ++Task.#idCounter;
    this.title = title;
    this.priority = priority;
    this.completed = false;
    this.createdAt = new Date();
  }

  toggle() {
    this.completed = !this.completed;
    return this;
  }
}

class TaskManager {
  #tasks = [];

  add(title, priority) {
    const task = new Task(title, priority);
    this.#tasks.push(task);
    return task;
  }

  remove(id) {
    this.#tasks = this.#tasks.filter(t => t.id !== id);
  }

  getAll(filter = "all") {
    if (filter === "active") return this.#tasks.filter(t => !t.completed);
    if (filter === "completed") return this.#tasks.filter(t => t.completed);
    return [...this.#tasks];
  }

  getByPriority(priority) {
    return this.#tasks.filter(t => t.priority === priority);
  }

  get stats() {
    return {
      total: this.#tasks.length,
      completed: this.#tasks.filter(t => t.completed).length,
      active: this.#tasks.filter(t => !t.completed).length
    };
  }
}

// Usage
const manager = new TaskManager();
manager.add("Learn JavaScript", "high");
manager.add("Build Portfolio", "high");
manager.add("Read Documentation", "low");
console.log(manager.stats); // { total: 3, completed: 0, active: 3 }
```

---

## 📝 Homework

1. Create a `Playlist` class with `Song` items — add, remove, shuffle, next, previous, repeat modes
2. Build a `ShoppingCart` class with `CartItem` — add, remove, update quantity, calculate total with tax
3. Create a class hierarchy: `Shape` → `Circle`, `Rectangle`, `Triangle` — each with `area()` and `perimeter()` methods
4. Extend the `BankAccount` example with `SavingsAccount` (interest rate) and `CheckingAccount` (overdraft limit)

---

## 💡 Pro Tips

> 🔒 **Private fields:** Use `#` prefix for fields that should not be accessed outside the class. This is true encapsulation, unlike the old `_` convention.

> 🏭 **Factory methods:** Use `static` methods as named constructors for common creation patterns (e.g., `User.fromJSON(data)`).

> 🧬 **Favor composition over inheritance:** Don't create deep class hierarchies. If a class needs diverse behavior, compose it from smaller objects rather than inheriting from a chain of parents.

---

[← Day 1](day-01.md) | [Back to Week 7](README.md) | [Day 3 →](day-03.md)
