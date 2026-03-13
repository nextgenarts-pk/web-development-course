# 📅 Week 12 — Day 2: Context + Reducer — Scalable State Management 🏗️

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Combine Context and useReducer for app-wide state
- Build a store pattern (like a lightweight Redux)
- Separate state and dispatch contexts for performance
- Create action creators for cleaner dispatch calls
- Build a complete cart store for an e-commerce app

---

## 📖 Lesson Content

### 1. The Problem: Prop Drilling + Complex State

We learned `useContext` for sharing state and `useReducer` for complex logic. Separately, each solves half the problem:

| Tool | Shares state? | Handles complex logic? |
|------|:---:|:---:|
| `useContext` | ✅ | ❌ |
| `useReducer` | ❌ | ✅ |
| **Context + Reducer** | ✅ | ✅ |

### 2. The Pattern

```
Create reducer → Create context → Build provider (useReducer inside) → Export custom hooks
```

### 3. Building a Cart Store

#### Step 1: Define the Reducer

```jsx
// store/cartReducer.js
export const cartInitialState = {
  items: [],
  total: 0,
  itemCount: 0,
};

export function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_TO_CART': {
      const existing = state.items.find(i => i.id === action.payload.id);
      let items;

      if (existing) {
        items = state.items.map(i =>
          i.id === action.payload.id
            ? { ...i, quantity: i.quantity + 1 }
            : i
        );
      } else {
        items = [...state.items, { ...action.payload, quantity: 1 }];
      }

      return {
        items,
        total: calcTotal(items),
        itemCount: calcCount(items),
      };
    }

    case 'REMOVE_FROM_CART': {
      const items = state.items.filter(i => i.id !== action.payload);
      return {
        items,
        total: calcTotal(items),
        itemCount: calcCount(items),
      };
    }

    case 'UPDATE_QUANTITY': {
      const { id, quantity } = action.payload;
      if (quantity < 1) {
        // Remove if quantity drops to 0
        const items = state.items.filter(i => i.id !== id);
        return { items, total: calcTotal(items), itemCount: calcCount(items) };
      }

      const items = state.items.map(i =>
        i.id === id ? { ...i, quantity } : i
      );
      return { items, total: calcTotal(items), itemCount: calcCount(items) };
    }

    case 'CLEAR_CART':
      return cartInitialState;

    default:
      throw new Error(`Cart: unknown action "${action.type}"`);
  }
}

function calcTotal(items) {
  return items.reduce((sum, i) => sum + i.price * i.quantity, 0);
}

function calcCount(items) {
  return items.reduce((sum, i) => sum + i.quantity, 0);
}
```

#### Step 2: Create the Context & Provider

```jsx
// store/CartContext.jsx
import { createContext, useContext, useReducer } from 'react';
import { cartReducer, cartInitialState } from './cartReducer';

const CartStateContext = createContext();
const CartDispatchContext = createContext();

export function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, cartInitialState);

  return (
    <CartStateContext.Provider value={state}>
      <CartDispatchContext.Provider value={dispatch}>
        {children}
      </CartDispatchContext.Provider>
    </CartStateContext.Provider>
  );
}

export function useCartState() {
  const ctx = useContext(CartStateContext);
  if (ctx === undefined) {
    throw new Error('useCartState must be used within CartProvider');
  }
  return ctx;
}

export function useCartDispatch() {
  const ctx = useContext(CartDispatchContext);
  if (ctx === undefined) {
    throw new Error('useCartDispatch must be used within CartProvider');
  }
  return ctx;
}
```

> **Why two contexts?** Components that only read state (`Navbar` badge) won't re-render when dispatch changes, and components that only dispatch (`AddToCartButton`) won't re-render when state changes.

#### Step 3: Action Creators

Keep dispatch calls clean and typo-free:

```jsx
// store/cartActions.js
export const addToCart = (product) => ({
  type: 'ADD_TO_CART',
  payload: product,
});

export const removeFromCart = (id) => ({
  type: 'REMOVE_FROM_CART',
  payload: id,
});

export const updateQuantity = (id, quantity) => ({
  type: 'UPDATE_QUANTITY',
  payload: { id, quantity },
});

export const clearCart = () => ({
  type: 'CLEAR_CART',
});
```

#### Step 4: Wire Up the Provider

```jsx
// main.jsx
import { BrowserRouter } from 'react-router-dom';
import { CartProvider } from './store/CartContext';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <BrowserRouter>
    <CartProvider>
      <App />
    </CartProvider>
  </BrowserRouter>
);
```

