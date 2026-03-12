# 📅 Day 4: Conditional Rendering & Lists 📋

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Components and props from Days 2-3
- JavaScript arrays, `.map()`, `.filter()`, ternary operator

---

## 🎯 Today's Goals

- 🔀 Render UI conditionally (if/else, ternary, `&&`)
- 📋 Render lists from arrays using `.map()`
- 🔑 Understand the `key` prop and why it matters
- 🔍 Filter and sort lists dynamically

---

## 📘 Lesson Content

### Conditional Rendering

```jsx
// Method 1: if/else (early return)
function UserGreeting({ isLoggedIn, username }) {
  if (!isLoggedIn) {
    return <p>Please log in to continue.</p>;
  }

  return <h2>Welcome back, {username}!</h2>;
}

// Method 2: Ternary operator (inline)
function StatusBadge({ status }) {
  return (
    <span className={`badge ${status}`}>
      {status === "active" ? "✅ Active" : "❌ Inactive"}
    </span>
  );
}

// Method 3: Logical AND (render or nothing)
function Notification({ count }) {
  return (
    <div>
      <h2>Dashboard</h2>
      {count > 0 && (
        <span className="notification-badge">
          🔔 {count} new notifications
        </span>
      )}
    </div>
  );
}

// Method 4: Logical OR (fallback values)
function UserName({ name }) {
  return <h2>{name || "Anonymous User"}</h2>;
}

// Method 5: Nullish coalescing
function Price({ amount }) {
  return <span>PKR {amount ?? "N/A"}</span>;
}
```

### Common Conditional Patterns

```jsx
// Loading / Error / Data pattern
function DataDisplay({ isLoading, error, data }) {
  if (isLoading) return <div className="spinner">Loading...</div>;
  if (error) return <div className="error">Error: {error}</div>;
  if (!data || data.length === 0) return <p>No data found.</p>;

  return (
    <ul>
      {data.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// Role-based rendering
function Dashboard({ user }) {
  return (
    <div>
      <h1>Dashboard</h1>
      {user.role === "admin" && <AdminPanel />}
      {user.role === "editor" && <EditorTools />}
      <UserContent />
    </div>
  );
}

// Rendering different components
function Icon({ type }) {
  const icons = {
    success: "✅",
    warning: "⚠️",
    error: "❌",
    info: "ℹ️"
  };

  return <span className="icon">{icons[type] || "📌"}</span>;
}
```

### Rendering Lists

```jsx
// Basic list rendering with .map()
function StudentList() {
  const students = [
    { id: 1, name: "Ahmed Khan", grade: "A" },
    { id: 2, name: "Sara Ali", grade: "A+" },
    { id: 3, name: "Hassan Raza", grade: "B+" },
    { id: 4, name: "Fatima Noor", grade: "A" }
  ];

  return (
    <div>
      <h2>📋 Student Roster</h2>
      <ul>
        {students.map(student => (
          <li key={student.id}>
            {student.name} — Grade: {student.grade}
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### The `key` Prop

```jsx
// ✅ ALWAYS use unique, stable keys for list items
// Keys help React identify which items changed, added, or removed

// ✅ Best — use unique ID from data
{users.map(user => (
  <UserCard key={user.id} name={user.name} />
))}

// ⚠️ OK for static lists — use index as last resort
{colors.map((color, index) => (
  <li key={index}>{color}</li>
))}

// ❌ NEVER use random values as keys
{items.map(item => (
  <li key={Math.random()}>{item}</li>  // ❌ Causes re-renders!
))}
```

### List with Components

```jsx
// Extracting list items into components

function CourseCard({ course }) {
  return (
    <div className="course-card">
      <h3>{course.title}</h3>
      <p>{course.description}</p>
      <div className="meta">
        <span>⏱️ {course.duration}</span>
        <span>📊 {course.level}</span>
        <span>⭐ {course.rating}</span>
      </div>
    </div>
  );
}

