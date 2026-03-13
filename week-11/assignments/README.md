# 📋 Week 11 Assignments — React Router & Multi-Page Apps

> **NextGen Arts — Full Stack Web Development** | [nextgenarts.pk](https://nextgenarts.pk)

---

## 🎯 Weekly Project: Multi-Page Blog App 📝

Build a complete multi-page blog application using React Router, Context API, and a REST API (JSONPlaceholder). The app should demonstrate routing, nested layouts, dynamic pages, global state, and full CRUD operations.

### Requirements

| Feature | Points |
|---------|--------|
| React Router with 5+ routes (home, posts, detail, create, login, 404) | 15 |
| Nested layout with shared Navbar & Footer via `<Outlet />` | 10 |
| Dynamic routes with `useParams` (post detail page) | 10 |
| Search with `useSearchParams` (bookmarkable search) | 10 |
| Pagination on the post list page | 10 |
| Context API — theme toggle (dark/light, persisted) | 10 |
| Context API — auth context (login/logout) | 10 |
| Protected route for "Create Post" | 5 |
| API integration — fetch posts, comments, user | 10 |
| Responsive styling, clean UI | 5 |
| Code quality (service layer, reusable components) | 5 |
| **Total** | **100** |

### Submission Checklist

- [ ] All routes work and display the correct pages
- [ ] NavLink shows active state for the current page
- [ ] Post detail page loads post, author, and comments by ID
- [ ] Search filters posts and persists in the URL
- [ ] Pagination works and updates the URL
- [ ] Theme toggles between dark and light, persists on refresh
- [ ] Login/logout toggles navbar UI and protects the create page
- [ ] 404 page catches unknown routes
- [ ] No console errors or warnings
- [ ] Responsive on mobile

---

## 📝 Daily Exercises

### Day 1: React Router Setup & Basic Routing

1. Create a 4-page app with Navbar using `NavLink`
2. Add a 404 catch-all page
3. Use `useNavigate` for form redirect
4. Display current path with `useLocation`

### Day 2: Dynamic Routes & URL Parameters

1. Build a user directory with `/users/:userId`
2. Fetch and display data based on URL params
3. Add pagination with `useSearchParams`
4. Handle invalid params gracefully

### Day 3: Nested Routes & Layouts

1. Build a dashboard with sidebar + `<Outlet />`
2. Create index and child routes
3. Add a protected layout route
4. Style active `NavLink` in sidebar

### Day 4: useContext & Global State

1. Build `ThemeContext` with toggle + localStorage
2. Build `AuthContext` with login/logout
3. Combine both providers in the app
4. Protected route using auth context

### Day 5: API Integration in React

1. Build a service layer (`services/api.js`)
2. Create a full CRUD flow (list, detail, create, delete)
3. Build a reusable `useFetch` hook
4. Handle loading, error, and empty states

### Day 6: Project Day

1. Complete the **Multi-Page Blog App** with all features
2. (Stretch) Add edit/delete, route transitions, or tag filtering

---

## 📊 Grading Rubric

| Criteria | Excellent (90-100%) | Good (70-89%) | Needs Work (< 70%) |
|----------|-------------------|---------------|---------------------|
| **Routing** | All routes correct, nested layouts, params, search params | Most routes work, some issues | Routes broken or missing |
| **Context** | Theme + Auth contexts clean, with custom hooks | Works but messy or incomplete | Context not used or broken |
| **API Integration** | Service layer, loading/error states, CRUD | Fetches work but inline, no error handling | Broken fetches, no loading states |
| **UX** | Responsive, themed, polished, no console errors | Functional but rough | Poor layout, errors in console |
| **Code Quality** | Clean project structure, reusable components | Some organization | All code in one file, no structure |

---

## 📅 Submission

- **Due:** End of Week 11 (Saturday)
- **Format:** GitHub repository with README
- **Demo:** Running `npm run dev` should launch the app with working routes

---

[← Back to Week 11](README.md) | [Week 12 →](../week-12/README.md)
