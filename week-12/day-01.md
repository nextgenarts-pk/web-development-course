# 📅 Week 12 — Day 1: useReducer — Complex State Logic 🔄

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand when `useReducer` is better than `useState`
- Learn the reducer pattern: state, action, dispatch
- Build reducers for complex state transitions
- Handle multiple related state values with a single reducer
- Implement undo/redo with reducer

---

## 📖 Lesson Content

### 1. When useState Isn't Enough

`useState` works great for independent pieces of state. But when state changes depend on **previous state**, involve **multiple sub-values**, or have **complex update logic**, a reducer is cleaner:

| Scenario | useState | useReducer |
|----------|----------|------------|
| Toggle boolean | ✅ | Overkill |
| Form with 2-3 fields | ✅ | Fine |
| Shopping cart (add, remove, update qty, clear) | Messy | ✅ |
| Multi-step wizard with validation | Messy | ✅ |
| State with undo/redo | Very hard | ✅ |

### 2. The Reducer Pattern

A **reducer** is a pure function that takes the current state and an action, and returns the next state:

```
(state, action) → newState
```

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    case 'DECREMENT':
      return { count: state.count - 1 };
    case 'RESET':
      return { count: 0 };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}
```

### 3. useReducer Basics

```jsx
import { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + 1 };
    case 'DECREMENT':
      return { count: state.count - 1 };
    case 'SET':
      return { count: action.payload };
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h2>Count: {state.count}</h2>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'SET', payload: 0 })}>Reset</button>
    </div>
  );
}
```

### 4. Shopping Cart Reducer

A real-world example with multiple action types:

```jsx
const cartInitialState = {
  items: [],
  total: 0,
};

function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.items.find(item => item.id === action.payload.id);

      if (existing) {
        const items = state.items.map(item =>
          item.id === action.payload.id
            ? { ...item, quantity: item.quantity + 1 }
            : item
        );
        return {
          ...state,
          items,
          total: calcTotal(items),
        };
      }

      const items = [...state.items, { ...action.payload, quantity: 1 }];
      return {
        ...state,
        items,
        total: calcTotal(items),
      };
    }

    case 'REMOVE_ITEM': {
      const items = state.items.filter(item => item.id !== action.payload);
      return {
        ...state,
        items,
        total: calcTotal(items),
      };
    }

    case 'UPDATE_QUANTITY': {
      const { id, quantity } = action.payload;
      if (quantity < 1) return state;

      const items = state.items.map(item =>
        item.id === id ? { ...item, quantity } : item
      );
      return {
        ...state,
        items,
        total: calcTotal(items),
      };
    }

    case 'CLEAR_CART':
      return cartInitialState;

    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function calcTotal(items) {
  return items.reduce((sum, item) => sum + item.price * item.quantity, 0);
}
```

### 5. Using the Cart Reducer

```jsx
function ShoppingCart() {
  const [cart, dispatch] = useReducer(cartReducer, cartInitialState);

  function addToCart(product) {
    dispatch({ type: 'ADD_ITEM', payload: product });
  }

  function removeFromCart(id) {
    dispatch({ type: 'REMOVE_ITEM', payload: id });
  }

  function updateQty(id, quantity) {
    dispatch({ type: 'UPDATE_QUANTITY', payload: { id, quantity } });
  }

  return (
    <div>
      <h2>🛒 Cart ({cart.items.length} items)</h2>

      {cart.items.map(item => (
        <div key={item.id} className="cart-item">
          <span>{item.name} — ${item.price}</span>
          <div>
            <button onClick={() => updateQty(item.id, item.quantity - 1)}>-</button>
            <span>{item.quantity}</span>
            <button onClick={() => updateQty(item.id, item.quantity + 1)}>+</button>
          </div>
          <button onClick={() => removeFromCart(item.id)}>🗑️</button>
        </div>
      ))}

      <h3>Total: ${cart.total.toFixed(2)}</h3>
      <button onClick={() => dispatch({ type: 'CLEAR_CART' })}>
        Clear Cart
      </button>
    </div>
  );
}
```

### 6. Todo App with useReducer

```jsx
const todoInitialState = {
  todos: [],
  filter: 'all', // 'all' | 'active' | 'completed'
};

function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [
          ...state.todos,
          { id: Date.now(), text: action.payload, completed: false },
        ],
      };

    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload
            ? { ...todo, completed: !todo.completed }
            : todo
        ),
      };

    case 'DELETE_TODO':
      return {
        ...state,
        todos: state.todos.filter(todo => todo.id !== action.payload),
      };

    case 'SET_FILTER':
      return { ...state, filter: action.payload };

    case 'CLEAR_COMPLETED':
      return {
        ...state,
        todos: state.todos.filter(todo => !todo.completed),
      };

    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}
```

### 7. Undo / Redo with Reducer

```jsx
function undoableReducer(reducer, initialState) {
  const undoInitial = {
    past: [],
    present: initialState,
    future: [],
  };

  return function (state, action) {
    switch (action.type) {
      case 'UNDO': {
        if (state.past.length === 0) return state;
        const previous = state.past[state.past.length - 1];
        const newPast = state.past.slice(0, -1);
        return {
          past: newPast,
          present: previous,
          future: [state.present, ...state.future],
        };
      }

      case 'REDO': {
        if (state.future.length === 0) return state;
        const next = state.future[0];
        const newFuture = state.future.slice(1);
        return {
          past: [...state.past, state.present],
          present: next,
          future: newFuture,
        };
      }

      default: {
        const newPresent = reducer(state.present, action);
        if (newPresent === state.present) return state;
        return {
          past: [...state.past, state.present],
          present: newPresent,
          future: [],
        };
      }
    }
  };
}
```

---

## 💪 Hands-On Exercise

### Build a Task Manager with useReducer

1. State: `{ tasks: [], filter: 'all', sortBy: 'date' }`
2. Actions: `ADD_TASK`, `TOGGLE_TASK`, `DELETE_TASK`, `EDIT_TASK`, `SET_FILTER`, `SET_SORT`
3. Each task: `{ id, title, priority, completed, createdAt }`
4. Filter by: all, active, completed
5. Sort by: date, priority, alphabetical
6. Display counts: total, active, completed

---

## 📝 Homework

1. Add an `UNDO` action to the task manager that reverts the last change
2. Add a `CLEAR_COMPLETED` action
3. Persist state to localStorage — initialize from localStorage if available
4. Add `SET_PRIORITY` action that changes a task's priority (low, medium, high)

---

## 💡 Pro Tips

- Reducers are **pure functions** — same inputs always produce same outputs, no side effects
- Always throw on unknown action types — catches typos immediately
- Use `action.payload` consistently for data, `action.type` for intent
- If your reducer gets big, split it into helper functions — don't nest switches

---

[← Back to Week 12](README.md) | [Day 2 →](day-02.md)
