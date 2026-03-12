# 📅 Day 4: Backgrounds, Gradients & Shadows 🌈

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- Colors and typography from Day 3

## 🎯 Today's Goals
- Master CSS background properties
- Create linear and radial gradients
- Apply box shadows and text shadows
- Combine techniques for visual depth

---

## 📖 Lesson Content

### 1. CSS Backgrounds

```css
.hero {
    /* Solid color */
    background-color: #f5f5f5;

    /* Image */
    background-image: url('hero-bg.jpg');
    background-size: cover;          /* cover, contain, 100% */
    background-position: center;
    background-repeat: no-repeat;
    background-attachment: fixed;     /* parallax effect */

    /* Shorthand */
    background: url('hero-bg.jpg') center/cover no-repeat fixed;
}
```

### 2. CSS Gradients

```css
/* Linear Gradient */
.gradient-1 {
    background: linear-gradient(to right, #667eea, #764ba2);
}

.gradient-2 {
    background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
}

/* Radial Gradient */
.gradient-3 {
    background: radial-gradient(circle, #667eea, #764ba2);
}

/* Multi-color gradient */
.gradient-4 {
    background: linear-gradient(to right, #ff6b6b, #feca57, #48dbfb, #ff9ff3);
}
```

### 3. Box Shadows

```css
/* box-shadow: x-offset y-offset blur spread color */
.card {
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);       /* subtle */
}

.card:hover {
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.2);       /* elevated */
}

.card-strong {
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);      /* strong */
}

/* Inset shadow */
.inset {
    box-shadow: inset 0 2px 10px rgba(0, 0, 0, 0.1);
}

/* Multiple shadows */
.multi-shadow {
    box-shadow:
        0 2px 4px rgba(0, 0, 0, 0.1),
        0 8px 16px rgba(0, 0, 0, 0.1);
}
```

### 4. Text Shadows

```css
h1 {
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

/* Glowing text */
.glow {
    text-shadow: 0 0 10px rgba(102, 126, 234, 0.8);
}
```

---

## ✍️ Hands-On Exercise
1. Create a hero section with a gradient background
2. Build 3 card components with different shadow levels
3. Add hover effects that change the shadow on cards
4. Apply a background image with overlay to a section

---

## 📝 Homework
- Create a collection of 5 gradient cards
- Experiment with [cssgradient.io](https://cssgradient.io/) for custom gradients
- Apply shadows and gradients to your bio page

---

## 💡 Tips & Best Practices
- 🎯 Use subtle shadows (`rgba(0,0,0,0.1)`) for a modern look
- 🌈 Gradients work great for buttons and hero sections
- 📐 Use `background-size: cover` for responsive background images

---

[← Previous Day](day-03.md) | [Back to Week 2](README.md) | [Next Day →](day-05.md)
