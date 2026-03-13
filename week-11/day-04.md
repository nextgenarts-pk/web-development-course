# 📅 Week 11 — Day 4: useContext & Global State 🌐

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand the "prop drilling" problem
- Learn `createContext`, `useContext`, and the Provider pattern
- Build a theme context (dark/light mode)
- Build an auth context for user state
- Combine multiple contexts in an app
- Know when context is the right tool and when it isn't

---

## 📖 Lesson Content

### 1. The Prop Drilling Problem

When many deeply nested components need the same data, you end up passing props through every intermediate component — even the ones that don't use it:

```
App → Layout → Sidebar → UserMenu → Avatar (needs user)
```

Every component in the chain receives and forwards `user` just so `Avatar` can use it. That's **prop drilling** and it makes code hard to maintain.

### 2. Context to the Rescue

Context lets you share values across the component tree **without** passing props at every level.

Three steps:
1. **Create** a context
2. **Provide** it at a high level
3. **Consume** it in any descendant

### 3. Building a Theme Context

```jsx
// context/ThemeContext.jsx
import { createContext, useContext, useState } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  function toggleTheme() {
    setTheme(prev => (prev === 'light' ? 'dark' : 'light'));
  }

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error('useTheme must be used within a ThemeProvider');
  }
  return context;
}
```

### 4. Providing the Context

Wrap the provider around the part of the tree that needs access:

```jsx
// main.jsx
import { BrowserRouter } from 'react-router-dom';
import { ThemeProvider } from './context/ThemeContext';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <ThemeProvider>
      <App />
    </ThemeProvider>
  </BrowserRouter>
);
```

### 5. Consuming the Context

Any component inside the provider can use it — no prop passing needed:

```jsx
// components/ThemeToggle.jsx
import { useTheme } from '../context/ThemeContext';

function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();

  return (
    <button onClick={toggleTheme}>
      {theme === 'light' ? '🌙 Dark Mode' : '☀️ Light Mode'}
    </button>
  );
}

export default ThemeToggle;
```

```jsx
// layouts/RootLayout.jsx
import { Outlet } from 'react-router-dom';
import { useTheme } from '../context/ThemeContext';
import Navbar from '../components/Navbar';

function RootLayout() {
  const { theme } = useTheme();

  return (
    <div className={`app ${theme}`}>
      <Navbar />
      <main>
        <Outlet />
      </main>
    </div>
  );
}
```

### 6. Auth Context — Managing User State

```jsx
// context/AuthContext.jsx
import { createContext, useContext, useState } from 'react';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  function login(userData) {
    setUser(userData);
  }

  function logout() {
    setUser(null);
  }

  return (
    <AuthContext.Provider value={{ user, login, logout, isLoggedIn: !!user }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within an AuthProvider');
  }
  return context;
}
```

### 7. Using Auth Context

```jsx
// components/LoginButton.jsx
import { useAuth } from '../context/AuthContext';

function LoginButton() {
  const { user, login, logout, isLoggedIn } = useAuth();

  if (isLoggedIn) {
    return (
      <div>
        <span>👤 {user.name}</span>
        <button onClick={logout}>Logout</button>
      </div>
    );
  }

  return (
    <button onClick={() => login({ name: 'Ali', email: 'ali@example.com' })}>
      Login
    </button>
  );
}
```

### 8. Protected Routes with Auth Context

Combine auth context with the protected layout from Day 3:

```jsx
// layouts/ProtectedLayout.jsx
import { Outlet, Navigate } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

function ProtectedLayout() {
  const { isLoggedIn } = useAuth();

  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}

export default ProtectedLayout;
```

No more passing `isLoggedIn` as a prop — the layout reads it from context.

### 9. Combining Multiple Providers

```jsx
// main.jsx
ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <AuthProvider>
      <ThemeProvider>
        <App />
      </ThemeProvider>
    </AuthProvider>
  </BrowserRouter>
);
```

Order matters: `AuthProvider` wraps `ThemeProvider`, so `ThemeProvider` can access auth if needed (but not vice-versa).

### 10. When to Use Context

| Use Context For | Don't Use Context For |
|---|---|
| Theme (dark/light) | Frequently changing data (per-keystroke) |
| Auth / current user | Local component state (form inputs) |
| Locale / language | Data only used by one component |
| App-wide settings | Everything — context is not a state manager |

> **Rule of thumb:** If you're passing the same prop through 3+ levels, context might help. If only 1-2 levels, just pass the prop.

---

## 💪 Hands-On Exercise

### Build a Multi-Context App

1. Create `ThemeContext` — light/dark with toggle, persist to localStorage
2. Create `AuthContext` — user state with login/logout
3. Build a `Navbar` that shows the user's name or a Login button
4. Build a `ThemeToggle` button in the Navbar
5. Apply `theme` as a CSS class on the root `<div>`
6. Create a protected `/dashboard` route that redirects to `/login` if not logged in
7. After login, redirect to `/dashboard`

---

## 📝 Homework

1. Create a `NotificationContext` with `addNotification` and `removeNotification` functions
2. Show a toast notification when the user logs in or out
3. Persist the theme preference to `localStorage` and read it on mount
4. Build a `useLocalStorage` + `useContext` combo for any persistent global setting

---

## 💡 Pro Tips

- **Always** export a custom hook (`useTheme`, `useAuth`) instead of exporting the raw context — it adds error checking and cleaner imports
- Throw an error in the custom hook if it's used outside its Provider — catches bugs early
- Context re-renders **all consumers** when the value changes — split large contexts into smaller ones if performance matters
- Don't use context as a replacement for a proper state management library in very large apps

---

[← Day 3](day-03.md) | [Back to Week 11](README.md) | [Day 5 →](day-05.md)
