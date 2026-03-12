# 📅 Day 2: Flexbox Practice — Navbars & Card Layouts 🧭

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Flex container and item properties from Day 1
- Understanding of `justify-content` and `align-items`

---

## 🎯 Today's Goals

- 🧭 Build a responsive navigation bar with Flexbox
- 🃏 Create reusable card components
- 📱 Make card grids that wrap responsively
- 🎨 Build pricing table layouts

---

## 📘 Lesson Content

### Responsive Navbar with Flexbox

```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  background: #2c3e50;
  color: white;
}

.navbar__logo {
  font-size: 1.5rem;
  font-weight: bold;
}

.navbar__links {
  display: flex;
  list-style: none;
  gap: 1.5rem;
  margin: 0;
  padding: 0;
}

.navbar__links a {
  color: white;
  text-decoration: none;
  transition: color 0.3s;
}

.navbar__links a:hover {
  color: #3498db;
}

.navbar__actions {
  display: flex;
  gap: 0.75rem;
}
```

```html
<nav class="navbar">
  <div class="navbar__logo">NextGen Arts</div>
  <ul class="navbar__links">
    <li><a href="#">Home</a></li>
    <li><a href="#">Courses</a></li>
    <li><a href="#">About</a></li>
    <li><a href="#">Contact</a></li>
  </ul>
  <div class="navbar__actions">
    <button class="btn btn--outline">Login</button>
    <button class="btn btn--primary">Sign Up</button>
  </div>
</nav>
```

### Reusable Card Component

```css
.card {
  display: flex;
  flex-direction: column;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: transform 0.3s, box-shadow 0.3s;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.card__image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.card__body {
  padding: 1.5rem;
  flex: 1; /* Takes remaining space */
  display: flex;
  flex-direction: column;
}

.card__title {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
}

.card__text {
  color: #666;
  flex: 1; /* Pushes footer down */
}

.card__footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}
```

### Card Grid Layout

```css
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 2rem;
  padding: 2rem;
}

.card-grid .card {
  flex: 1 1 300px;  /* Grow, shrink, min-width 300px */
  max-width: 400px;
}
```

### Pricing Table with Flexbox

```css
.pricing {
  display: flex;
  justify-content: center;
  gap: 2rem;
  padding: 3rem;
  flex-wrap: wrap;
}

.pricing__plan {
  flex: 1 1 280px;
  max-width: 350px;
  display: flex;
  flex-direction: column;
  text-align: center;
  border: 2px solid #e0e0e0;
  border-radius: 16px;
  padding: 2rem;
}

.pricing__plan--featured {
  border-color: #3498db;
  transform: scale(1.05);
  box-shadow: 0 10px 30px rgba(52, 152, 219, 0.2);
}

.pricing__features {
  flex: 1;
  list-style: none;
  padding: 0;
}

.pricing__features li {
  padding: 0.5rem 0;
  border-bottom: 1px solid #f0f0f0;
}
```

---

## 🛠️ Hands-On Exercise

Build a complete **Course Cards Page** with:

1. A navbar with logo, links, and action buttons
2. A hero section with centered text
3. A card grid showing 6 course cards
4. Each card has: image, title, description, price, and "Enroll" button

```html
<section class="courses">
  <h2 class="section-title">Our Courses</h2>
  <div class="card-grid">
    <article class="card">
      <img src="https://picsum.photos/400/200?random=1" alt="Course" class="card__image">
      <div class="card__body">
        <h3 class="card__title">Web Development</h3>
        <p class="card__text">Learn HTML, CSS, and JavaScript from scratch.</p>
        <div class="card__footer">
          <span class="card__price">$99</span>
          <button class="btn btn--primary">Enroll</button>
        </div>
      </div>
    </article>
    <!-- Repeat for more cards -->
  </div>
</section>
```

---

## 📝 Homework

1. Build a **footer** with 4 columns using Flexbox (About, Links, Contact, Social)
2. Create a **team members** section with circular avatar cards
3. Add **hover effects** to all cards and buttons
4. Make the navbar **sticky** at the top of the page

---

## 💡 Pro Tips

> 🃏 **Card Height:** Use `flex: 1` on `.card__body` and `.card__text` to ensure all cards in a row have equal height with buttons aligned at the bottom.

> 📱 **Responsive Cards:** `flex: 1 1 300px` makes cards automatically wrap and resize — no media queries needed for basic responsiveness!

---

[← Day 1](day-01.md) | [Back to Week 3](README.md) | [Day 3 →](day-03.md)
