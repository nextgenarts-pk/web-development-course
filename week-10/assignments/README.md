# 📋 Week 10 Assignments — React Hooks Deep Dive

> **NextGen Arts — Full Stack Web Development** | [nextgenarts.pk](https://nextgenarts.pk)

---

## 🎯 Weekly Project: Expense Tracker App 💰

Build a complete expense tracker application demonstrating mastery of React hooks: useState, useEffect, useRef, useMemo, useCallback, and custom hooks.

### Requirements

| Feature | Points |
|---------|--------|
| Balance dashboard (total, income, expenses) | 10 |
| Add transaction form (description, amount, type, category) | 15 |
| Transaction list with filter & sort | 15 |
| Delete transactions | 10 |
| Category breakdown with visual bars | 10 |
| localStorage persistence (useLocalStorage hook) | 10 |
| useMemo for computed values (balance, filtering) | 10 |
| Custom hook(s) used properly | 10 |
| Styled with Tailwind (responsive, clean) | 5 |
| Code quality (hooks rules, no warnings) | 5 |
| **Total** | **100** |

### Submission Checklist

- [ ] Balance updates correctly when adding/deleting transactions
- [ ] Form validates input (non-empty, positive amount)
- [ ] Filter works (All / Income / Expense)
- [ ] Sort works (by date, amount, name)
- [ ] Transactions persist across page refreshes
- [ ] At least one custom hook (useLocalStorage)
- [ ] useMemo used for expensive computations
- [ ] No console warnings or errors
- [ ] Responsive design

---

## 📝 Daily Exercises

### Day 1: useState Deep Dive

1. Build a color picker with R/G/B sliders
2. Create a shopping list with add, edit, remove, mark-as-purchased
3. Build a multi-step form with state for all fields
4. Create a scoreboard with player names, scores, and sorting

### Day 2: useEffect

1. Build a random quote fetcher (fetch on mount + button click)
2. Create a dark mode toggle synced to localStorage
3. Build a mouse position tracker with cleanup
4. Create a countdown timer with start/pause/reset

### Day 3: Forms

1. Build a login form with email/password and show/hide toggle
2. Create a survey with radio buttons, checkboxes, textarea + validation
3. Build a password strength meter
4. Create a dynamic form builder (add/remove fields)

### Day 4: useRef, useMemo & useCallback

1. Build a video player with ref-based controls
2. Create a filterable table with 500+ rows using useMemo
3. Build a parent-child pair using useCallback + React.memo
4. Create a form with auto-save via useRef timer

### Day 5: Custom Hooks

1. Build `useHover` — detects hover state on an element
2. Create `useKeyPress` — detects specific key presses
3. Build `useMediaQuery` — matches CSS media queries
4. Create `useAsync` — wraps async functions with loading/error state

### Day 6: Project Day

1. Complete the **Expense Tracker App** with all features
2. (Stretch) Add edit, monthly budget, or export-to-CSV features

---

## 📊 Grading Rubric

| Criteria | Excellent (90-100%) | Good (70-89%) | Needs Work (< 70%) |
|----------|-------------------|---------------|---------------------|
| **Hooks Usage** | Correct hooks for every use case, custom hooks | Most hooks used correctly | Wrong hook choices, misuse |
| **State Management** | Immutable updates, derived state, no duplication | Works but some unnecessary state | Mutable updates, duplicated state |
| **Side Effects** | Proper cleanup, dependency arrays correct | Mostly correct, minor dependency issues | Missing cleanup, wrong dependencies |
| **Custom Hooks** | Reusable, well-designed, clean API | Functional but could be more reusable | Not extracted or poorly designed |
| **Code Quality** | Clean, modular, no warnings | Minor issues | Warnings, poor structure |

---

## 📅 Submission

- **Due:** End of Week 10 (Saturday)
- **Format:** GitHub repository with README
- **Demo:** Running `npm run dev` should launch the app

---

[← Back to Week 10](README.md) | [Week 11 →](../week-11/README.md)