function CourseList({ courses }) {
  if (courses.length === 0) {
    return <p className="empty">No courses available.</p>;
  }

  return (
    <div className="course-list">
      {courses.map(course => (
        <CourseCard key={course.id} course={course} />
      ))}
    </div>
  );
}
```

### Filtering and Sorting Lists

```jsx
function FilterableProductList() {
  const products = [
    { id: 1, name: "Laptop", category: "Electronics", price: 150000, inStock: true },
    { id: 2, name: "Headphones", category: "Electronics", price: 5000, inStock: true },
    { id: 3, name: "Notebook", category: "Stationery", price: 200, inStock: false },
    { id: 4, name: "Backpack", category: "Accessories", price: 3500, inStock: true },
    { id: 5, name: "Pen Set", category: "Stationery", price: 800, inStock: true },
  ];

  // Filter: only in-stock items
  const available = products.filter(p => p.inStock);

  // Sort: by price low to high
  const sorted = [...available].sort((a, b) => a.price - b.price);

  // Group: by category
  const grouped = products.reduce((groups, product) => {
    const category = product.category;
    if (!groups[category]) groups[category] = [];
    groups[category].push(product);
    return groups;
  }, {});

  return (
    <div>
      <h2>Available Products (sorted by price)</h2>
      <ul>
        {sorted.map(p => (
          <li key={p.id}>{p.name} — PKR {p.price.toLocaleString()}</li>
        ))}
      </ul>

      <h2>Products by Category</h2>
      {Object.entries(grouped).map(([category, items]) => (
        <div key={category}>
          <h3>{category}</h3>
          <ul>
            {items.map(item => (
              <li key={item.id}>{item.name}</li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}
```

### Nested Lists

```jsx
function CourseOutline() {
  const phases = [
    {
      id: 1,
      title: "Phase 1: Web Foundations",
      weeks: [
        { id: "w1", name: "HTML Basics" },
        { id: "w2", name: "CSS Fundamentals" },
        { id: "w3", name: "Flexbox & Grid" },
        { id: "w4", name: "Tailwind CSS" }
      ]
    },
    {
      id: 2,
      title: "Phase 2: JavaScript",
      weeks: [
        { id: "w5", name: "JS Fundamentals" },
        { id: "w6", name: "DOM & Events" },
        { id: "w7", name: "ES6+" },
        { id: "w8", name: "Async & APIs" }
      ]
    }
  ];

  return (
    <div className="outline">
      {phases.map(phase => (
        <div key={phase.id} className="phase">
          <h2>{phase.title}</h2>
          <ol>
            {phase.weeks.map(week => (
              <li key={week.id}>{week.name}</li>
            ))}
          </ol>
        </div>
      ))}
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Student Directory with Filters

```jsx
const students = [
  { id: 1, name: "Ahmed Khan", grade: "A+", track: "Frontend", active: true },
  { id: 2, name: "Sara Ali", grade: "A", track: "Backend", active: true },
  { id: 3, name: "Hassan Raza", grade: "B+", track: "Frontend", active: false },
  { id: 4, name: "Fatima Noor", grade: "A+", track: "Full Stack", active: true },
  { id: 5, name: "Omar Siddiqui", grade: "B", track: "Frontend", active: true },
];

function StudentCard({ student }) {
  return (
    <div className={`student-card ${student.active ? "" : "inactive"}`}>
      <h3>{student.name} {!student.active && "(Inactive)"}</h3>
      <p>Track: {student.track}</p>
      <p>Grade: {student.grade}</p>
      {student.grade.startsWith("A") && <span className="badge">🏆 Honor Roll</span>}
    </div>
  );
}

function StudentDirectory() {
  const activeStudents = students.filter(s => s.active);
  const frontendStudents = students.filter(s => s.track === "Frontend");

  return (
    <div>
      <h1>Student Directory</h1>

      <h2>Active Students ({activeStudents.length})</h2>
      {activeStudents.map(s => <StudentCard key={s.id} student={s} />)}

      <h2>Frontend Track ({frontendStudents.length})</h2>
      {frontendStudents.map(s => <StudentCard key={s.id} student={s} />)}

      {students.length === 0 && <p>No students enrolled yet.</p>}
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **TaskList** that renders tasks and shows ✅ or ⬜ based on `completed` status
2. Create a **NotificationList** where different types (info, warning, error) render differently
3. Build a **LeaderBoard** that sorts players by score and highlights the top 3
4. Create a **FAQ** component that shows "No questions yet" when the array is empty
5. Build a **TagCloud** that renders tags from an array with different sizes based on frequency

---

## 💡 Pro Tips

> 🔑 **Keys must be unique among siblings:** Keys only need to be unique within the same list, not globally in your app.

> 🚫 **Don't use array index as key for dynamic lists:** If items can be reordered, added, or removed, index keys cause subtle rendering bugs. Use stable IDs.

> 🎯 **Filter/sort BEFORE `.map()`:** Always chain `.filter()` and `.sort()` before `.map()` to keep your rendering logic clean.

---

[← Day 3](day-03.md) | [Back to Week 9](README.md) | [Day 5 →](day-05.md)
