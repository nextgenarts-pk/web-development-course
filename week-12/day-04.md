# 📅 Week 12 — Day 4: Performance Optimization ⚡

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand React's rendering behavior and when re-renders happen
- Use `React.memo` to skip unnecessary re-renders
- Apply `useMemo` and `useCallback` effectively
- Implement code splitting with `React.lazy` and `Suspense`
- Build error boundaries for graceful error handling
- Profile components with React DevTools

---

## 📖 Lesson Content

### 1. When Does React Re-Render?

A component re-renders when:

1. Its **state** changes (via `setState` or `dispatch`)
2. Its **parent** re-renders (unless memoized)
3. The **context** it consumes changes

> Re-rendering is NOT the same as DOM updates. React's virtual DOM diffing ensures only actual changes hit the real DOM. But unnecessary re-renders still cost CPU time.

### 2. React.memo — Skipping Re-Renders

`React.memo` wraps a component so it only re-renders when its **props change** (shallow compare):

```jsx
import { memo } from 'react';

const ProductCard = memo(function ProductCard({ product, onAddToCart }) {
  console.log(`Rendering ProductCard: ${product.name}`);

  return (
    <div className="product-card">
      <h3>{product.name}</h3>
      <p>${product.price}</p>
      <button onClick={() => onAddToCart(product)}>Add to Cart</button>
    </div>
  );
});

export default ProductCard;
```

Without `memo`, every time the parent re-renders (e.g., typing in a search bar), all `ProductCard` components re-render — even if their props didn't change.

### 3. The Callback Problem

`memo` uses **shallow comparison**. If the parent creates a new function on each render, `memo` is defeated:

```jsx
// ❌ New function every render — memo is useless
function ProductList({ products }) {
  function handleAdd(product) {
    dispatch(addToCart(product));
  }

  return products.map(p => (
    <ProductCard key={p.id} product={p} onAddToCart={handleAdd} />
  ));
}
```

**Fix with `useCallback`:**

```jsx
// ✅ Stable function reference
function ProductList({ products }) {
  const handleAdd = useCallback((product) => {
    dispatch(addToCart(product));
  }, [dispatch]);

  return products.map(p => (
    <ProductCard key={p.id} product={p} onAddToCart={handleAdd} />
  ));
}
```

### 4. useMemo for Expensive Computations

```jsx
function ProductList({ products, searchQuery, sortBy }) {
  // ✅ Only recomputes when products, searchQuery, or sortBy change
  const filteredProducts = useMemo(() => {
    console.log('Filtering & sorting...');
    let result = products;

    if (searchQuery) {
      result = result.filter(p =>
        p.name.toLowerCase().includes(searchQuery.toLowerCase())
      );
    }

    if (sortBy === 'price-asc') {
      result = [...result].sort((a, b) => a.price - b.price);
    } else if (sortBy === 'price-desc') {
      result = [...result].sort((a, b) => b.price - a.price);
    } else if (sortBy === 'name') {
      result = [...result].sort((a, b) => a.name.localeCompare(b.name));
    }

    return result;
  }, [products, searchQuery, sortBy]);

  return (
    <div className="product-grid">
      {filteredProducts.map(p => (
        <ProductCard key={p.id} product={p} />
      ))}
    </div>
  );
}
```

### 5. When NOT to Optimize

| Do optimize | Don't bother |
|---|---|
| Lists with 100+ items | Components that render rarely |
| Expensive computations (sort, filter 1000+ items) | Simple components with few props |
| Components that re-render on every keystroke | Components already fast enough |
| Context consumers that don't use all context values | One-off components |

> **Premature optimization is the root of all evil.** Profile first, optimize second.

### 6. Code Splitting with React.lazy

By default, all your code ships in one bundle. `React.lazy` splits it into chunks loaded on demand:

```jsx
import { lazy, Suspense } from 'react';
import { Routes, Route } from 'react-router-dom';

// Lazy-loaded pages
const Home = lazy(() => import('./pages/Home'));
const PostList = lazy(() => import('./pages/PostList'));
const PostDetail = lazy(() => import('./pages/PostDetail'));
const CreatePost = lazy(() => import('./pages/CreatePost'));
const Dashboard = lazy(() => import('./pages/Dashboard'));

function App() {
  return (
    <Suspense fallback={<div className="loading">Loading page...</div>}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/posts" element={<PostList />} />
        <Route path="/posts/:id" element={<PostDetail />} />
        <Route path="/posts/new" element={<CreatePost />} />
        <Route path="/dashboard" element={<Dashboard />} />
      </Routes>
    </Suspense>
  );
}
```

Each page becomes a separate `.js` file — loaded only when the user navigates to that route.

### 7. Suspense Fallback

`Suspense` shows a fallback while the lazy component loads:

```jsx
function LoadingPage() {
  return (
    <div className="loading-page">
      <div className="spinner" />
      <p>Loading...</p>
    </div>
  );
}

<Suspense fallback={<LoadingPage />}>
  <Routes>
    {/* ... */}
  </Routes>
</Suspense>
```

### 8. Error Boundaries

React doesn't have a hook for catching render errors. You need a **class component**:

```jsx
// components/ErrorBoundary.jsx
import { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('ErrorBoundary caught:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        this.props.fallback || (
          <div className="error-boundary">
            <h2>Something went wrong 😕</h2>
            <p>{this.state.error?.message}</p>
            <button onClick={() => this.setState({ hasError: false, error: null })}>
              Try Again
            </button>
          </div>
        )
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

**Usage:**

```jsx
<ErrorBoundary fallback={<p>Failed to load products.</p>}>
  <ProductList />
</ErrorBoundary>
```

### 9. Combining Lazy + Suspense + ErrorBoundary

```jsx
import { lazy, Suspense } from 'react';
import ErrorBoundary from './components/ErrorBoundary';

const Dashboard = lazy(() => import('./pages/Dashboard'));

function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<p>Loading...</p>}>
        <Dashboard />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### 10. React DevTools Profiler

1. Install [React Developer Tools](https://react.dev/learn/react-developer-tools) browser extension
2. Open DevTools → **Profiler** tab
3. Click Record → interact with your app → Stop
4. Inspect which components rendered and why
5. Look for components that render unnecessarily

---

## 💪 Hands-On Exercise

### Optimize a Product Catalog

1. Start with a list of 200 products (generate mock data)
2. Add a search bar that filters products on every keystroke
3. Without optimization — observe how many components re-render
4. Add `React.memo` to `ProductCard`
5. Add `useCallback` for the `onAddToCart` handler
6. Add `useMemo` for the filtered + sorted product list
7. Lazy-load the Cart page
8. Wrap the app in an `ErrorBoundary`
9. Use React DevTools Profiler to verify improvements

---

## 📝 Homework

1. Profile the optimized vs. unoptimized version — note the difference in render count
2. Add a custom comparison function to `React.memo` that only checks `product.id`
3. Lazy-load a "Heavy" component that has a simulated delay (`await new Promise(r => setTimeout(r, 2000))`)
4. Create a reusable `ErrorBoundary` with a "Retry" button that resets the error state

---

## 💡 Pro Tips

- **Profile before optimizing** — don't guess, measure with React DevTools
- `React.memo` only does a **shallow** comparison — deep objects always look "different"
- `useMemo` and `useCallback` have their own cost — don't add them everywhere
- Code splitting is free performance — always lazy-load routes in production apps
- Error boundaries catch **render** errors — not event handler errors or async errors

---

[← Day 3](day-03.md) | [Back to Week 12](README.md) | [Day 5 →](day-05.md)
