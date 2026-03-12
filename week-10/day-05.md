# 📅 Day 5: Custom Hooks — Building Reusable Logic 🧩

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- `useState`, `useEffect`, `useRef` from Days 1-4
- Understanding of hooks rules

---

## 🎯 Today's Goals

- 🧩 Understand what custom hooks are and when to use them
- 🔁 Extract reusable logic into custom hooks
- 🛠️ Build practical hooks: `useLocalStorage`, `useFetch`, `useToggle`
- 📦 Create a hooks library for your projects

---

## 📘 Lesson Content

### What Are Custom Hooks?

```
Custom hooks are JavaScript functions that:
1. Start with "use" (useToggle, useFetch, useLocalStorage)
2. Can use other hooks inside them
3. Extract and share stateful logic between components

They're NOT components — they don't return JSX.
They return values and functions for components to use.
```

### Rules of Hooks

```
1. Only call hooks at the TOP LEVEL
   ❌ Don't call inside loops, conditions, or nested functions
2. Only call hooks from React functions
   ✅ Functional components or custom hooks
   ❌ Regular JavaScript functions
3. Name custom hooks starting with "use"
```

### useToggle

```jsx
// A simple hook for boolean toggles
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => setValue(prev => !prev), []);
  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);

  return { value, toggle, setTrue, setFalse };
}

// Usage
function DarkModeToggle() {
  const { value: isDark, toggle } = useToggle(false);

  return (
    <div className={isDark ? "dark" : "light"}>
      <button onClick={toggle}>
        {isDark ? "🌙 Dark" : "☀️ Light"}
      </button>
    </div>
  );
}

function DropdownMenu() {
  const { value: isOpen, toggle, setFalse: close } = useToggle();

  return (
    <div>
      <button onClick={toggle}>Menu {isOpen ? "▲" : "▼"}</button>
      {isOpen && (
        <ul className="dropdown">
          <li onClick={close}>Home</li>
          <li onClick={close}>About</li>
          <li onClick={close}>Contact</li>
        </ul>
      )}
    </div>
  );
}
```

### useLocalStorage

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);
      return stored !== null ? JSON.parse(stored) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch (err) {
      console.error("Failed to save to localStorage:", err);
    }
  }, [key, value]);

  const remove = useCallback(() => {
    localStorage.removeItem(key);
    setValue(initialValue);
  }, [key, initialValue]);

  return [value, setValue, remove];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage("theme", "light");
  const [fontSize, setFontSize] = useLocalStorage("fontSize", 16);
  const [notifications, setNotifications] = useLocalStorage("notifications", true);

  return (
    <div>
      <h2>Settings</h2>
      <label>
        Theme:
        <select value={theme} onChange={(e) => setTheme(e.target.value)}>
          <option value="light">Light</option>
          <option value="dark">Dark</option>
        </select>
      </label>
      <label>
        Font Size: {fontSize}px
        <input type="range" min={12} max={24} value={fontSize}
          onChange={(e) => setFontSize(Number(e.target.value))} />
      </label>
      <label>
        <input type="checkbox" checked={notifications}
          onChange={(e) => setNotifications(e.target.checked)} />
        Enable Notifications
      </label>
    </div>
  );
}
```

### useFetch

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) {
      setData(null);
      setLoading(false);
      return;
    }

    let cancelled = false;

    async function fetchData() {
      setLoading(true);
      setError(null);

      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const json = await response.json();

        if (!cancelled) {
          setData(json);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => { cancelled = true; };
  }, [url]);

  return { data, loading, error };
}

// Usage — so clean! 🎉
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`
  );

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <p>📍 {user.address.city}</p>
    </div>
  );
}

