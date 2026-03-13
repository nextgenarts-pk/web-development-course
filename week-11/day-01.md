# 📅 Week 11 — Day 1: React Router Setup & Basic Routing 🗺️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand why single-page apps need client-side routing
- Install and configure React Router v6
- Create routes for different pages
- Navigate with `Link` and `NavLink`
- Handle 404 / catch-all routes
- Use `useNavigate` for programmatic navigation

---

## 📖 Lesson Content

### 1. Why Client-Side Routing?

Traditional websites request a **new HTML page** from the server for every URL. Single-page apps (SPAs) load once and update the DOM dynamically — but users still expect URLs to work, the back button to function, and bookmarks to be shareable.

**React Router** solves this by mapping URL paths to React components — without a full page reload.

### 2. Installation

```bash
npm create vite@latest blog-app -- --template react
cd blog-app
npm install
npm install react-router-dom
```

### 3. Setting Up the Router

Wrap your app in `BrowserRouter`:

```jsx
// main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);
```

### 4. Defining Routes

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';
import NotFound from './pages/NotFound';

function App() {
  return (
    <div className="app">
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </div>
  );
}

export default App;
```

> **`path="*"`** catches any URL that doesn't match — perfect for 404 pages.

### 5. Creating Page Components

```jsx
// pages/Home.jsx
function Home() {
  return (
    <div>
      <h1>🏠 Home</h1>
      <p>Welcome to our blog!</p>
    </div>
  );
}

export default Home;
```

```jsx
// pages/About.jsx
function About() {
  return (
    <div>
      <h1>ℹ️ About</h1>
      <p>This is a multi-page React app with React Router.</p>
    </div>
  );
}

export default About;
```

```jsx
// pages/NotFound.jsx
import { Link } from 'react-router-dom';

function NotFound() {
  return (
    <div>
      <h1>404 — Page Not Found</h1>
      <p>The page you're looking for doesn't exist.</p>
      <Link to="/">Go Home</Link>
    </div>
  );
}

export default NotFound;
```

### 6. Navigation with Link & NavLink

**Never** use `<a href="...">` for internal navigation — it causes a full page reload.

```jsx
import { Link, NavLink } from 'react-router-dom';

function Navbar() {
  return (
    <nav className="navbar">
      <Link to="/" className="logo">📝 Blog</Link>

      <ul className="nav-links">
        <li>
          <NavLink
            to="/"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            Home
          </NavLink>
        </li>
        <li>
          <NavLink
            to="/about"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            About
          </NavLink>
        </li>
        <li>
          <NavLink
            to="/contact"
            className={({ isActive }) => isActive ? 'active' : ''}
          >
            Contact
          </NavLink>
        </li>
      </ul>
    </nav>
  );
}

export default Navbar;
```

| Feature | `<Link>` | `<NavLink>` |
|---------|----------|-------------|
| Prevents page reload | ✅ | ✅ |
| `isActive` class/style | ❌ | ✅ |
| Use for | Logos, buttons, text links | Navigation menus |

### 7. Programmatic Navigation with useNavigate

```jsx
import { useNavigate } from 'react-router-dom';

function ContactForm() {
  const navigate = useNavigate();

  function handleSubmit(e) {
    e.preventDefault();
    // ... handle form submission
    navigate('/'); // Go to home after submit
  }

  function handleCancel() {
    navigate(-1); // Go back one step (like browser back button)
  }

  return (
    <form onSubmit={handleSubmit}>
      <h2>Contact Us</h2>
      <input type="text" placeholder="Your name" required />
      <textarea placeholder="Message" required />
      <button type="submit">Send</button>
      <button type="button" onClick={handleCancel}>Cancel</button>
    </form>
  );
}
```

### 8. useLocation — Reading Current URL

```jsx
import { useLocation } from 'react-router-dom';

function Breadcrumb() {
  const location = useLocation();

  return (
    <p className="breadcrumb">
      📍 Current path: <code>{location.pathname}</code>
    </p>
  );
}
```

### 9. Project Structure

```
src/
├── components/
│   ├── Navbar.jsx
│   └── Footer.jsx
├── pages/
│   ├── Home.jsx
│   ├── About.jsx
│   ├── Contact.jsx
│   └── NotFound.jsx
├── App.jsx
├── main.jsx
└── index.css
```

> **Convention:** Place routed components in `pages/` and shared UI in `components/`.

---

## 💪 Hands-On Exercise

### Build a Portfolio Site with 4 Pages

1. Create a Vite + React project with React Router
2. Define routes: `/`, `/projects`, `/blog`, `/contact`
3. Build a `Navbar` using `NavLink` with an active class
4. Build a `Footer` shown on every page
5. Create a 404 page with a link back to home
6. Add `useNavigate` on the contact form to redirect after submit

---

## 📝 Homework

1. Add a "Skills" page (`/skills`) with a list of technologies
2. Highlight the current `NavLink` with a colored underline
3. Add `useLocation` to display the current path in the footer
4. Use `navigate(-1)` on a "Go Back" button on the 404 page

---

## 💡 Pro Tips

- Always wrap `<Routes>` inside `<BrowserRouter>` — only one `BrowserRouter` per app
- `NavLink` is great for navbars — it auto-applies `isActive` for styling
- `navigate(-1)` mimics the browser back button — users love it
- Keep route definitions in `App.jsx` for a clear routing overview

---

[← Back to Week 11](README.md) | [Day 2 →](day-02.md)
