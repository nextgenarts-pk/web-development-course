# 📅 Day 1: Introduction to React & JSX ⚛️

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- HTML, CSS, and JavaScript fundamentals (Weeks 1-8)
- ES6 modules, classes, arrow functions, destructuring
- Node.js installed on your machine

---

## 🎯 Today's Goals

- ⚛️ Understand what React is and why it dominates frontend development
- 🏗️ Set up a React project using Vite
- 📝 Write JSX — HTML-like syntax in JavaScript
- 🧩 Create your first React component
- 🔄 Understand the Virtual DOM

---

## 📘 Lesson Content

### What is React?

```
React is a JavaScript library for building user interfaces.

Why React?
✅ Component-based — build reusable UI pieces
✅ Declarative — describe WHAT you want, not HOW
✅ Virtual DOM — efficient updates, fast rendering
✅ Massive ecosystem — huge community, tons of libraries
✅ Used by: Facebook, Instagram, Netflix, Airbnb, Uber
```

### Traditional DOM vs React

```javascript
// ❌ Traditional — Imperative (tell the browser HOW)
const list = document.getElementById("todo-list");
const li = document.createElement("li");
li.textContent = "Buy groceries";
li.className = "todo-item";
list.appendChild(li);

// ✅ React — Declarative (tell React WHAT you want)
function TodoItem({ text }) {
  return <li className="todo-item">{text}</li>;
}
```

### Setting Up with Vite

```bash
# Create new React project
npm create vite@latest my-react-app -- --template react

# Navigate into the project
cd my-react-app

# Install dependencies
npm install

# Start development server
npm run dev
```

### Project Structure

```
my-react-app/
├── index.html          ← Entry HTML file
├── package.json        ← Dependencies & scripts
├── vite.config.js      ← Vite configuration
├── public/             ← Static assets
└── src/
    ├── main.jsx        ← React entry point
    ├── App.jsx         ← Root component
    ├── App.css         ← App styles
    └── index.css       ← Global styles
```

### Understanding main.jsx

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App.jsx";
import "./index.css";

// Create root and render the App component
ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

// React.StrictMode helps find bugs during development
// It renders components twice to detect side effects
```

### JSX — JavaScript XML

```jsx
// JSX looks like HTML but lives inside JavaScript
function Greeting() {
  const name = "Ahmed";
  const isLoggedIn = true;

  return (
    <div className="greeting">
      {/* className instead of class */}
      <h1>Hello, {name}!</h1>
      {/* Curly braces for JS expressions */}
      <p>2 + 2 = {2 + 2}</p>
      <p>Status: {isLoggedIn ? "Online" : "Offline"}</p>
      {/* Self-closing tags required */}
      <img src="/avatar.png" alt="Avatar" />
      <br />
    </div>
  );
}
```

### JSX Rules

```jsx
// Rule 1: Return a single root element
// ❌ Wrong
function Bad() {
  return (
    <h1>Title</h1>
    <p>Text</p>
  );
}

// ✅ Correct — wrap in a parent element
function Good() {
  return (
    <div>
      <h1>Title</h1>
      <p>Text</p>
    </div>
  );
}

// ✅ Or use a Fragment to avoid extra DOM nodes
function Better() {
  return (
    <>
      <h1>Title</h1>
      <p>Text</p>
    </>
  );
}

// Rule 2: Close all tags
// <img>, <br>, <input> must be <img />, <br />, <input />

// Rule 3: camelCase for attributes
// class → className
// for → htmlFor
// onclick → onClick
// tabindex → tabIndex
// style takes an object, not a string
<div style={{ backgroundColor: "blue", fontSize: "16px" }}>
  Styled div
</div>
```

### Your First Component

```jsx
// App.jsx
function App() {
  const courseTitle = "Full Stack Web Development";
  const topics = ["HTML", "CSS", "JavaScript", "React"];

  return (
    <div className="app">
      <header>
        <h1>🚀 {courseTitle}</h1>
        <p>by NextGen Arts</p>
      </header>

      <main>
        <h2>Topics We Cover:</h2>
        <ul>
          {topics.map((topic, index) => (
            <li key={index}>{topic}</li>
          ))}
        </ul>
      </main>

      <footer>
        <p>&copy; 2026 NextGen Arts</p>
      </footer>
    </div>
  );
}

export default App;
```

### Embedding Expressions in JSX

```jsx
function UserProfile() {
  const user = {
    name: "Sara Khan",
    age: 22,
    skills: ["React", "Node.js", "MongoDB"],
    avatar: "https://i.pravatar.cc/150?img=5"
  };

  const getGreeting = () => {
    const hour = new Date().getHours();
    if (hour < 12) return "Good morning";
    if (hour < 18) return "Good afternoon";
    return "Good evening";
  };

  return (
    <div className="profile">
      <img src={user.avatar} alt={user.name} />
      <h2>{getGreeting()}, {user.name}!</h2>
      <p>Age: {user.age}</p>
      <p>Skills: {user.skills.join(", ")}</p>
      <p>Total skills: {user.skills.length}</p>
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Build a Course Card Component

```jsx
function CourseCard() {
  const course = {
    title: "Full Stack Web Development",
    instructor: "NextGen Arts",
    duration: "24 weeks",
    level: "Beginner to Advanced",
    price: "PKR 25,000",
    enrolled: 156,
    rating: 4.8
  };

  return (
    <div style={{
      border: "1px solid #ddd",
      borderRadius: "12px",
      padding: "20px",
      maxWidth: "400px",
      fontFamily: "system-ui"
    }}>
      <h2>{course.title}</h2>
      <p>👨‍🏫 {course.instructor}</p>
      <p>⏱️ {course.duration} | 📊 {course.level}</p>
      <p>⭐ {course.rating}/5 ({course.enrolled} students)</p>
      <p style={{ fontSize: "1.5rem", fontWeight: "bold", color: "#e50914" }}>
        {course.price}
      </p>
      <button style={{
        background: "#e50914",
        color: "white",
        border: "none",
        padding: "10px 20px",
        borderRadius: "8px",
        cursor: "pointer",
        width: "100%",
        fontSize: "1rem"
      }}>
        Enroll Now
      </button>
    </div>
  );
}
```

---

## 📝 Homework

1. Create a new Vite + React project and explore the generated files
2. Build a **ProfileCard** component with your name, bio, and avatar
3. Create a **SkillsList** component that renders an array of your skills
4. Build a **Clock** component that displays the current date and time
5. Experiment with inline styles vs className

---

## 💡 Pro Tips

> ⚛️ **Think in components:** Break every UI into small, reusable pieces. If you see a repeated pattern, it's probably a component.

> 🔑 **JSX is just JavaScript:** Remember that JSX compiles to `React.createElement()` calls. The curly braces `{}` let you use any JavaScript expression.

> 🛠️ **Use React DevTools:** Install the React Developer Tools browser extension — it lets you inspect component trees, props, and state.

---

[Back to Week 9](README.md) | [Day 2 →](day-02.md)
