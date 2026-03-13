# 📅 Week 11 — Day 3: Nested Routes & Layouts 🏗️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand nested routes and the `<Outlet />` component
- Build shared layouts (header, sidebar, footer)
- Create route-based code organization
- Implement index routes
- Build a protected route pattern

---

## 📖 Lesson Content

### 1. Why Nested Routes?

Many pages share common UI — a navbar, a sidebar, a footer. Without nested routes, you'd duplicate that layout in every page component. React Router's **nested routes** let you define a parent layout that wraps child routes.

```
/dashboard           → Dashboard layout + Overview
/dashboard/stats     → Dashboard layout + Stats
/dashboard/settings  → Dashboard layout + Settings
```

The Dashboard layout (navbar, sidebar) stays mounted — only the inner content changes.

### 2. The Outlet Component

`<Outlet />` is a placeholder that renders the **matched child route**:

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom';
import RootLayout from './layouts/RootLayout';
import Home from './pages/Home';
import About from './pages/About';
import BlogLayout from './layouts/BlogLayout';
import BlogList from './pages/BlogList';
import BlogPost from './pages/BlogPost';
import NotFound from './pages/NotFound';

function App() {
  return (
    <Routes>
      <Route path="/" element={<RootLayout />}>
        <Route index element={<Home />} />
        <Route path="about" element={<About />} />

        <Route path="blog" element={<BlogLayout />}>
          <Route index element={<BlogList />} />
          <Route path=":postId" element={<BlogPost />} />
        </Route>

        <Route path="*" element={<NotFound />} />
      </Route>
    </Routes>
  );
}
```

### 3. Building the Root Layout

```jsx
// layouts/RootLayout.jsx
import { Outlet } from 'react-router-dom';
import Navbar from '../components/Navbar';
import Footer from '../components/Footer';

function RootLayout() {
  return (
    <div className="root-layout">
      <Navbar />
      <main className="container">
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}

export default RootLayout;
```

Every child route renders **inside** `<Outlet />`. The Navbar and Footer stay persistent.

### 4. A Nested Blog Layout

```jsx
// layouts/BlogLayout.jsx
import { Outlet, NavLink } from 'react-router-dom';

function BlogLayout() {
  return (
    <div className="blog-layout">
      <aside className="sidebar">
        <h3>📁 Categories</h3>
        <ul>
          <li><NavLink to="/blog?category=tech">Tech</NavLink></li>
          <li><NavLink to="/blog?category=design">Design</NavLink></li>
          <li><NavLink to="/blog?category=career">Career</NavLink></li>
        </ul>
      </aside>

      <section className="blog-content">
        <Outlet />
      </section>
    </div>
  );
}

export default BlogLayout;
```

**Route nesting:**

```
/blog        → RootLayout > BlogLayout > BlogList  (index route)
/blog/42     → RootLayout > BlogLayout > BlogPost
/about       → RootLayout > About
```

### 5. Index Routes

An **index route** renders when the parent path matches exactly — no additional segment. It's the "default child."

```jsx
<Route path="blog" element={<BlogLayout />}>
  <Route index element={<BlogList />} />     {/* /blog */}
  <Route path=":postId" element={<BlogPost />} /> {/* /blog/42 */}
</Route>
```

### 6. Project Structure with Layouts

```
src/
├── components/
│   ├── Navbar.jsx
│   └── Footer.jsx
├── layouts/
│   ├── RootLayout.jsx
│   └── BlogLayout.jsx
├── pages/
│   ├── Home.jsx
│   ├── About.jsx
│   ├── BlogList.jsx
│   ├── BlogPost.jsx
│   └── NotFound.jsx
├── App.jsx
└── main.jsx
```

### 7. Protected Routes

Some pages require the user to be "logged in." You can build a **layout-based guard**:

```jsx
// layouts/ProtectedLayout.jsx
import { Outlet, Navigate } from 'react-router-dom';

function ProtectedLayout({ isLoggedIn }) {
  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }

  return (
    <div className="protected-layout">
      <h4>🔒 Protected Area</h4>
      <Outlet />
    </div>
  );
}

export default ProtectedLayout;
```

```jsx
// App.jsx
<Route path="/" element={<RootLayout />}>
  <Route index element={<Home />} />
  <Route path="login" element={<Login />} />

  {/* Protected section */}
  <Route element={<ProtectedLayout isLoggedIn={user !== null} />}>
    <Route path="dashboard" element={<Dashboard />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Route>
```

> Note: The protected `<Route>` has **no path** — it's a **layout route** that just wraps its children with the guard.

### 8. Navigate Component

`<Navigate>` performs a redirect when rendered:

```jsx
// Redirect /home to /
<Route path="/home" element={<Navigate to="/" replace />} />
```

### 9. Active Links in Nested Layouts

```jsx
function Navbar() {
  const linkClass = ({ isActive }) =>
    `nav-link ${isActive ? 'nav-link--active' : ''}`;

  return (
    <nav>
      <NavLink to="/" end className={linkClass}>Home</NavLink>
      <NavLink to="/blog" className={linkClass}>Blog</NavLink>
      <NavLink to="/about" className={linkClass}>About</NavLink>
    </nav>
  );
}
```

> The `end` prop on the Home `NavLink` ensures it's only active on `/` — not on `/blog` or `/about`.

---

## 💪 Hands-On Exercise

### Build a Dashboard with Nested Routes

1. Create a `RootLayout` with Navbar and Footer
2. Create a `DashboardLayout` with a sidebar nav (Overview, Stats, Settings)
3. Define nested routes:
   - `/` → Home
   - `/dashboard` → DashboardLayout + Overview (index)
   - `/dashboard/stats` → DashboardLayout + Stats
   - `/dashboard/settings` → DashboardLayout + Settings
4. Add `NavLink` in the sidebar with active styling
5. Add a `ProtectedLayout` — redirect to `/login` if not logged in
6. Use a simple `useState` to toggle login state

---

## 📝 Homework

1. Add a "Profile" section under `/dashboard/profile` with a nested layout of its own (tabs: Info, Security)
2. Create a redirect from `/home` to `/` using `<Navigate>`
3. Style the active `NavLink` in the sidebar with a left border highlight
4. Add a `useLocation` breadcrumb that shows the full nested path

---

## 💡 Pro Tips

- Use **layout routes** (no `path` prop) for auth guards — they apply to all children without adding a URL segment
- The `end` prop on `NavLink` prevents parent routes from staying "active" when a child is matched
- Keep layouts in a separate `layouts/` folder for clarity
- `<Navigate replace />` prevents the redirect from cluttering the browser's back history

---

[← Day 2](day-02.md) | [Back to Week 11](README.md) | [Day 4 →](day-04.md)
