# 📅 Week 12 — Day 6: Project Day — E-Commerce Product Catalog 🛒

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Build a complete e-commerce product catalog
- Use Context + Reducer for shopping cart state management
- Implement product listing with filters and sorting
- Build cart functionality with add, remove, and quantity controls
- Apply performance optimizations (React.memo, lazy loading)

---

## 🏗️ Project Overview

### E-Commerce Product Catalog

A fully-featured product catalog with browsing, filtering, cart management, and checkout simulation — demonstrating everything learned in Phase 3.

### Features

| Feature | Details |
|---------|---------|
| Product listing | Grid layout with cards, images, prices |
| Category filter | Filter by category (top bar or sidebar) |
| Search | Filter products by name |
| Sorting | By price (low/high), name, rating |
| Shopping cart | Add, remove, update quantity |
| Cart badge | Item count in navbar |
| Cart page | Full cart with totals |
| Theme toggle | Dark/light mode |
| Responsive | Mobile-first grid layout |
| Lazy loading | Cart page loaded on demand |

---

## 📁 Project Structure

```
ecommerce-app/
├── src/
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── ProductCard.jsx
│   │   ├── ProductGrid.jsx
│   │   ├── FilterBar.jsx
│   │   ├── SearchBar.jsx
│   │   ├── SortSelect.jsx
│   │   ├── CartItem.jsx
│   │   ├── CartSummary.jsx
│   │   ├── ThemeToggle.jsx
│   │   └── ErrorBoundary.jsx
│   ├── context/
│   │   └── ThemeContext.jsx
│   ├── store/
│   │   ├── CartContext.jsx
│   │   ├── cartReducer.js
│   │   └── cartActions.js
│   ├── data/
│   │   └── products.js
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── ProductDetail.jsx
│   │   └── Cart.jsx
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
└── package.json
```

---

## 💻 Full Implementation

### 1. Product Data

```jsx
// data/products.js
export const products = [
  {
    id: 1,
    name: 'Wireless Headphones',
    price: 79.99,
    category: 'electronics',
    rating: 4.5,
    image: 'https://placehold.co/300x300/4F46E5/white?text=Headphones',
    description: 'Premium wireless headphones with noise cancellation and 30-hour battery life.',
  },
  {
    id: 2,
    name: 'Running Shoes',
    price: 129.99,
    category: 'clothing',
    rating: 4.8,
    image: 'https://placehold.co/300x300/059669/white?text=Shoes',
    description: 'Lightweight running shoes with responsive cushioning for everyday runs.',
  },
  {
    id: 3,
    name: 'Mechanical Keyboard',
    price: 149.99,
    category: 'electronics',
    rating: 4.7,
    image: 'https://placehold.co/300x300/7C3AED/white?text=Keyboard',
    description: 'RGB mechanical keyboard with hot-swappable switches and aluminum frame.',
  },
  {
    id: 4,
    name: 'Backpack',
    price: 59.99,
    category: 'accessories',
    rating: 4.3,
    image: 'https://placehold.co/300x300/DC2626/white?text=Backpack',
    description: 'Water-resistant backpack with laptop compartment, fits up to 15.6 inch.',
  },
  {
    id: 5,
    name: 'Smart Watch',
    price: 199.99,
    category: 'electronics',
    rating: 4.6,
    image: 'https://placehold.co/300x300/0891B2/white?text=Watch',
    description: 'Fitness tracking smartwatch with heart rate monitor and GPS.',
  },
  {
    id: 6,
    name: 'Denim Jacket',
    price: 89.99,
    category: 'clothing',
    rating: 4.4,
    image: 'https://placehold.co/300x300/B45309/white?text=Jacket',
    description: 'Classic denim jacket with modern fit, available in multiple washes.',
  },
  {
    id: 7,
    name: 'Portable Charger',
    price: 34.99,
    category: 'electronics',
    rating: 4.2,
    image: 'https://placehold.co/300x300/6366F1/white?text=Charger',
    description: '20000mAh portable charger with fast charging support for all devices.',
  },
  {
    id: 8,
    name: 'Sunglasses',
    price: 44.99,
    category: 'accessories',
    rating: 4.1,
    image: 'https://placehold.co/300x300/E11D48/white?text=Shades',
    description: 'Polarized sunglasses with UV400 protection and lightweight frame.',
  },
  {
    id: 9,
    name: 'Yoga Mat',
    price: 29.99,
    category: 'fitness',
    rating: 4.5,
    image: 'https://placehold.co/300x300/16A34A/white?text=Yoga+Mat',
    description: 'Non-slip yoga mat with alignment marks, 6mm thick for joint comfort.',
  },
  {
    id: 10,
    name: 'Water Bottle',
    price: 24.99,
    category: 'fitness',
    rating: 4.7,
    image: 'https://placehold.co/300x300/0284C7/white?text=Bottle',
    description: 'Insulated stainless steel water bottle, keeps drinks cold for 24 hours.',
  },
  {
    id: 11,
    name: 'Desk Lamp',
    price: 39.99,
    category: 'accessories',
    rating: 4.3,
    image: 'https://placehold.co/300x300/9333EA/white?text=Lamp',
    description: 'LED desk lamp with adjustable brightness and color temperature.',
  },
  {
    id: 12,
    name: 'Resistance Bands',
    price: 19.99,
    category: 'fitness',
    rating: 4.6,
    image: 'https://placehold.co/300x300/EA580C/white?text=Bands',
    description: 'Set of 5 resistance bands with varying levels for home workouts.',
  },
];

export const categories = ['all', 'electronics', 'clothing', 'accessories', 'fitness'];
```

