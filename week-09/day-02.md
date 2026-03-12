# 📅 Day 2: Components — Functional Components & Composition 🧩

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- React & JSX basics from Day 1
- Vite project setup

---

## 🎯 Today's Goals

- 🧩 Understand what components are and why they matter
- 📁 Organize components into separate files
- 🏗️ Compose complex UIs by nesting components
- 🎨 Build a multi-component page layout

---

## 📘 Lesson Content

### What is a Component?

```
A component is a reusable, self-contained piece of UI.

Think of it like LEGO blocks:
🧱 Header component
🧱 Navigation component
🧱 Card component
🧱 Footer component

You combine them to build a complete page.
```

### Creating Components

```jsx
// ✅ Functional Component (modern standard)
function Welcome() {
  return <h1>Welcome to NextGen Arts!</h1>;
}

// ✅ Arrow function component
const Welcome = () => {
  return <h1>Welcome to NextGen Arts!</h1>;
};

// ✅ Implicit return for simple components
const Welcome = () => <h1>Welcome to NextGen Arts!</h1>;
```

### Component Rules

```jsx
// Rule 1: Name must start with UPPERCASE
function header() { }   // ❌ React treats lowercase as HTML
function Header() { }   // ✅ React treats uppercase as component

// Rule 2: Must return JSX (or null)
function Empty() {
  return null; // ✅ Renders nothing
}

// Rule 3: One component per file (best practice)
// src/components/Header.jsx
export default function Header() {
  return <header><h1>My App</h1></header>;
}
```

### Component File Organization

```
src/
├── components/
│   ├── Header.jsx
│   ├── Footer.jsx
│   ├── Navbar.jsx
│   └── Card.jsx
├── App.jsx
└── main.jsx
```

```jsx
// src/components/Header.jsx
export default function Header() {
  return (
    <header className="header">
      <h1>🚀 NextGen Arts</h1>
      <p>Full Stack Web Development</p>
    </header>
  );
}

// src/components/Navbar.jsx
export default function Navbar() {
  return (
    <nav className="navbar">
      <a href="#home">Home</a>
      <a href="#about">About</a>
      <a href="#courses">Courses</a>
      <a href="#contact">Contact</a>
    </nav>
  );
}

// src/components/Footer.jsx
export default function Footer() {
  return (
    <footer className="footer">
      <p>&copy; 2026 NextGen Arts SMC-Private Ltd.</p>
    </footer>
  );
}
```

### Component Composition

```jsx
// src/App.jsx — Composing components together
import Header from "./components/Header";
import Navbar from "./components/Navbar";
import Footer from "./components/Footer";

function App() {
  return (
    <div className="app">
      <Header />
      <Navbar />
      <main>
        <h2>Welcome to our course!</h2>
        <p>Start your web development journey today.</p>
      </main>
      <Footer />
    </div>
  );
}

export default App;
```

### Nesting Components

```jsx
// Components can contain other components

function StudentCard() {
  return (
    <div className="student-card">
      <Avatar />
      <StudentInfo />
      <SkillBadges />
    </div>
  );
}

function Avatar() {
  return <img src="https://i.pravatar.cc/100" alt="Student" className="avatar" />;
}

function StudentInfo() {
  return (
    <div className="info">
      <h3>Ali Hassan</h3>
      <p>Web Developer Student</p>
      <p>📍 Gilgit, Pakistan</p>
    </div>
  );
}

function SkillBadges() {
  const skills = ["HTML", "CSS", "JavaScript", "React"];
  return (
    <div className="badges">
      {skills.map((skill, i) => (
        <span key={i} className="badge">{skill}</span>
      ))}
    </div>
  );
}
```

### Building a Complete Page Layout

```jsx
// A real-world page composition

function App() {
  return (
    <div className="app-layout">
      <Header />
      <div className="content-wrapper">
        <Sidebar />
        <MainContent />
      </div>
      <Footer />
    </div>
  );
}

function Sidebar() {
  return (
    <aside className="sidebar">
      <h3>📚 Course Modules</h3>
      <ul>
        <li>HTML & CSS</li>
        <li>JavaScript</li>
        <li className="active">React.js</li>
        <li>Node.js</li>
        <li>MongoDB</li>
      </ul>
    </aside>
  );
}

function MainContent() {
  return (
    <main className="main-content">
      <h2>⚛️ React.js Fundamentals</h2>
      <p>Learn to build modern user interfaces with React.</p>

      <section className="lesson-cards">
        <LessonCard title="JSX Syntax" duration="30 min" />
        <LessonCard title="Components" duration="45 min" />
        <LessonCard title="Props" duration="40 min" />
      </section>
    </main>
  );
}

function LessonCard({ title, duration }) {
  return (
    <div className="lesson-card">
      <h4>{title}</h4>
      <span>⏱️ {duration}</span>
    </div>
  );
}
```

### Component Reusability

```jsx
// A reusable Button component
function Button({ text, variant = "primary" }) {
  const styles = {
    primary: { background: "#e50914", color: "white" },
    secondary: { background: "#333", color: "white" },
    outline: { background: "transparent", border: "2px solid #e50914", color: "#e50914" }
  };

  return (
    <button style={{
      ...styles[variant],
      padding: "10px 20px",
      borderRadius: "8px",
      border: "none",
      cursor: "pointer",
      fontSize: "1rem",
      ...(!styles[variant].border && { border: "none" })
    }}>
      {text}
    </button>
  );
}

// Use it multiple times with different variants
function ActionBar() {
  return (
    <div className="actions">
      <Button text="Enroll Now" variant="primary" />
      <Button text="Learn More" variant="secondary" />
      <Button text="Contact Us" variant="outline" />
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Build a Team Page

Create a page that displays team members using component composition:

```jsx
// src/components/TeamMember.jsx
export default function TeamMember({ name, role, image }) {
  return (
    <div className="team-member">
      <img src={image} alt={name} />
      <h3>{name}</h3>
      <p>{role}</p>
    </div>
  );
}

// src/App.jsx
import TeamMember from "./components/TeamMember";

function App() {
  return (
    <div className="team-page">
      <h1>Our Team</h1>
      <div className="team-grid">
        <TeamMember
          name="Fatima Ali"
          role="Lead Instructor"
          image="https://i.pravatar.cc/150?img=1"
        />
        <TeamMember
          name="Hassan Khan"
          role="Backend Developer"
          image="https://i.pravatar.cc/150?img=3"
        />
        <TeamMember
          name="Aisha Noor"
          role="UI/UX Designer"
          image="https://i.pravatar.cc/150?img=5"
        />
      </div>
    </div>
  );
}
```

---

## 📝 Homework

1. Create a **Header**, **Main**, and **Footer** component in separate files
2. Build a **CourseCard** component and render 4 different courses
3. Create a **Testimonial** component and a **TestimonialList** that renders 3
4. Build a **FAQ** component with question and answer sections
5. Organize all components in a `components/` folder with clean imports

---

## 💡 Pro Tips

> 📁 **One component per file:** Keep each component in its own `.jsx` file. It makes code easier to find and maintain.

> 🔤 **Naming conventions:** Component files should be PascalCase (`Header.jsx`, `CourseCard.jsx`). This matches the component name.

> 🧩 **Think small:** Start with the smallest components first, then compose them into larger ones. A `Button` can be used in a `Card`, which can be used in a `CardList`.

---

[← Day 1](day-01.md) | [Back to Week 9](README.md) | [Day 3 →](day-03.md)
