# 📅 Day 1: useState Deep Dive — Complex State Patterns 🪝

> **Week 10 — React Hooks Deep Dive** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- React components, props, JSX (Week 9)
- JavaScript objects, arrays, spread operator

---

## 🎯 Today's Goals

- 🪝 Master the `useState` hook for all data types
- 📦 Manage objects and arrays in state
- 🔄 Understand state updates and batching
- ⬆️ Learn to lift state up between components
- 🧠 Grasp the "state as a snapshot" mental model

---

## 📘 Lesson Content

### useState Basics Review

```jsx
import { useState } from "react";

function Counter() {
  // useState returns [currentValue, setterFunction]
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <button onClick={() => setCount(count - 1)}>-1</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### State with Different Data Types

```jsx
function StateExamples() {
  // String
  const [name, setName] = useState("");

  // Number
  const [age, setAge] = useState(0);

  // Boolean
  const [isDarkMode, setIsDarkMode] = useState(false);

  // Array
  const [items, setItems] = useState([]);

  // Object
  const [user, setUser] = useState({ name: "", email: "" });

  return <div>...</div>;
}
```

### Functional Updates (Updater Function)

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  // ❌ This might not work as expected when called multiple times rapidly
  const incrementBad = () => {
    setCount(count + 1);
    setCount(count + 1); // Still uses the same "count" snapshot!
    // Result: only increments by 1
  };

  // ✅ Use the updater function when new state depends on old state
  const incrementGood = () => {
    setCount(prev => prev + 1);
    setCount(prev => prev + 1); // Uses the latest pending value
    // Result: increments by 2
  };

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={incrementGood}>+2</button>
    </div>
  );
}
```

### State with Objects

```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: "",
    email: "",
    age: 0,
    address: {
      city: "",
      country: ""
    }
  });

  // ❌ NEVER mutate state directly
  const handleBad = () => {
    user.name = "Ahmed"; // ❌ Mutation! React won't re-render
    setUser(user);        // ❌ Same reference, no update
  };

  // ✅ Create a NEW object with spread operator
  const handleNameChange = (e) => {
    setUser({ ...user, name: e.target.value });
  };

  // ✅ Updating nested objects
  const handleCityChange = (e) => {
    setUser({
      ...user,
      address: {
        ...user.address,
        city: e.target.value
      }
    });
  };

  return (
    <form>
      <input
        value={user.name}
        onChange={handleNameChange}
        placeholder="Name"
      />
      <input
        value={user.email}
        onChange={(e) => setUser({ ...user, email: e.target.value })}
        placeholder="Email"
      />
      <input
        value={user.address.city}
        onChange={handleCityChange}
        placeholder="City"
      />
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </form>
  );
}
```

### State with Arrays

```jsx
function TodoApp() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn React", completed: false },
    { id: 2, text: "Build projects", completed: false }
  ]);

  // Add item
  const addTodo = (text) => {
    setTodos([...todos, { id: Date.now(), text, completed: false }]);
  };

  // Remove item
  const removeTodo = (id) => {
    setTodos(todos.filter(todo => todo.id !== id));
  };

  // Update item
  const toggleTodo = (id) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo
    ));
  };

  // Replace item
  const editTodo = (id, newText) => {
    setTodos(todos.map(todo =>
      todo.id === id ? { ...todo, text: newText } : todo
    ));
  };

  // Insert at position
  const insertAt = (index, newTodo) => {
    setTodos([
      ...todos.slice(0, index),
      newTodo,
      ...todos.slice(index)
    ]);
  };

  return <div>...</div>;
}
```

### Lifting State Up

