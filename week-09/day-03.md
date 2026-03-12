# 📅 Day 3: Props — Passing Data to Components 🔗

> **Week 9 — React.js Fundamentals** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Components and composition from Day 2
- JavaScript destructuring and objects

---

## 🎯 Today's Goals

- 🔗 Understand what props are and how they work
- 📦 Pass strings, numbers, arrays, objects, and functions as props
- 🎯 Destructure props for cleaner code
- 🛡️ Set default prop values
- 👶 Use `children` prop for flexible component composition

---

## 📘 Lesson Content

### What Are Props?

```
Props = "Properties"

Props are how you pass data FROM a parent component TO a child component.

Think of it like function arguments:
- Function: greet("Ahmed") → the argument is "Ahmed"
- Component: <Greeting name="Ahmed" /> → the prop is name="Ahmed"

Props are READ-ONLY — a child cannot modify its props.
```

### Passing and Receiving Props

```jsx
// Parent passes props
function App() {
  return (
    <div>
      <Greeting name="Ahmed" age={25} />
      <Greeting name="Sara" age={22} />
      <Greeting name="Hassan" age={28} />
    </div>
  );
}

// Child receives props as an object
function Greeting(props) {
  return (
    <div>
      <h2>Hello, {props.name}!</h2>
      <p>You are {props.age} years old.</p>
    </div>
  );
}
```

### Destructuring Props (Preferred)

```jsx
// ✅ Destructure in the parameter — clean and clear
function Greeting({ name, age }) {
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
    </div>
  );
}

// ✅ Destructure inside the function body
function Greeting(props) {
  const { name, age } = props;
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
    </div>
  );
}
```

### Prop Types

```jsx
// You can pass ANY JavaScript value as a prop

function App() {
  const skills = ["React", "Node.js", "MongoDB"];
  const address = { city: "Gilgit", country: "Pakistan" };

  return (
    <UserProfile
      name="Ahmed"                    // String
      age={25}                        // Number
      isActive={true}                 // Boolean
      skills={skills}                 // Array
      address={address}               // Object
      joinDate={new Date()}           // Date
      onEdit={() => console.log("Edit clicked")}  // Function
    />
  );
}

function UserProfile({ name, age, isActive, skills, address, joinDate, onEdit }) {
  return (
    <div className="profile">
      <h2>{name} {isActive && "✅"}</h2>
      <p>Age: {age}</p>
      <p>📍 {address.city}, {address.country}</p>
      <p>Skills: {skills.join(", ")}</p>
      <p>Joined: {joinDate.toLocaleDateString()}</p>
      <button onClick={onEdit}>Edit Profile</button>
    </div>
  );
}
```

### Default Props

```jsx
// Set default values using default parameters
function Button({ text = "Click Me", variant = "primary", size = "medium" }) {
  return (
    <button className={`btn btn-${variant} btn-${size}`}>
      {text}
    </button>
  );
}

// All of these work:
<Button />                        // Uses all defaults
<Button text="Submit" />          // Custom text, default variant & size
<Button variant="danger" size="large" text="Delete" />
```

### The `children` Prop

```jsx
// children is a special prop — it's whatever goes BETWEEN the opening and closing tags

function Card({ title, children }) {
  return (
    <div className="card">
      <h3 className="card-title">{title}</h3>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
}

// Usage — anything between <Card> and </Card> becomes children
function App() {
  return (
    <div>
      <Card title="Welcome">
        <p>This paragraph is the children prop!</p>
        <button>Get Started</button>
      </Card>

      <Card title="About Us">
        <p>NextGen Arts provides world-class education.</p>
        <ul>
          <li>Web Development</li>
          <li>AI Integration</li>
          <li>Career Support</li>
        </ul>
      </Card>
    </div>
  );
}
```

### Layout Components with Children

