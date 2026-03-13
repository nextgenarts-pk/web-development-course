# 📅 Week 12 — Day 5: React Ecosystem — Libraries & Tools 🧰

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Survey the essential React ecosystem libraries
- Learn form handling with React Hook Form
- Explore data fetching with TanStack Query (React Query)
- Understand state management options beyond Context
- Work with UI component libraries
- Prepare for full-stack development (Phase 4+)

---

## 📖 Lesson Content

### 1. The React Ecosystem

React is a **library**, not a framework. You choose your own tools for routing, state, forms, etc. Here's the modern landscape:

| Category | Library | Why |
|----------|---------|-----|
| Routing | React Router | Industry standard |
| Forms | React Hook Form | Performance, minimal re-renders |
| Data fetching | TanStack Query | Caching, loading states, refetch |
| State management | Zustand / Redux Toolkit | When Context isn't enough |
| Styling | Tailwind CSS / styled-components | Utility-first or CSS-in-JS |
| UI Kit | shadcn/ui / Headless UI | Accessible, customizable |
| Animation | Framer Motion | Declarative animations |
| Testing | Vitest + React Testing Library | Fast, user-centric tests |

### 2. React Hook Form — Powerful Forms

React Hook Form minimizes re-renders and simplifies validation:

```bash
npm install react-hook-form
```

```jsx
import { useForm } from 'react-hook-form';

function RegisterForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm();

  async function onSubmit(data) {
    console.log('Form data:', data);
    // await api.register(data);
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label>Name</label>
        <input
          {...register('name', {
            required: 'Name is required',
            minLength: { value: 2, message: 'At least 2 characters' },
          })}
        />
        {errors.name && <span className="error">{errors.name.message}</span>}
      </div>

      <div>
        <label>Email</label>
        <input
          type="email"
          {...register('email', {
            required: 'Email is required',
            pattern: {
              value: /^[^\s@]+@[^\s@]+\.[^\s@]+$/,
              message: 'Invalid email',
            },
          })}
        />
        {errors.email && <span className="error">{errors.email.message}</span>}
      </div>

      <div>
        <label>Password</label>
        <input
          type="password"
          {...register('password', {
            required: 'Password is required',
            minLength: { value: 8, message: 'At least 8 characters' },
          })}
        />
        {errors.password && <span className="error">{errors.password.message}</span>}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Registering...' : 'Register'}
      </button>
    </form>
  );
}
```

**Why React Hook Form?**
- **Uncontrolled** inputs — minimal re-renders (contrast with `useState` per field)
- Built-in validation with clear error messages
- Easy async submission handling

### 3. TanStack Query — Smart Data Fetching

TanStack Query (formerly React Query) replaces manual `useEffect` + `useState` fetching:

```bash
npm install @tanstack/react-query
```

**Setup:**

```jsx
// main.jsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById('root')).render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>
);
```

**Fetching:**

```jsx
import { useQuery } from '@tanstack/react-query';

function PostList() {
  const { data: posts, isLoading, error } = useQuery({
    queryKey: ['posts'],
    queryFn: () =>
      fetch('https://jsonplaceholder.typicode.com/posts?_limit=10')
        .then(res => res.json()),
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

**What you get for free:**
- Automatic **caching** — navigating away and back doesn't refetch
- **Background refetch** — stale data is shown immediately, fresh data replaces it
- **Loading / error states** — no manual `useState`
- **Retry** on failure
- DevTools for inspection

**Mutations (POST, PUT, DELETE):**

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function CreatePost() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newPost) =>
      fetch('https://jsonplaceholder.typicode.com/posts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newPost),
      }).then(res => res.json()),
    onSuccess: () => {
      // Invalidate the posts cache so the list refetches
      queryClient.invalidateQueries({ queryKey: ['posts'] });
    },
  });

  function handleSubmit(e) {
    e.preventDefault();
    mutation.mutate({ title: 'New Post', body: 'Content...' });
  }

  return (
    <form onSubmit={handleSubmit}>
      <button disabled={mutation.isPending}>
        {mutation.isPending ? 'Creating...' : 'Create Post'}
      </button>
      {mutation.isError && <p>Error: {mutation.error.message}</p>}
    </form>
  );
}
```