### 2. Cart Store (Context + Reducer)

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

      return calculate(items);
    }

    case 'REMOVE_FROM_CART': {
      const items = state.items.filter(i => i.id !== action.payload);
      return calculate(items);
    }

    case 'UPDATE_QUANTITY': {
      const { id, quantity } = action.payload;
      if (quantity < 1) {
        const items = state.items.filter(i => i.id !== id);
        return calculate(items);
      }
      const items = state.items.map(i =>
        i.id === id ? { ...i, quantity } : i
      );
      return calculate(items);
    }

    case 'CLEAR_CART':
      return cartInitialState;

    default:
      throw new Error(`Unknown cart action: ${action.type}`);
  }
}

function calculate(items) {
  return {
    items,
    total: items.reduce((sum, i) => sum + i.price * i.quantity, 0),
    itemCount: items.reduce((sum, i) => sum + i.quantity, 0),
  };
}
```

```jsx
// store/cartActions.js
export const addToCart = (product) => ({ type: 'ADD_TO_CART', payload: product });
export const removeFromCart = (id) => ({ type: 'REMOVE_FROM_CART', payload: id });
export const updateQuantity = (id, qty) => ({ type: 'UPDATE_QUANTITY', payload: { id, quantity: qty } });
export const clearCart = () => ({ type: 'CLEAR_CART' });
```

```jsx
// store/CartContext.jsx
import { createContext, useContext, useReducer, useEffect } from 'react';
import { cartReducer, cartInitialState } from './cartReducer';

const CartStateCtx = createContext();
const CartDispatchCtx = createContext();

export function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, cartInitialState, () => {
    const saved = localStorage.getItem('ecommerce-cart');
    return saved ? JSON.parse(saved) : cartInitialState;
  });

  useEffect(() => {
    localStorage.setItem('ecommerce-cart', JSON.stringify(state));
  }, [state]);

  return (
    <CartStateCtx.Provider value={state}>
      <CartDispatchCtx.Provider value={dispatch}>
        {children}
      </CartDispatchCtx.Provider>
    </CartStateCtx.Provider>
  );
}

export function useCartState() {
  const ctx = useContext(CartStateCtx);
  if (!ctx) throw new Error('useCartState must be within CartProvider');
  return ctx;
}

