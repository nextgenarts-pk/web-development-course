# 📅 Day 5: Styling React Apps (CSS Modules, Tailwind) 🎨

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- React components and props from Days 1-4
- CSS fundamentals (Weeks 2-4)
- Tailwind CSS basics (Week 4)

---

## 🎯 Today's Goals

- 🎨 Explore all styling approaches in React
- 📦 Use CSS Modules for scoped styles
- 🌊 Integrate Tailwind CSS with React
- 🧩 Build a reusable component library with consistent styling

---

## 📘 Lesson Content

### Styling Approaches in React

```
1. Global CSS         → import "./styles.css"
2. Inline Styles      → style={{ color: "red" }}
3. CSS Modules        → import styles from "./Button.module.css"
4. Tailwind CSS       → className="bg-red-500 text-white p-4"
5. CSS-in-JS          → styled-components, Emotion (libraries)

We'll focus on CSS Modules and Tailwind — the most practical choices.
```

### 1. Global CSS (Review)

```jsx
// src/index.css
/* These styles apply everywhere */
body {
  margin: 0;
  font-family: system-ui, sans-serif;
}

// src/main.jsx
import "./index.css"; // Import once at the top level
```

**Problem:** Class names can clash across components. `.card` in one file might override `.card` in another.

### 2. Inline Styles

```jsx
function InlineExample() {
  // Inline styles use camelCase and objects
  const headerStyle = {
    backgroundColor: "#1a1a2e",
    color: "#eee",
    padding: "1rem 2rem",
    textAlign: "center"
  };

  return (
    <header style={headerStyle}>
      <h1 style={{ fontSize: "2rem", margin: 0 }}>NextGen Arts</h1>
      <p style={{ opacity: 0.8, marginTop: "0.5rem" }}>Learn. Build. Deploy.</p>
    </header>
  );
}

// Limitations:
// ❌ No hover, media queries, or pseudo-elements
// ❌ No CSS animations
// ❌ Verbose for complex styles
// ✅ Great for dynamic styles based on props/state
```

### 3. CSS Modules (Recommended)

```css
/* src/components/Button.module.css */
.button {
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s;
}

.primary {
  background-color: #e50914;
  color: white;
}

.primary:hover {
  background-color: #b0060f;
}

.secondary {
  background-color: #333;
  color: white;
}

.large {
  padding: 14px 28px;
  font-size: 1.2rem;
}
```

```jsx
// src/components/Button.jsx
import styles from "./Button.module.css";

export default function Button({ children, variant = "primary", size = "" }) {
  // CSS Modules auto-generate unique class names
  // styles.button → "Button_button_x7yz2"
  const classNames = [
    styles.button,
    styles[variant],
    size && styles[size]
  ].filter(Boolean).join(" ");

  return (
    <button className={classNames}>
      {children}
    </button>
  );
}

// Usage
<Button variant="primary">Enroll Now</Button>
<Button variant="secondary" size="large">Learn More</Button>
```

### CSS Modules for a Card

```css
/* src/components/Card.module.css */
.card {
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: transform 0.3s, box-shadow 0.3s;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.body {
  padding: 1.25rem;
}

.title {
  font-size: 1.2rem;
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.description {
  color: #666;
  line-height: 1.5;
}
```

```jsx
// src/components/Card.jsx
import styles from "./Card.module.css";

export default function Card({ image, title, description, children }) {
  return (
    <div className={styles.card}>
      {image && <img className={styles.image} src={image} alt={title} />}
      <div className={styles.body}>
        <h3 className={styles.title}>{title}</h3>
        <p className={styles.description}>{description}</p>
        {children}
      </div>
    </div>
  );
}
```

### 4. Tailwind CSS in React

```bash
# Add Tailwind to a Vite + React project
npm install -D tailwindcss @tailwindcss/vite

# In vite.config.js, add the plugin:
```

```js
// vite.config.js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import tailwindcss from "@tailwindcss/vite";

export default defineConfig({
  plugins: [react(), tailwindcss()],
});
```

```css
/* src/index.css */
@import "tailwindcss";
```

```jsx
// Now use Tailwind utility classes in your components!

function TailwindCard({ title, description, image }) {
  return (
    <div className="bg-white rounded-xl shadow-md overflow-hidden hover:shadow-xl transition-shadow duration-300">
      <img
        className="w-full h-48 object-cover"
        src={image}
        alt={title}
      />
      <div className="p-5">
        <h3 className="text-xl font-bold text-gray-800 mb-2">{title}</h3>
        <p className="text-gray-600 leading-relaxed">{description}</p>
        <button className="mt-4 w-full bg-red-600 text-white py-2 px-4 rounded-lg hover:bg-red-700 transition-colors">
          Learn More
        </button>
      </div>
    </div>
  );
}
```

### Tailwind with Dynamic Classes

