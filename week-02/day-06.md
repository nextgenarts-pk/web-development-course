# 📅 Day 6: Transitions, Transforms & Project Day ✨

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- All CSS concepts from Week 2 Days 1-5

## 🎯 Today's Goals
- Add smooth CSS transitions to elements
- Apply CSS transforms (rotate, scale, translate, skew)
- Complete the Week 2 project: Style your bio page
- Create polished, interactive UI elements

---

## 📖 Lesson Content

### 1. CSS Transitions

```css
.button {
    background-color: #667eea;
    color: white;
    padding: 12px 24px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.button:hover {
    background-color: #5a67d8;
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}
```

### 2. CSS Transforms

```css
/* Translate (move) */
.move { transform: translate(50px, 20px); }

/* Rotate */
.rotate { transform: rotate(45deg); }

/* Scale */
.grow { transform: scale(1.1); }

/* Skew */
.skew { transform: skew(10deg, 5deg); }

/* Combine transforms */
.combo {
    transform: translate(10px, 0) rotate(5deg) scale(1.05);
}
```

### 3. Interactive Card Example

```css
.card {
    background: white;
    border-radius: 12px;
    padding: 24px;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.08);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.card:hover {
    transform: translateY(-8px);
    box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
}

.card img {
    width: 100%;
    border-radius: 8px;
    transition: transform 0.3s ease;
}

.card:hover img {
    transform: scale(1.05);
}
```

### 4. Animated Navigation Links

```css
.nav-link {
    position: relative;
    text-decoration: none;
    color: #333;
    padding: 8px 0;
}

.nav-link::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    width: 0;
    height: 2px;
    background-color: #667eea;
    transition: width 0.3s ease;
}

.nav-link:hover::after {
    width: 100%;
}
```

---

## ✍️ Hands-On Exercise
Complete the **Styled Bio Page** project with all CSS you've learned this week.

---

## 📝 Homework
- Finish styling your bio page and push to GitHub
- Preview: Read about [CSS Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

---

## 💡 Tips & Best Practices
- ⏱️ Keep transitions under 0.5s for snappy feel
- 🎯 Use `ease` or `ease-in-out` for natural motion
- 🔄 Use `transform` for animations — it's GPU-accelerated

---

[← Previous Day](day-05.md) | [Back to Week 2](README.md) | [Next Week →](../week-03/README.md)