export function useCartDispatch() {
  const ctx = useContext(CartDispatchCtx);
  if (!ctx) throw new Error('useCartDispatch must be within CartProvider');
  return ctx;
}
```

### 3. Theme Context

```jsx
// context/ThemeContext.jsx
import { createContext, useContext, useState, useEffect } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState(() =>
    localStorage.getItem('ecommerce-theme') || 'light'
  );

  useEffect(() => {
    localStorage.setItem('ecommerce-theme', theme);
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  const toggleTheme = () =>
    setTheme(prev => (prev === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be within ThemeProvider');
  return ctx;
}
```

### 4. Entry Point

```jsx
// main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import { ThemeProvider } from './context/ThemeContext';
import { CartProvider } from './store/CartContext';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <ThemeProvider>
        <CartProvider>
          <App />
        </CartProvider>
      </ThemeProvider>
    </BrowserRouter>
  </React.StrictMode>
);
```

### 5. App Routes (with lazy loading)

```jsx
// App.jsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';
import Navbar from './components/Navbar';
import ErrorBoundary from './components/ErrorBoundary';
import Home from './pages/Home';

const ProductDetail = lazy(() => import('./pages/ProductDetail'));
const Cart = lazy(() => import('./pages/Cart'));

function App() {
  return (
    <div className="app">
      <Navbar />
      <main className="container">
        <ErrorBoundary>
          <Suspense fallback={<div className="loading">Loading...</div>}>
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/product/:productId" element={<ProductDetail />} />
              <Route path="/cart" element={<Cart />} />
              <Route path="*" element={<h1>404 — Not Found</h1>} />
            </Routes>
          </Suspense>
        </ErrorBoundary>
      </main>
    </div>
  );
}

export default App;
```

### 6. Components

```jsx
// components/Navbar.jsx
import { Link, NavLink } from 'react-router-dom';
import { useCartState } from '../store/CartContext';
import ThemeToggle from './ThemeToggle';

function Navbar() {
  const { itemCount } = useCartState();

  return (
    <nav className="navbar">
      <Link to="/" className="logo">🛍️ ShopApp</Link>

      <div className="nav-links">
        <NavLink to="/" end className={({ isActive }) => isActive ? 'active' : ''}>
          Products
        </NavLink>
        <NavLink to="/cart" className={({ isActive }) => isActive ? 'active' : ''}>
          Cart {itemCount > 0 && <span className="badge">{itemCount}</span>}
        </NavLink>
      </div>

      <ThemeToggle />
    </nav>
  );
}

export default Navbar;
```

```jsx
// components/ThemeToggle.jsx
import { useTheme } from '../context/ThemeContext';

function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();
  return (
    <button onClick={toggleTheme} className="theme-toggle">
      {theme === 'light' ? '🌙' : '☀️'}
    </button>
  );
}

export default ThemeToggle;
```

```jsx
// components/ProductCard.jsx
import { memo, useCallback } from 'react';
import { Link } from 'react-router-dom';
import { useCartDispatch } from '../store/CartContext';
import { addToCart } from '../store/cartActions';

const ProductCard = memo(function ProductCard({ product }) {
  const dispatch = useCartDispatch();

  const handleAdd = useCallback(() => {
    dispatch(addToCart(product));
  }, [dispatch, product]);

  return (
    <div className="product-card">
      <Link to={`/product/${product.id}`}>
        <img src={product.image} alt={product.name} />
      </Link>
      <div className="product-info">
        <span className="category">{product.category}</span>
        <h3>
          <Link to={`/product/${product.id}`}>{product.name}</Link>
        </h3>
        <div className="product-meta">
          <span className="price">${product.price.toFixed(2)}</span>
          <span className="rating">⭐ {product.rating}</span>
        </div>
        <button className="btn btn-primary" onClick={handleAdd}>
          🛒 Add to Cart
        </button>
      </div>
    </div>
  );
});

export default ProductCard;
```

```jsx
// components/FilterBar.jsx
function FilterBar({ categories, activeCategory, onCategoryChange }) {
  return (
    <div className="filter-bar">
      {categories.map(cat => (
        <button
          key={cat}
          className={`filter-btn ${cat === activeCategory ? 'active' : ''}`}
          onClick={() => onCategoryChange(cat)}
        >
          {cat.charAt(0).toUpperCase() + cat.slice(1)}
        </button>
      ))}
    </div>
  );
}

export default FilterBar;
```

```jsx
// components/SearchBar.jsx
function SearchBar({ value, onChange }) {
  return (
    <input
      type="text"
      className="search-input"
      placeholder="Search products..."
      value={value}
      onChange={(e) => onChange(e.target.value)}
    />
  );
}

export default SearchBar;
```

```jsx
// components/SortSelect.jsx
function SortSelect({ value, onChange }) {
  return (
    <select className="sort-select" value={value} onChange={e => onChange(e.target.value)}>
      <option value="default">Sort by</option>
      <option value="price-asc">Price: Low → High</option>
      <option value="price-desc">Price: High → Low</option>
      <option value="rating">Rating</option>
      <option value="name">Name (A-Z)</option>
    </select>
  );
}

export default SortSelect;
```

```jsx
// components/CartItem.jsx
import { useCartDispatch } from '../store/CartContext';
import { removeFromCart, updateQuantity } from '../store/cartActions';

function CartItem({ item }) {
  const dispatch = useCartDispatch();

  return (
    <div className="cart-item">
      <img src={item.image} alt={item.name} className="cart-item-img" />
      <div className="cart-item-info">
        <h3>{item.name}</h3>
        <p className="price">${item.price.toFixed(2)}</p>
      </div>

      <div className="qty-controls">
        <button onClick={() => dispatch(updateQuantity(item.id, item.quantity - 1))}>−</button>
        <span>{item.quantity}</span>
        <button onClick={() => dispatch(updateQuantity(item.id, item.quantity + 1))}>+</button>
      </div>

      <p className="item-total">${(item.price * item.quantity).toFixed(2)}</p>

      <button className="btn-remove" onClick={() => dispatch(removeFromCart(item.id))}>
        🗑️
      </button>
    </div>
  );
}

export default CartItem;
```

```jsx
// components/ErrorBoundary.jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="error-boundary">
          <h2>Something went wrong 😕</h2>
          <button onClick={() => this.setState({ hasError: false })}>Try Again</button>
        </div>
      );
    }
    return this.props.children;
  }
}