```jsx
function Badge({ type = "info", children }) {
  const colors = {
    info: "bg-blue-100 text-blue-800",
    success: "bg-green-100 text-green-800",
    warning: "bg-yellow-100 text-yellow-800",
    error: "bg-red-100 text-red-800"
  };

  return (
    <span className={`inline-block px-3 py-1 rounded-full text-sm font-medium ${colors[type]}`}>
      {children}
    </span>
  );
}

// Usage
<Badge type="success">Active</Badge>
<Badge type="error">Expired</Badge>
<Badge type="warning">Pending</Badge>
```

### Tailwind Responsive Design

```jsx
function HeroSection() {
  return (
    <section className="bg-gray-900 text-white py-12 md:py-20 lg:py-32 px-4">
      <div className="max-w-5xl mx-auto text-center">
        <h1 className="text-3xl md:text-5xl lg:text-6xl font-bold mb-4">
          Learn Full Stack Development
        </h1>
        <p className="text-gray-400 text-lg md:text-xl mb-8 max-w-2xl mx-auto">
          Master HTML, CSS, JavaScript, React, Node.js, and more in 24 weeks.
        </p>
        <div className="flex flex-col sm:flex-row gap-4 justify-center">
          <button className="bg-red-600 hover:bg-red-700 px-8 py-3 rounded-lg font-semibold transition-colors">
            Enroll Now
          </button>
          <button className="border border-gray-500 hover:border-white px-8 py-3 rounded-lg font-semibold transition-colors">
            View Curriculum
          </button>
        </div>
      </div>
    </section>
  );
}
```

### Building a Mini Component Library

```jsx
// src/components/ui/index.jsx — Reusable UI components

export function Container({ children, className = "" }) {
  return (
    <div className={`max-w-6xl mx-auto px-4 ${className}`}>
      {children}
    </div>
  );
}

export function SectionTitle({ children, subtitle }) {
  return (
    <div className="text-center mb-10">
      <h2 className="text-3xl font-bold text-gray-800">{children}</h2>
      {subtitle && <p className="text-gray-500 mt-2">{subtitle}</p>}
    </div>
  );
}

export function Grid({ children, cols = 3 }) {
  const colClasses = {
    1: "grid-cols-1",
    2: "grid-cols-1 md:grid-cols-2",
    3: "grid-cols-1 md:grid-cols-2 lg:grid-cols-3",
    4: "grid-cols-1 md:grid-cols-2 lg:grid-cols-4"
  };

  return (
    <div className={`grid gap-6 ${colClasses[cols]}`}>
      {children}
    </div>
  );
}
```

---

## 🛠️ Hands-On Exercise

### Build a Styled Course Landing Page

Combine CSS Modules and/or Tailwind to build a landing page:

```jsx
import { Container, SectionTitle, Grid } from "./components/ui";

function LandingPage() {
  const features = [
    { icon: "💻", title: "Hands-On Projects", desc: "Build 12+ real-world projects" },
    { icon: "🤖", title: "AI Integration", desc: "Learn to build AI-powered apps" },
    { icon: "📜", title: "Certificate", desc: "Internationally recognized" },
    { icon: "👨‍🏫", title: "Expert Mentors", desc: "Learn from industry professionals" },
    { icon: "🌐", title: "Online + Onsite", desc: "Flexible learning options" },
    { icon: "💼", title: "Career Support", desc: "Job placement assistance" }
  ];

  return (
    <div>
      <HeroSection />
      <section className="py-16 bg-gray-50">
        <Container>
          <SectionTitle subtitle="Everything you need to become a full stack developer">
            Why NextGen Arts?
          </SectionTitle>
          <Grid cols={3}>
            {features.map((f, i) => (
              <div key={i} className="bg-white p-6 rounded-xl shadow-sm text-center hover:shadow-md transition-shadow">
                <span className="text-4xl">{f.icon}</span>
                <h3 className="text-lg font-bold mt-3">{f.title}</h3>
                <p className="text-gray-500 mt-2">{f.desc}</p>
              </div>
            ))}
          </Grid>
        </Container>
      </section>
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **NavBar** component using Tailwind with responsive mobile menu
2. Create a **TestimonialCard** with CSS Modules (photo, quote, name, role)
3. Build a **PricingTable** with 3 plan cards using Tailwind (Basic, Pro, Enterprise)
4. Create a **dark mode toggle** that switches between two CSS themes
5. Style the **ProductCard** from Day 3 homework with both CSS Modules and Tailwind (compare)

---

## 💡 Pro Tips

> 🎨 **CSS Modules for component styles, Tailwind for utilities:** Many teams use CSS Modules for complex component styles and Tailwind for layout/spacing — they work great together.

> 🔤 **Consistent naming:** Use a naming convention for CSS module classes: `.card`, `.cardTitle`, `.cardBody` — avoid BEM in modules since they're already scoped.

> ⚡ **Tailwind + VS Code:** Install the "Tailwind CSS IntelliSense" extension for class name autocomplete and preview.

---

[← Day 4](day-04.md) | [Back to Week 9](README.md) | [Day 6 →](day-06.md)
