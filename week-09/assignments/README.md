# 📋 Week 9 Assignments — React.js Fundamentals

> **NextGen Arts — Full Stack Web Development** | [nextgenarts.pk](https://nextgenarts.pk)

---

## 🎯 Weekly Project: React To-Do App ✅

Build a complete React To-Do application demonstrating mastery of React fundamentals: components, props, JSX, conditional rendering, and list rendering.

### Requirements

| Feature | Points |
|---------|--------|
| Vite + React project setup with clean structure | 10 |
| Component composition (5+ components) | 15 |
| Props used correctly for data & callbacks | 15 |
| Add new tasks via form input | 10 |
| Toggle task completion | 10 |
| Delete tasks | 10 |
| Filter tasks (All / Active / Completed) | 10 |
| Styled with CSS Modules or Tailwind | 10 |
| Empty state & edge case handling | 5 |
| Code quality (clean, organized, no warnings) | 5 |
| **Total** | **100** |

### Submission Checklist

- [ ] Created with Vite + React template
- [ ] Components in separate files inside `components/` folder
- [ ] Props passed correctly (no prop drilling beyond 2 levels)
- [ ] `.map()` with proper `key` props
- [ ] Conditional rendering for empty state, completed status
- [ ] Filter buttons change displayed tasks
- [ ] Clean, consistent styling
- [ ] No console errors or warnings
- [ ] README with setup instructions

---

## 📝 Daily Exercises

### Day 1: Introduction to React & JSX

1. Set up a Vite + React project and explore the generated files
2. Build a **ProfileCard** component with name, bio, and avatar
3. Create a **SkillsList** component that renders an array of skills
4. Build a **Clock** component showing the current date and time

### Day 2: Components & Composition

1. Create **Header**, **Main**, and **Footer** in separate files
2. Build a **CourseCard** component and render 4 different courses
3. Create a **Testimonial** and **TestimonialList** component
4. Build a **FAQ** component with question/answer display

### Day 3: Props

1. Build a **RecipeCard** accepting `title`, `ingredients`, `cookTime`, `difficulty`
2. Create a **Modal** component using the `children` prop
3. Build an **Alert** with `type` (success/warning/error) and `message`
4. Create a **Navbar** receiving `links` array as prop

### Day 4: Conditional Rendering & Lists

1. Build a **TaskList** with ✅/⬜ based on `completed` status
2. Create a **NotificationList** with different types rendering differently
3. Build a **LeaderBoard** sorted by score with top 3 highlighted
4. Create a **TagCloud** with sizes based on tag frequency

### Day 5: Styling React Apps

1. Build a **NavBar** with responsive mobile menu using Tailwind
2. Create a **TestimonialCard** with CSS Modules
3. Build a **PricingTable** with 3 plan cards using Tailwind
4. Create a **dark mode toggle** switching between themes

### Day 6: Project Day

1. Complete the **React To-Do App** with all required features
2. (Stretch) Add localStorage persistence, edit functionality, or drag & drop

---

## 📊 Grading Rubric

| Criteria | Excellent (90-100%) | Good (70-89%) | Needs Work (< 70%) |
|----------|-------------------|---------------|---------------------|
| **Components** | Well-structured, reusable, single responsibility | Components work but some are too large | Few components, logic mixed together |
| **Props** | Clean prop passing, destructured, defaults set | Props work but some inconsistency | Missing props, prop drilling issues |
| **JSX** | Clean JSX, proper key usage, conditional rendering | Works but some patterns could improve | Incorrect key usage, messy JSX |
| **Styling** | Consistent, responsive, polished look | Functional styling, some rough areas | Minimal or broken styling |
| **Code Quality** | Clean structure, no warnings, good naming | Minor issues, mostly clean | Console warnings, poor naming |

---

## 📅 Submission

- **Due:** End of Week 9 (Saturday)
- **Format:** GitHub repository with README
- **Demo:** Running `npm run dev` should launch the app successfully

---

[← Back to Week 9](README.md) | [Week 10 →](../week-10/README.md)