export default ErrorBoundary;
```

### 7. Pages

```jsx
// pages/Home.jsx
import { useState, useMemo } from 'react';
import { products, categories } from '../data/products';
import ProductCard from '../components/ProductCard';
import FilterBar from '../components/FilterBar';
import SearchBar from '../components/SearchBar';
import SortSelect from '../components/SortSelect';

function Home() {
  const [category, setCategory] = useState('all');
  const [search, setSearch] = useState('');
  const [sortBy, setSortBy] = useState('default');

  const filteredProducts = useMemo(() => {
    let result = products;

    if (category !== 'all') {
      result = result.filter(p => p.category === category);
    }

    if (search) {
      result = result.filter(p =>
        p.name.toLowerCase().includes(search.toLowerCase())
      );
    }

    switch (sortBy) {
      case 'price-asc':
        result = [...result].sort((a, b) => a.price - b.price);
        break;
      case 'price-desc':
        result = [...result].sort((a, b) => b.price - a.price);
        break;
      case 'rating':
        result = [...result].sort((a, b) => b.rating - a.rating);
        break;
      case 'name':
        result = [...result].sort((a, b) => a.name.localeCompare(b.name));
        break;
    }

    return result;
  }, [category, search, sortBy]);

  return (
    <div className="home">
      <h1>🛍️ Products</h1>

      <div className="controls">
        <SearchBar value={search} onChange={setSearch} />
        <SortSelect value={sortBy} onChange={setSortBy} />
      </div>

      <FilterBar
        categories={categories}
        activeCategory={category}
        onCategoryChange={setCategory}
      />

      {filteredProducts.length === 0 ? (
        <p className="empty">No products found.</p>
      ) : (
        <div className="product-grid">
          {filteredProducts.map(product => (
            <ProductCard key={product.id} product={product} />
          ))}
        </div>
      )}
    </div>
  );
}

export default Home;
```

```jsx
// pages/ProductDetail.jsx
import { useParams, Link, useNavigate } from 'react-router-dom';
import { products } from '../data/products';
import { useCartDispatch } from '../store/CartContext';
import { addToCart } from '../store/cartActions';

