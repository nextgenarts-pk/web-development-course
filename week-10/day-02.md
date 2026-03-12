# 📅 Day 2: useEffect — Side Effects, Cleanup & Lifecycle ⚡

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- `useState` deep dive from Day 1
- Basic understanding of component rendering

---

## 🎯 Today's Goals

- ⚡ Understand what "side effects" are in React
- 🔄 Master the `useEffect` dependency array
- 🧹 Write cleanup functions to prevent memory leaks
- 🌐 Fetch data from APIs with useEffect
- ⏱️ Use timers and event listeners safely

---

## 📘 Lesson Content

### What Are Side Effects?

```
Side effects = anything that reaches OUTSIDE the component:
- Fetching data from an API
- Setting up event listeners
- Modifying the DOM directly
- Setting timers (setTimeout, setInterval)
- Subscribing to services
- Writing to localStorage

React components should be "pure" during rendering.
Side effects happen AFTER rendering, inside useEffect.
```

### useEffect Syntax

```jsx
import { useState, useEffect } from "react";

function MyComponent() {
  // useEffect(callback, dependencyArray)

  // Runs after EVERY render
  useEffect(() => {
    console.log("Rendered!");
  });

  // Runs ONCE after the first render (mount)
  useEffect(() => {
    console.log("Component mounted!");
  }, []); // ← Empty dependency array

  // Runs when "count" changes
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log(`Count changed to: ${count}`);
  }, [count]); // ← Dependency array with count

  return <div>...</div>;
}
```

### Dependency Array Explained

```jsx
// 1. No dependency array → runs after EVERY render
useEffect(() => {
  console.log("Every render");
});

// 2. Empty array → runs ONCE (on mount)
useEffect(() => {
  console.log("Mount only");
}, []);

// 3. Specific dependencies → runs when those values change
useEffect(() => {
  console.log("name or age changed");
}, [name, age]);
```

### Document Title Updater

```jsx
function PageTitle() {
  const [title, setTitle] = useState("My App");

  useEffect(() => {
    document.title = title;
  }, [title]); // Updates document.title whenever "title" state changes

  return (
    <input
      value={title}
      onChange={(e) => setTitle(e.target.value)}
      placeholder="Page title"
    />
  );
}
```

### Fetching Data with useEffect

```jsx
function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let isCancelled = false; // Prevent state update on unmounted component

    async function fetchUsers() {
      try {
        setLoading(true);
        const response = await fetch("https://jsonplaceholder.typicode.com/users");
        if (!response.ok) throw new Error("Failed to fetch");
        const data = await response.json();

        if (!isCancelled) {
          setUsers(data);
          setError(null);
        }
      } catch (err) {
        if (!isCancelled) {
          setError(err.message);
        }
      } finally {
        if (!isCancelled) {
          setLoading(false);
        }
      }
    }

    fetchUsers();

    // Cleanup: if the component unmounts before the fetch completes
    return () => {
      isCancelled = true;
    };
  }, []); // Empty array → fetch once on mount

  if (loading) return <p>Loading users...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name} — {user.email}</li>
      ))}
    </ul>
  );
}
```

### Cleanup Function

```jsx
// Cleanup function runs:
// 1. Before the effect runs again (when dependencies change)
// 2. When the component unmounts

function WindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    // Setup
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    window.addEventListener("resize", handleResize);

    // Cleanup — remove event listener
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []); // Run once, clean up on unmount

  return (
    <p>Window: {size.width} × {size.height}</p>
  );
}
```

### Timer with Cleanup

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  useEffect(() => {
    if (!isRunning) return; // Don't start timer if not running

    const intervalId = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    // Cleanup — clear interval when isRunning changes or component unmounts
    return () => clearInterval(intervalId);
  }, [isRunning]);

  return (
    <div>
      <h2>⏱️ {seconds}s</h2>
      <button onClick={() => setIsRunning(!isRunning)}>
        {isRunning ? "Pause" : "Start"}
      </button>
      <button onClick={() => { setSeconds(0); setIsRunning(false); }}>
        Reset
      </button>
    </div>
  );
}
```

### Fetch with Search Query

```jsx
function SearchPosts() {
  const [query, setQuery] = useState("");
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query.trim()) {
      setPosts([]);
      return;
    }

    let cancelled = false;
    const timeoutId = setTimeout(async () => {
      setLoading(true);
      try {
        const res = await fetch(
          `https://jsonplaceholder.typicode.com/posts?q=${encodeURIComponent(query)}`
        );
        const data = await res.json();
        if (!cancelled) setPosts(data);
      } catch (err) {
        console.error(err);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }, 500); // Debounce

    return () => {
      cancelled = true;
      clearTimeout(timeoutId);
    };
  }, [query]); // Re-run when query changes

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search posts..."
      />
      {loading && <p>Searching...</p>}
      <ul>
        {posts.map(post => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}
```

### LocalStorage Sync

```jsx
function PersistentCounter() {
  const [count, setCount] = useState(() => {
    // Lazy initialization — runs only on first render
    const saved = localStorage.getItem("counter");
    return saved ? JSON.parse(saved) : 0;
  });

  // Save to localStorage whenever count changes
  useEffect(() => {
    localStorage.setItem("counter", JSON.stringify(count));
  }, [count]);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(prev => prev + 1)}>Increment</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Live Clock with Greeting

```jsx
function LiveClock() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const timer = setInterval(() => {
      setTime(new Date());
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  const hours = time.getHours();
  const greeting = hours < 12 ? "Good morning" : hours < 18 ? "Good afternoon" : "Good evening";

  return (
    <div className="text-center p-8">
      <h2 className="text-2xl">{greeting}! 👋</h2>
      <p className="text-5xl font-mono font-bold my-4">
        {time.toLocaleTimeString()}
      </p>
      <p className="text-gray-500">{time.toLocaleDateString()}</p>
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **random quote fetcher** — fetch a new quote on button click and on mount
2. Create a **dark mode toggle** that syncs preference to localStorage
3. Build a **mouse position tracker** (`mousemove` listener with cleanup)
4. Create a **countdown timer** component with start, pause, and reset
5. Build a **GitHub user search** — fetch user data when the input changes (debounced)

---

## 💡 Pro Tips

> 🧹 **Always clean up:** If your effect sets up listeners, timers, or subscriptions, ALWAYS return a cleanup function.

> 📦 **Lazy initialization:** Use a function inside `useState(() => ...)` for expensive initial values (like reading from localStorage).

> 🚫 **Don't lie about dependencies:** Include ALL values from the component scope that the effect uses. The ESLint rule `react-hooks/exhaustive-deps` will warn you.

---

[← Day 1](day-01.md) | [Back to Week 10](README.md) | [Day 3 →](day-03.md)