function PostList() {
  const { data: posts, loading } = useFetch(
    "https://jsonplaceholder.typicode.com/posts?_limit=10"
  );

  if (loading) return <p>Loading posts...</p>;

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### useDebounce

```jsx
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage — debounced search
function SearchPage() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 400);
  const { data: results, loading } = useFetch(
    debouncedQuery
      ? `https://api.github.com/search/repositories?q=${encodeURIComponent(debouncedQuery)}&per_page=10`
      : null
  );

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search GitHub repos..."
      />
      {loading && <p>Searching...</p>}
      {results?.items?.map(repo => (
        <div key={repo.id}>
          <h3>{repo.full_name}</h3>
          <p>⭐ {repo.stargazers_count}</p>
        </div>
      ))}
    </div>
  );
}
```

### useWindowSize

```jsx
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}

// Usage
function ResponsiveLayout({ children }) {
  const { width } = useWindowSize();

  if (width < 768) {
    return <MobileLayout>{children}</MobileLayout>;
  }
  return <DesktopLayout>{children}</DesktopLayout>;
}
```

### useOnClickOutside

```jsx
function useOnClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (event) => {
      if (!ref.current || ref.current.contains(event.target)) {
        return; // Click was inside, do nothing
      }
      handler(event);
    };

    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);

    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
}

// Usage — close dropdown when clicking outside
function Dropdown() {
  const [isOpen, setIsOpen] = useState(false);
  const ref = useRef(null);

  useOnClickOutside(ref, () => setIsOpen(false));

  return (
    <div ref={ref}>
      <button onClick={() => setIsOpen(!isOpen)}>Menu</button>
      {isOpen && (
        <ul className="dropdown">
          <li>Profile</li>
          <li>Settings</li>
          <li>Logout</li>
        </ul>
      )}
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Build a Hooks Library

Create 3 custom hooks and use them in a demo app:

```jsx
// src/hooks/useCounter.js
export function useCounter(initial = 0, step = 1) {
  const [count, setCount] = useState(initial);

  const increment = useCallback(() => setCount(c => c + step), [step]);
  const decrement = useCallback(() => setCount(c => c - step), [step]);
  const reset = useCallback(() => setCount(initial), [initial]);

  return { count, increment, decrement, reset };
}

// src/hooks/useForm.js
export function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);

  const handleChange = useCallback((e) => {
    const { name, value, type, checked } = e.target;
    setValues(prev => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value
    }));
  }, []);

  const resetForm = useCallback(() => setValues(initialValues), [initialValues]);

  return { values, handleChange, resetForm, setValues };
}

// Demo App using both hooks
function DemoApp() {
  const { count, increment, decrement, reset } = useCounter(0, 5);
  const { values, handleChange, resetForm } = useForm({
    name: "",
    email: ""
  });
  const { value: isDark, toggle: toggleTheme } = useToggle(false);

  return (
    <div className={isDark ? "dark" : "light"}>
      <button onClick={toggleTheme}>Toggle Theme</button>

      <h2>Counter: {count}</h2>
      <button onClick={decrement}>-5</button>
      <button onClick={increment}>+5</button>
      <button onClick={reset}>Reset</button>

      <input name="name" value={values.name} onChange={handleChange} placeholder="Name" />
      <input name="email" value={values.email} onChange={handleChange} placeholder="Email" />
      <button onClick={resetForm}>Clear Form</button>
    </div>
  );
}
```

---

## 📝 Homework

1. Build `useHover` — returns `true` when an element is hovered (use ref + event listeners)
2. Create `useKeyPress` — detects when a specific key is pressed
3. Build `useMediaQuery` — returns `true` if a CSS media query matches
4. Create `usePrevious` — returns the previous value of a state variable
5. Build `useAsync` — wraps any async function with loading/error/data state

---

## 💡 Pro Tips

> 🧩 **Extract when logic repeats:** If you copy-paste stateful logic between 2+ components, extract it into a custom hook.

> 📝 **Start with "use":** Always name custom hooks starting with `use`. This lets React enforce the rules of hooks and linters work correctly.

> 📦 **Compose hooks:** Custom hooks can use other custom hooks! `useFetch` + `useDebounce` = debounced search with minimal code.

---

[← Day 4](day-04.md) | [Back to Week 10](README.md) | [Day 6 →](day-06.md)