function ProductDetail() {
  const { productId } = useParams();
  const navigate = useNavigate();
  const dispatch = useCartDispatch();

  const product = products.find(p => p.id === Number(productId));

  if (!product) {
    return (
      <div>
        <h2>Product not found</h2>
        <Link to="/">Browse products</Link>
      </div>
    );
  }

  function handleAddToCart() {
    dispatch(addToCart(product));
    navigate('/cart');
  }

  return (
    <div className="product-detail">
      <button onClick={() => navigate(-1)} className="btn btn-back">← Back</button>

      <div className="product-detail-grid">
        <img src={product.image} alt={product.name} className="product-detail-img" />

        <div className="product-detail-info">
          <span className="category">{product.category}</span>
          <h1>{product.name}</h1>
          <p className="rating">⭐ {product.rating} / 5</p>
          <p className="description">{product.description}</p>
          <p className="price">${product.price.toFixed(2)}</p>

          <button className="btn btn-primary btn-lg" onClick={handleAddToCart}>
            🛒 Add to Cart
          </button>
        </div>
      </div>
    </div>
  );
}

export default ProductDetail;
```

```jsx
// pages/Cart.jsx
import { Link } from 'react-router-dom';
import { useCartState, useCartDispatch } from '../store/CartContext';
import { clearCart } from '../store/cartActions';
import CartItem from '../components/CartItem';

function Cart() {
  const { items, total } = useCartState();
  const dispatch = useCartDispatch();

  if (items.length === 0) {
    return (
      <div className="cart-empty">
        <h1>🛒 Your Cart is Empty</h1>
        <p>Looks like you haven't added anything yet.</p>
        <Link to="/" className="btn btn-primary">Browse Products</Link>
      </div>
    );
  }

  return (
    <div className="cart-page">
      <h1>🛒 Shopping Cart</h1>

      <div className="cart-items">
        {items.map(item => (
          <CartItem key={item.id} item={item} />
        ))}
      </div>

      <div className="cart-summary">
        <div className="summary-row">
          <span>Subtotal:</span>
          <span>${total.toFixed(2)}</span>
        </div>
        <div className="summary-row">
          <span>Shipping:</span>
          <span>{total > 50 ? 'Free' : '$9.99'}</span>
        </div>
        <div className="summary-row total">
          <span>Total:</span>
          <span>${(total > 50 ? total : total + 9.99).toFixed(2)}</span>
        </div>

        <button className="btn btn-primary btn-lg btn-block">
          Checkout
        </button>
        <button
          className="btn btn-block"
          onClick={() => dispatch(clearCart())}
        >
          Clear Cart
        </button>
      </div>
    </div>
  );
}

export default Cart;
```

### 8. Styles

```css
/* index.css */
:root {
  --bg: #f9fafb; --bg-card: #ffffff; --text: #111827; --text-secondary: #6b7280;
  --primary: #4f46e5; --primary-hover: #4338ca; --border: #e5e7eb;
  --radius: 10px; --shadow: 0 1px 3px rgba(0,0,0,0.08);
}
[data-theme="dark"] {
  --bg: #0f172a; --bg-card: #1e293b; --text: #f1f5f9; --text-secondary: #94a3b8;
  --primary: #818cf8; --primary-hover: #6366f1; --border: #334155;
  --shadow: 0 1px 3px rgba(0,0,0,0.3);
}

* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: 'Inter', system-ui, sans-serif; background: var(--bg); color: var(--text); }
.container { max-width: 1200px; margin: 0 auto; padding: 2rem 1rem; }

.navbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 1rem 2rem; background: var(--bg-card); border-bottom: 1px solid var(--border);
}
.logo { font-size: 1.3rem; font-weight: 700; text-decoration: none; color: var(--text); }
.nav-links { display: flex; gap: 1.5rem; align-items: center; }
.nav-links a { text-decoration: none; color: var(--text-secondary); font-weight: 500; }
.nav-links a.active { color: var(--primary); }
.badge {
  background: var(--primary); color: white; font-size: 0.75rem;
  padding: 0.1rem 0.5rem; border-radius: 999px; margin-left: 0.25rem;
}

.controls { display: flex; gap: 1rem; margin-bottom: 1rem; flex-wrap: wrap; }
.search-input {
  flex: 1; min-width: 200px; padding: 0.6rem 1rem; border-radius: var(--radius);
  border: 1px solid var(--border); background: var(--bg-card); color: var(--text);
}
.sort-select {
  padding: 0.6rem 1rem; border-radius: var(--radius); border: 1px solid var(--border);
  background: var(--bg-card); color: var(--text);
}

