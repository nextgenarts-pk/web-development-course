# 📅 Day 4: useRef, useMemo & useCallback 🔧

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- `useState` and `useEffect` from Days 1-2
- Understanding of re-renders in React

---

## 🎯 Today's Goals

- 🔧 Use `useRef` for DOM access and mutable values
- ⚡ Optimize with `useMemo` for expensive computations
- 🔄 `useCallback` for stable function references
- 🧠 Understand when (and when NOT) to optimize

---

## 📘 Lesson Content

### useRef — Accessing DOM Elements

```jsx
import { useRef, useEffect } from "react";

function AutoFocusInput() {
  const inputRef = useRef(null);

  useEffect(() => {
    // Focus the input on mount
    inputRef.current.focus();
  }, []);

  return (
    <input ref={inputRef} placeholder="I'm auto-focused!" />
  );
}
```

### useRef — Storing Mutable Values

```jsx
// useRef persists values between renders WITHOUT causing re-renders

function StopWatch() {
  const [time, setTime] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const intervalRef = useRef(null); // Persists across renders

  const start = () => {
    if (isRunning) return;
    setIsRunning(true);
    intervalRef.current = setInterval(() => {
      setTime(prev => prev + 10);
    }, 10);
  };

  const stop = () => {
    clearInterval(intervalRef.current);
    setIsRunning(false);
  };

  const reset = () => {
    clearInterval(intervalRef.current);
    setIsRunning(false);
    setTime(0);
  };

  // Format time
  const minutes = Math.floor(time / 60000);
  const seconds = Math.floor((time % 60000) / 1000);
  const ms = Math.floor((time % 1000) / 10);

  return (
    <div>
      <h2>
        {String(minutes).padStart(2, "0")}:
        {String(seconds).padStart(2, "0")}.
        {String(ms).padStart(2, "0")}
      </h2>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### useRef — Tracking Previous Values

```jsx
function PreviousValue() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef(0);

  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCountRef.current}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}
```

### useRef vs useState

```
┌────────────────────────────────────────────┐
│         useRef           vs   useState     │
├────────────────────────────────────────────┤
│ Does NOT trigger re-render  │ Triggers re-render      │
│ .current is mutable         │ Immutable (use setter)  │
│ Persists across renders     │ Persists across renders │
│ Use for: DOM refs, timers   │ Use for: UI data        │
│ Use for: values you DON'T   │ Use for: values you     │
│   want to display           │   want to display       │
└────────────────────────────────────────────┘
```

### useMemo — Memoizing Expensive Computations

```jsx
import { useState, useMemo } from "react";

function ExpensiveList() {
  const [items, setItems] = useState(generateItems(1000));
  const [filter, setFilter] = useState("");
  const [theme, setTheme] = useState("light");

  // ✅ Only recomputes when items or filter change
  // NOT when theme changes (which also triggers a re-render)
  const filteredItems = useMemo(() => {
    console.log("Filtering...");
    return items.filter(item =>
      item.name.toLowerCase().includes(filter.toLowerCase())
    );
  }, [items, filter]);

  // ✅ Expensive calculation memoized
  const stats = useMemo(() => {
    console.log("Computing stats...");
    return {
      total: filteredItems.length,
      avgPrice: filteredItems.reduce((sum, i) => sum + i.price, 0) / filteredItems.length || 0
    };
  }, [filteredItems]);

  return (
    <div className={theme}>
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter items..."
      />
      <button onClick={() => setTheme(t => t === "light" ? "dark" : "light")}>
        Toggle Theme
      </button>
      <p>Showing {stats.total} items (avg price: ${stats.avgPrice.toFixed(2)})</p>
      <ul>
        {filteredItems.map(item => (
          <li key={item.id}>{item.name} — ${item.price}</li>
        ))}
      </ul>
    </div>
  );
}
```

### useCallback — Memoizing Functions

```jsx
import { useState, useCallback } from "react";