### 4. Using the Store in Components

```jsx
// components/ProductCard.jsx
import { useCartDispatch } from '../store/CartContext';
import { addToCart } from '../store/cartActions';

function ProductCard({ product }) {
  const dispatch = useCartDispatch();

  return (
    <div className="product-card">
      <img src={product.image} alt={product.name} />
      <h3>{product.name}</h3>
      <p className="price">${product.price.toFixed(2)}</p>
      <button onClick={() => dispatch(addToCart(product))}>
        🛒 Add to Cart
      </button>
    </div>
  );
}
```

```jsx
// components/CartBadge.jsx
import { useCartState } from '../store/CartContext';

function CartBadge() {
  const { itemCount } = useCartState();

  return (
    <span className="cart-badge">
      🛒 {itemCount > 0 && <span className="badge">{itemCount}</span>}
    </span>
  );
}
```

```jsx
// pages/CartPage.jsx
import { useCartState, useCartDispatch } from '../store/CartContext';
import { removeFromCart, updateQuantity, clearCart } from '../store/cartActions';

function CartPage() {
  const { items, total } = useCartState();
  const dispatch = useCartDispatch();

  if (items.length === 0) {
    return <p>Your cart is empty.</p>;
  }

  return (
    <div className="cart-page">
      <h1>🛒 Shopping Cart</h1>

      {items.map(item => (
        <div key={item.id} className="cart-item">
          <div>
            <h3>{item.name}</h3>
            <p>${item.price.toFixed(2)}</p>
          </div>

          <div className="qty-controls">
            <button onClick={() => dispatch(updateQuantity(item.id, item.quantity - 1))}>
              −
            </button>
            <span>{item.quantity}</span>
            <button onClick={() => dispatch(updateQuantity(item.id, item.quantity + 1))}>
              +
            </button>
          </div>

          <p className="item-total">${(item.price * item.quantity).toFixed(2)}</p>

          <button
            className="btn-remove"
            onClick={() => dispatch(removeFromCart(item.id))}
          >
            🗑️
          </button>
        </div>
      ))}

      <div className="cart-summary">
        <h2>Total: ${total.toFixed(2)}</h2>
        <button className="btn btn-primary">Checkout</button>
        <button className="btn" onClick={() => dispatch(clearCart())}>
          Clear Cart
        </button>
      </div>
    </div>
  );
}

export default CartPage;
```

### 5. Multiple Stores

You can have multiple Context + Reducer pairs:

```jsx
// main.jsx
<BrowserRouter>
  <AuthProvider>
    <ThemeProvider>
      <CartProvider>
        <App />
      </CartProvider>
    </ThemeProvider>
  </AuthProvider>
</BrowserRouter>
```

Each store manages its own domain: auth, theme, cart.

### 6. Persisting Reducer State

```jsx
export function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, cartInitialState, () => {
    const saved = localStorage.getItem('cart');
    return saved ? JSON.parse(saved) : cartInitialState;
  });

  useEffect(() => {
    localStorage.setItem('cart', JSON.stringify(state));
  }, [state]);

  return (
    <CartStateContext.Provider value={state}>
      <CartDispatchContext.Provider value={dispatch}>
        {children}
      </CartDispatchContext.Provider>
    </CartStateContext.Provider>
  );
}
```

---

## 💪 Hands-On Exercise

### Build a Notes Store (Context + Reducer)

1. State: `{ notes: [], selectedId: null, filter: 'all' }`
2. Actions: `ADD_NOTE`, `DELETE_NOTE`, `UPDATE_NOTE`, `SELECT_NOTE`, `SET_FILTER`
3. Create `NotesProvider`, `useNotesState`, `useNotesDispatch`
4. Create action creator functions
5. Build a notes list + editor UI that connects to the store
6. Persist notes to localStorage

---

## 📝 Homework

1. Add a `TOGGLE_FAVORITE` action to the notes store
2. Add a favorited notes filter
3. Create a `useNotes()` hook that combines state + dispatch + action creators into one convenient API
4. Build a "Recently Deleted" feature with a separate reducer slice

---

## 💡 Pro Tips

- **Split state & dispatch contexts** — prevents unnecessary re-renders
- **Action creators** prevent string typos and centralize payload shapes
- The Context + Reducer pattern is ~80% of what Redux does — without adding a dependency
- Use the third argument of `useReducer` (initializer function) for lazy initialization from localStorage

---

[← Day 1](day-01.md) | [Back to Week 12](README.md) | [Day 3 →](day-03.md)