### 4. Zustand — Lightweight State Management

When Context + Reducer gets complex, Zustand offers a simple API:

```bash
npm install zustand
```

```jsx
import { create } from 'zustand';

const useCartStore = create((set) => ({
  items: [],
  total: 0,

  addItem: (product) =>
    set((state) => {
      const existing = state.items.find(i => i.id === product.id);
      let items;
      if (existing) {
        items = state.items.map(i =>
          i.id === product.id ? { ...i, quantity: i.quantity + 1 } : i
        );
      } else {
        items = [...state.items, { ...product, quantity: 1 }];
      }
      return { items, total: items.reduce((s, i) => s + i.price * i.quantity, 0) };
    }),

  removeItem: (id) =>
    set((state) => {
      const items = state.items.filter(i => i.id !== id);
      return { items, total: items.reduce((s, i) => s + i.price * i.quantity, 0) };
    }),

  clearCart: () => set({ items: [], total: 0 }),
}));

// Usage — no Provider needed!
function CartBadge() {
  const itemCount = useCartStore((state) => state.items.length);
  return <span>🛒 {itemCount}</span>;
}

function AddToCartBtn({ product }) {
  const addItem = useCartStore((state) => state.addItem);
  return <button onClick={() => addItem(product)}>Add to Cart</button>;
}
```

### 5. UI Component Libraries

**shadcn/ui** — Copy-paste components built on Radix + Tailwind:

```bash
npx shadcn@latest init
npx shadcn@latest add button card dialog
```

```jsx
import { Button } from '@/components/ui/button';
import { Card, CardHeader, CardTitle, CardContent } from '@/components/ui/card';

function ProductCard({ product }) {
  return (
    <Card>
      <CardHeader>
        <CardTitle>{product.name}</CardTitle>
      </CardHeader>
      <CardContent>
        <p>${product.price}</p>
        <Button onClick={() => addToCart(product)}>Add to Cart</Button>
      </CardContent>
    </Card>
  );
}
```

### 6. Framer Motion — Animations

```bash
npm install framer-motion
```

```jsx
import { motion, AnimatePresence } from 'framer-motion';

function ProductList({ products }) {
  return (
    <AnimatePresence>
      {products.map(product => (
        <motion.div
          key={product.id}
          initial={{ opacity: 0, y: 20 }}
          animate={{ opacity: 1, y: 0 }}
          exit={{ opacity: 0, y: -20 }}
          transition={{ duration: 0.3 }}
        >
          <ProductCard product={product} />
        </motion.div>
      ))}
    </AnimatePresence>
  );
}
```

### 7. Decision Guide

```
Need forms?          → React Hook Form
Need server data?    → TanStack Query
Need global state?   → Context + Reducer (small) / Zustand (medium) / Redux Toolkit (large)
Need UI components?  → shadcn/ui
Need animations?     → Framer Motion
Need testing?        → Vitest + React Testing Library
```

---

## 💪 Hands-On Exercise

### Refactor with Ecosystem Tools

1. Take the blog app from Week 11
2. Replace manual `useEffect` fetching with TanStack Query
3. Replace the create-post form with React Hook Form
4. Compare the code: how many lines were saved? How much cleaner is it?
5. Add a loading skeleton or spinner with Suspense

---

## 📝 Homework

1. Install Zustand and rebuild the cart store from Day 2 — compare the API
2. Add React Hook Form to the login page with email and password validation
3. Set up TanStack React Query DevTools and explore the cache
4. Read the [TanStack Query overview](https://tanstack.com/query/latest/docs/react/overview) and list 3 features you find most useful

---

## 💡 Pro Tips

- Don't install everything at once — add libraries when you actually need them
- React Hook Form's `register` uses **refs** — that's why it doesn't re-render on every keystroke
- TanStack Query's `queryKey` must be a **unique identifier** for the data — include all params
- Zustand doesn't need a Provider — it uses a global store pattern
- shadcn/ui copies code into your project (not a dependency) — you own and customize it

---

[← Day 4](day-04.md) | [Back to Week 12](README.md) | [Day 6 →](day-06.md)
