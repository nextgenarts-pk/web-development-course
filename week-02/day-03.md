# 📅 Day 3: Colors, Typography & Google Fonts 🔤

> **Week 2: CSS Fundamentals & Styling** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites
- Box model from Day 2

## 🎯 Today's Goals
- Master CSS color systems (hex, rgb, hsl)
- Learn typography properties
- Import and use Google Fonts
- Use icon libraries (Font Awesome)

---

## 📖 Lesson Content

### 1. CSS Colors

```css
.element {
    /* Named colors */
    color: red;
    color: dodgerblue;

    /* Hexadecimal */
    color: #FF5733;         /* full hex */
    color: #333;            /* shorthand */

    /* RGB / RGBA */
    color: rgb(255, 87, 51);
    color: rgba(255, 87, 51, 0.5);  /* 50% opacity */

    /* HSL / HSLA */
    color: hsl(14, 100%, 60%);
    color: hsla(14, 100%, 60%, 0.8);
}
```

### 2. Typography Properties

```css
.text {
    /* Font family */
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;

    /* Font size */
    font-size: 16px;
    font-size: 1.2rem;

    /* Font weight */
    font-weight: 400;        /* normal */
    font-weight: 700;        /* bold */

    /* Font style */
    font-style: italic;

    /* Line height */
    line-height: 1.6;

    /* Letter & word spacing */
    letter-spacing: 1px;
    word-spacing: 3px;

    /* Text alignment */
    text-align: center;       /* left, right, center, justify */

    /* Text decoration */
    text-decoration: none;     /* underline, overline, line-through */

    /* Text transform */
    text-transform: uppercase; /* lowercase, capitalize */
}
```

### 3. Google Fonts

```html
<!-- In HTML <head> -->
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap" rel="stylesheet">
```

```css
/* In CSS */
body {
    font-family: 'Poppins', sans-serif;
}

h1 {
    font-weight: 700;
}

p {
    font-weight: 300;
    line-height: 1.8;
}
```

### 4. Icon Libraries

```html
<!-- Font Awesome CDN -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<!-- Usage -->
<i class="fas fa-home"></i> Home
<i class="fas fa-envelope"></i> Email
<i class="fab fa-github"></i> GitHub
```

---

## ✍️ Hands-On Exercise
1. Create a color palette section with 5 colors displayed as boxes
2. Apply Google Fonts (Poppins) to your bio page
3. Style headings, body text, and links with proper typography
4. Add Font Awesome icons to your navigation and contact section

---

## 📝 Homework
- Browse [Google Fonts](https://fonts.google.com/) and pick 2 complementary fonts
- Create a color scheme using [Coolors](https://coolors.co/)
- Apply them to your bio page

---

## 💡 Tips & Best Practices
- 🔤 Use maximum 2-3 font families per website
- 📏 Set body `line-height: 1.6` for readability
- 🎨 Use HSL colors for easier adjustments
- ♿ Ensure color contrast meets accessibility standards (4.5:1 ratio)

---

[← Previous Day](day-02.md) | [Back to Week 2](README.md) | [Next Day →](day-04.md)