.filter-bar { display: flex; gap: 0.5rem; margin-bottom: 1.5rem; flex-wrap: wrap; }
.filter-btn {
  padding: 0.4rem 1rem; border-radius: 999px; border: 1px solid var(--border);
  background: var(--bg-card); color: var(--text-secondary); cursor: pointer;
}
.filter-btn.active { background: var(--primary); color: white; border-color: var(--primary); }

.product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(260px, 1fr)); gap: 1.5rem; }
.product-card {
  background: var(--bg-card); border: 1px solid var(--border); border-radius: var(--radius);
  overflow: hidden; box-shadow: var(--shadow); transition: transform 0.2s;
}
.product-card:hover { transform: translateY(-4px); }
.product-card img { width: 100%; height: 200px; object-fit: cover; }
.product-info { padding: 1rem; }
.product-info h3 a { text-decoration: none; color: var(--text); }
.product-meta { display: flex; justify-content: space-between; margin: 0.5rem 0; }
.price { font-size: 1.1rem; font-weight: 700; color: var(--primary); }
.category {
  font-size: 0.75rem; text-transform: uppercase; color: var(--text-secondary);
  letter-spacing: 0.05em;
}

.btn {
  padding: 0.6rem 1.25rem; border-radius: var(--radius); border: 1px solid var(--border);
  cursor: pointer; font-weight: 500; background: var(--bg-card); color: var(--text);
}
.btn-primary { background: var(--primary); color: white; border: none; }
.btn-primary:hover { background: var(--primary-hover); }
.btn-lg { padding: 0.8rem 2rem; font-size: 1.05rem; }
.btn-block { width: 100%; margin-top: 0.5rem; }
.theme-toggle { background: none; border: none; font-size: 1.3rem; cursor: pointer; }

.cart-item {
  display: flex; align-items: center; gap: 1rem; padding: 1rem;
  background: var(--bg-card); border: 1px solid var(--border); border-radius: var(--radius);
  margin-bottom: 1rem;
}
.cart-item-img { width: 80px; height: 80px; object-fit: cover; border-radius: 6px; }
.cart-item-info { flex: 1; }
.qty-controls { display: flex; align-items: center; gap: 0.5rem; }
.qty-controls button {
  width: 30px; height: 30px; border-radius: 50%; border: 1px solid var(--border);
  background: var(--bg-card); cursor: pointer; color: var(--text);
}
.btn-remove { background: none; border: none; font-size: 1.2rem; cursor: pointer; }

.cart-summary {
  background: var(--bg-card); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 1.5rem; margin-top: 2rem;
}
.summary-row { display: flex; justify-content: space-between; padding: 0.5rem 0; }
.summary-row.total { font-size: 1.2rem; font-weight: 700; border-top: 2px solid var(--border); padding-top: 1rem; }

.loading { text-align: center; padding: 3rem; color: var(--text-secondary); }
.empty { text-align: center; padding: 3rem; color: var(--text-secondary); }

.product-detail-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; margin-top: 1rem; }
.product-detail-img { width: 100%; border-radius: var(--radius); }
.product-detail-info h1 { margin: 0.5rem 0; }
.description { margin: 1rem 0; color: var(--text-secondary); line-height: 1.7; }

@media (max-width: 768px) {
  .product-detail-grid { grid-template-columns: 1fr; }
  .cart-item { flex-wrap: wrap; }
  .navbar { flex-wrap: wrap; gap: 0.5rem; }
}
```

---

## 🎯 Stretch Goals

1. Add a "Wishlist" feature (heart icon on product cards)
2. Add product quantity selector on the detail page
3. Implement a checkout form with React Hook Form
4. Add Framer Motion animations for cart add/remove
5. Connect to a real API (Fake Store API: `https://fakestoreapi.com/products`)

---

## 📝 Homework

1. Complete all features from the table above
2. Ensure cart persists across page refreshes
3. Verify dark/light theme works throughout the app
4. Test every route and make sure 404 catches unknown paths

---

## 💡 Pro Tips

- `React.memo` on `ProductCard` prevents re-renders when the search/filter state changes in the parent
- Split state and dispatch contexts to reduce unnecessary re-renders
- `useMemo` for filtering + sorting is critical when product lists grow large
- Lazy-load the Cart page — most users browse before adding items

---

[← Day 5](day-05.md) | [Back to Week 12](README.md) | [Week 13 →](../week-13/README.md)