```jsx
// Reusable layout wrapper
function PageLayout({ children }) {
  return (
    <div className="page-layout">
      <Header />
      <main className="page-content">
        {children}
      </main>
      <Footer />
    </div>
  );
}

// Container component
function Container({ maxWidth = "1200px", children }) {
  return (
    <div style={{ maxWidth, margin: "0 auto", padding: "0 1rem" }}>
      {children}
    </div>
  );
}

// Usage
function HomePage() {
  return (
    <PageLayout>
      <Container>
        <h1>Welcome Home!</h1>
        <p>Content goes here.</p>
      </Container>
    </PageLayout>
  );
}
```

### Spreading Props

```jsx
// Pass an entire object as props using spread operator
function App() {
  const userData = {
    name: "Zara Ahmed",
    role: "Frontend Developer",
    avatar: "https://i.pravatar.cc/100?img=10",
    location: "Islamabad"
  };

  return <UserCard {...userData} />;
  // Same as: <UserCard name="Zara Ahmed" role="Frontend Developer" ... />
}

function UserCard({ name, role, avatar, location }) {
  return (
    <div className="user-card">
      <img src={avatar} alt={name} />
      <h3>{name}</h3>
      <p>{role}</p>
      <p>📍 {location}</p>
    </div>
  );
}
```

### Props Are Immutable

```jsx
// ❌ NEVER modify props
function BadComponent({ name }) {
  name = "Changed"; // ❌ Don't do this!
  return <h1>{name}</h1>;
}

// ✅ Props flow ONE WAY: parent → child
// If a child needs to "change" something, use callbacks (coming in Week 10)
function GoodComponent({ name }) {
  return <h1>{name}</h1>; // ✅ Just read and render
}
```

---

## 🛠️ Hands-On Exercise

### Product Card Gallery

```jsx
// src/components/ProductCard.jsx
export default function ProductCard({ name, price, image, rating, inStock = true }) {
  return (
    <div className="product-card">
      <img src={image} alt={name} />
      <h3>{name}</h3>
      <div className="product-info">
        <span className="price">PKR {price.toLocaleString()}</span>
        <span className="rating">{"⭐".repeat(Math.round(rating))} {rating}</span>
      </div>
      <p className={inStock ? "in-stock" : "out-of-stock"}>
        {inStock ? "✅ In Stock" : "❌ Out of Stock"}
      </p>
      <button disabled={!inStock}>
        {inStock ? "Add to Cart 🛒" : "Unavailable"}
      </button>
    </div>
  );
}

// src/App.jsx
import ProductCard from "./components/ProductCard";

const products = [
  { id: 1, name: "Mechanical Keyboard", price: 8500, image: "https://picsum.photos/200?random=1", rating: 4.5 },
  { id: 2, name: "Wireless Mouse", price: 3200, image: "https://picsum.photos/200?random=2", rating: 4.0 },
  { id: 3, name: "USB-C Hub", price: 4500, image: "https://picsum.photos/200?random=3", rating: 3.5, inStock: false },
  { id: 4, name: "Monitor Stand", price: 6000, image: "https://picsum.photos/200?random=4", rating: 5.0 },
];

function App() {
  return (
    <div className="product-gallery">
      <h1>🛒 Tech Store</h1>
      <div className="product-grid">
        {products.map(product => (
          <ProductCard key={product.id} {...product} />
        ))}
      </div>
    </div>
  );
}
```

---

## 📝 Homework

1. Build a **RecipeCard** component that takes `title`, `ingredients` (array), `cookTime`, and `difficulty`
2. Create a **Modal** component using `children` — it should accept any content inside
3. Build an **Alert** component with `type` (success/warning/error), `message`, and optional `title`
4. Create a **Navbar** that receives `links` (array of `{label, href}`) as a prop
5. Build a **pricing table** with 3 `PricingCard` components, each receiving different plans

---

## 💡 Pro Tips

> 🔗 **Props are like function arguments:** Think of each component as a function. Props are the arguments that customize its output.

> 👶 **Use children for wrappers:** Whenever you build a layout component, modal, card, or container — use `children` to make it flexible.

> 🚫 **Never mutate props:** React's one-way data flow (parent → child) is a feature, not a limitation. It makes your app predictable and easy to debug.

---

[← Day 2](day-02.md) | [Back to Week 9](README.md) | [Day 4 →](day-04.md)