function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");

  // ❌ New function created every render
  const handleClick = () => {
    console.log("Clicked!");
  };

  // ✅ Same function reference unless dependencies change
  const handleClickMemo = useCallback(() => {
    console.log("Clicked! Count:", count);
  }, [count]);

  // ✅ Useful when passing to child components
  const handleDelete = useCallback((id) => {
    setItems(prev => prev.filter(item => item.id !== id));
  }, []);

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <p>Count: {count}</p>
      <ChildComponent onClick={handleClickMemo} />
    </div>
  );
}

// React.memo prevents re-render if props haven't changed
const ChildComponent = React.memo(function ChildComponent({ onClick }) {
  console.log("Child rendered");
  return <button onClick={onClick}>Click Me</button>;
});
```

### When to Use (and Not Use) These Hooks

```
⚠️ DON'T optimize prematurely!

✅ USE useMemo when:
  - Filtering/sorting large arrays (1000+ items)
  - Complex calculations that are slow
  - Creating objects passed to dependency arrays

✅ USE useCallback when:
  - Passing callbacks to memoized child components (React.memo)
  - Using functions in useEffect dependency arrays

❌ DON'T use for:
  - Simple calculations (adding a few numbers)
  - Small lists (< 100 items)
  - Every function by default
  - The cost of useMemo/useCallback can be MORE than recalculating
```

---

## 🛠️ Hands-On Exercise

### Auto-Saving Notes App

```jsx
function NotesApp() {
  const [notes, setNotes] = useState(() => {
    const saved = localStorage.getItem("notes");
    return saved ? JSON.parse(saved) : [];
  });
  const [currentNote, setCurrentNote] = useState("");
  const inputRef = useRef(null);
  const saveTimerRef = useRef(null);

  // Auto-save with debounce
  useEffect(() => {
    clearTimeout(saveTimerRef.current);
    saveTimerRef.current = setTimeout(() => {
      localStorage.setItem("notes", JSON.stringify(notes));
    }, 500);

    return () => clearTimeout(saveTimerRef.current);
  }, [notes]);

  const addNote = useCallback(() => {
    if (!currentNote.trim()) return;
    setNotes(prev => [...prev, {
      id: Date.now(),
      text: currentNote,
      createdAt: new Date().toISOString()
    }]);
    setCurrentNote("");
    inputRef.current.focus();
  }, [currentNote]);

  const deleteNote = useCallback((id) => {
    setNotes(prev => prev.filter(n => n.id !== id));
  }, []);

  const sortedNotes = useMemo(() => {
    return [...notes].sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt));
  }, [notes]);

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">📝 Notes ({notes.length})</h2>
      <div className="flex gap-2 mb-4">
        <input
          ref={inputRef}
          value={currentNote}
          onChange={(e) => setCurrentNote(e.target.value)}
          onKeyDown={(e) => e.key === "Enter" && addNote()}
          placeholder="Add a note..."
          className="flex-1 p-2 border rounded"
        />
        <button onClick={addNote} className="px-4 py-2 bg-blue-500 text-white rounded">
          Add
        </button>
      </div>
      <ul className="space-y-2">
        {sortedNotes.map(note => (
          <li key={note.id} className="flex justify-between p-2 bg-gray-50 rounded">
            <span>{note.text}</span>
            <button onClick={() => deleteNote(note.id)} className="text-red-500">×</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **video player** using `useRef` to control play/pause/seek
2. Create a **character counter** textarea where `useRef` tracks if the user is actively typing
3. Build a **filterable table** with 500+ rows — use `useMemo` for the filter + sort
4. Create a **parent-child** component pair where `useCallback` prevents unnecessary child re-renders
5. Build a **form with auto-save** that saves to localStorage 1 second after the last change

---

## 💡 Pro Tips

> 🎯 **useRef for DOM:** Whenever you need to focus an input, scroll to an element, or measure dimensions — reach for `useRef`.

> ⚡ **Profile first, optimize second:** Use React DevTools Profiler to find actual bottlenecks before adding `useMemo`/`useCallback`.

> 🧠 **Mental model:** `useRef` is like a "box" that holds a mutable value. Changing `.current` doesn't trigger a re-render.

---

[← Day 3](day-03.md) | [Back to Week 10](README.md) | [Day 5 →](day-05.md)