```jsx
// When two sibling components need to share state,
// lift the state up to their common parent.

function App() {
  // State lives in the parent
  const [temperature, setTemperature] = useState(20);

  return (
    <div>
      <TemperatureInput
        label="Celsius"
        value={temperature}
        onChange={setTemperature}
      />
      <TemperatureDisplay celsius={temperature} />
    </div>
  );
}

function TemperatureInput({ label, value, onChange }) {
  return (
    <label>
      {label}:
      <input
        type="number"
        value={value}
        onChange={(e) => onChange(Number(e.target.value))}
      />
    </label>
  );
}

function TemperatureDisplay({ celsius }) {
  const fahrenheit = (celsius * 9 / 5) + 32;
  return (
    <div>
      <p>{celsius}°C = {fahrenheit.toFixed(1)}°F</p>
      <p>{celsius > 30 ? "🔥 Hot!" : celsius < 10 ? "🥶 Cold!" : "😊 Pleasant"}</p>
    </div>
  );
}
```

### Multiple Pieces of State

```jsx
function ShoppingCart() {
  const [items, setItems] = useState([]);
  const [coupon, setCoupon] = useState("");
  const [isCheckingOut, setIsCheckingOut] = useState(false);

  // Derived state — DON'T store computed values in state
  const totalPrice = items.reduce((sum, item) => sum + item.price * item.qty, 0);
  const itemCount = items.reduce((sum, item) => sum + item.qty, 0);

  // ❌ Don't do this — totalPrice can be computed from items
  // const [totalPrice, setTotalPrice] = useState(0);

  const addItem = (product) => {
    setItems(prev => {
      const existing = prev.find(item => item.id === product.id);
      if (existing) {
        return prev.map(item =>
          item.id === product.id ? { ...item, qty: item.qty + 1 } : item
        );
      }
      return [...prev, { ...product, qty: 1 }];
    });
  };

  return (
    <div>
      <h2>🛒 Cart ({itemCount} items)</h2>
      <p>Total: PKR {totalPrice.toLocaleString()}</p>
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Interactive Counter Dashboard

```jsx
function CounterDashboard() {
  const [counters, setCounters] = useState([
    { id: 1, name: "Visitors", value: 0 },
    { id: 2, name: "Sign-ups", value: 0 },
    { id: 3, name: "Sales", value: 0 }
  ]);

  const updateCounter = (id, delta) => {
    setCounters(prev => prev.map(c =>
      c.id === id ? { ...c, value: Math.max(0, c.value + delta) } : c
    ));
  };

  const resetAll = () => {
    setCounters(prev => prev.map(c => ({ ...c, value: 0 })));
  };

  const total = counters.reduce((sum, c) => sum + c.value, 0);

  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">📊 Dashboard (Total: {total})</h1>
      <div className="grid grid-cols-3 gap-4">
        {counters.map(counter => (
          <div key={counter.id} className="bg-white p-4 rounded-lg shadow text-center">
            <h3 className="text-gray-500">{counter.name}</h3>
            <p className="text-3xl font-bold my-2">{counter.value}</p>
            <div className="flex gap-2 justify-center">
              <button onClick={() => updateCounter(counter.id, -1)}>-</button>
              <button onClick={() => updateCounter(counter.id, 1)}>+</button>
            </div>
          </div>
        ))}
      </div>
      <button onClick={resetAll} className="mt-4">Reset All</button>
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **color picker** with useState — sliders for R, G, B values showing the resulting color
2. Create a **shopping list** — add items with name & quantity, edit inline, remove, mark as purchased
3. Build a **multi-step form** (personal info → address → review) with state for all fields
4. Create a **scoreboard** with player names and scores, sort by score, highlight winner
5. Build an **accordion** component where only one section can be open at a time

---

## 💡 Pro Tips

> 🧊 **State is immutable:** Never mutate state directly. Always create new objects/arrays with spread/map/filter.

> 📊 **Derived state:** If a value can be computed from existing state, DON'T store it in a separate `useState`. Just compute it during render.

> ⬆️ **Lift state up:** When siblings need shared data, move the state to their closest common parent and pass it down via props.

---

[Back to Week 10](README.md) | [Day 2 →](day-02.md)
