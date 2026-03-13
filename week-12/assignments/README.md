# 📋 Week 12 Assignments — State Management & Advanced React Patterns

> **NextGen Arts — Full Stack Web Development** | [nextgenarts.pk](https://nextgenarts.pk)

---

## 🎯 Weekly Project: E-Commerce Product Catalog 🛒

Build a complete e-commerce product catalog demonstrating useReducer, Context + Reducer state management, advanced component patterns, and performance optimization.

### Requirements

| Feature | Points |
|---------|--------|
| Product grid with cards (image, name, price, rating) | 10 |
| Category filter (All, Electronics, Clothing, etc.) | 10 |
| Search by product name | 5 |
| Sort by price, rating, name | 5 |
| Shopping cart — add, remove, update quantity | 15 |
| Cart badge in navbar showing item count | 5 |
| Context + Reducer store (separate state/dispatch contexts) | 15 |
| Action creators for cart actions | 5 |
| Cart persisted to localStorage | 5 |
| Product detail page with `useParams` | 5 |
| React.memo on ProductCard | 5 |
| useMemo for filtering + sorting | 5 |
| Dark/light theme toggle via Context | 5 |
| Code quality (structure, no warnings) | 5 |
| **Total** | **100** |

### Submission Checklist

- [ ] Products display in a responsive grid
- [ ] Category filter updates the product list
- [ ] Search filters by product name
- [ ] Sort changes the product order
- [ ] Cart: add item increases quantity if already in cart
- [ ] Cart: quantity controls work (increment, decrement, remove at 0)
- [ ] Cart: total and item count are accurate
- [ ] Cart persists on page refresh
- [ ] Product detail page links correctly
- [ ] Theme toggles and persists
- [ ] No console errors or warnings
- [ ] Cart and Product Detail pages lazy-loaded

---

## 📝 Daily Exercises

### Day 1: useReducer

1. Build a task manager with `useReducer` (add, toggle, delete, filter, sort)
2. Implement undo functionality with the undo/redo reducer wrapper
3. Create a multi-step form wizard with reducer (next, back, validate)
4. Build a scoreboard with add/edit/remove player actions

### Day 2: Context + Reducer

1. Build a cart store with `CartProvider`, `useCartState`, `useCartDispatch`
2. Create action creators for all cart actions
3. Add localStorage persistence with lazy initializer
4. Build a notes store with a separate context

### Day 3: Advanced Component Patterns

1. Build a compound `<Accordion>` component
2. Build a compound `<Tabs>` component
3. Create a render-prop `<DataFetcher>` component
4. Build a `withAuth` higher-order component

### Day 4: Performance Optimization

1. Add `React.memo` to `ProductCard` and verify with console logs
2. Use `useMemo` for filtering 200+ products
3. Lazy-load the Cart page with `React.lazy` + `Suspense`
4. Create an `ErrorBoundary` component

### Day 5: React Ecosystem

1. Replace a form with React Hook Form + validation
2. Try TanStack Query for one data fetch
3. Experiment with Zustand for a simple store
4. Read the shadcn/ui docs and understand the copy-paste model

### Day 6: Project Day

1. Complete the **E-Commerce Product Catalog** with all features
2. (Stretch) Add wishlist, checkout form, or Framer Motion animations

---

## 📊 Grading Rubric

| Criteria | Excellent (90-100%) | Good (70-89%) | Needs Work (< 70%) |
|----------|-------------------|---------------|---------------------|
| **State Management** | Context + Reducer with separate contexts, action creators, localStorage | Works but combined context, no action creators | useState for everything, no structure |
| **Component Patterns** | React.memo/useMemo applied where needed, compound components | Some optimization, basic components | No memoization, monolithic components |
| **Feature Completeness** | All features working, polished UI | Most features, some rough edges | Missing multiple features |
| **Code Structure** | Clean folders (store/, components/, pages/), no warnings | Mostly organized | All in one file, messy |
| **Performance** | Lazy loading, ErrorBoundary, memoization verified | Some optimization | No optimization |

---

## 🏆 Phase 3 Summary

Congratulations on completing **Phase 3: React.js**! You've learned:

| Week | Topic | Project |
|------|-------|---------|
| 9 | React Fundamentals (JSX, Components, Props) | To-Do App |
| 10 | React Hooks Deep Dive (useState, useEffect, Custom Hooks) | Expense Tracker |
| 11 | React Router & Multi-Page Apps (Routing, Context, API) | Multi-Page Blog |
| 12 | State Management & Advanced Patterns (Reducer, Optimization) | E-Commerce Catalog |

You're now ready for **Phase 4: Backend Development** with Node.js and Express! 🚀

---

## 📅 Submission

- **Due:** End of Week 12 (Saturday)
- **Format:** GitHub repository with README
- **Demo:** Running `npm run dev` should launch the app

---

[← Back to Week 12](README.md) | [Phase 4 — Week 13 →](../week-13/README.md)
