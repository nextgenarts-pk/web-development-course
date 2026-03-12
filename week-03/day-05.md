# 📅 Day 5: Responsive Images & Media 🖼️

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Flexbox and CSS Grid fundamentals
- Understanding of media queries basics

---

## 🎯 Today's Goals

- 🖼️ Make images responsive and performant
- 🎬 Handle responsive video and embeds
- 📐 Use `object-fit` and `aspect-ratio` for consistent sizing
- 🔧 Understand `srcset` and `<picture>` for art direction

---

## 📘 Lesson Content

### Basic Responsive Images

```css
/* The foundation — every project should have this */
img {
  max-width: 100%;
  height: auto;
  display: block;
}
```

### `object-fit` Property

Controls how an image fills its container (like `background-size` for `<img>`):

```css
.thumbnail {
  width: 300px;
  height: 200px;
  object-fit: cover;    /* Fills container, crops excess */
  /* cover | contain | fill | none | scale-down */
  object-position: center top; /* Control crop position */
}
```

### `aspect-ratio` Property

```css
.card__image {
  width: 100%;
  aspect-ratio: 16 / 9;    /* Maintains 16:9 ratio */
  object-fit: cover;
}

.avatar {
  width: 80px;
  aspect-ratio: 1 / 1;     /* Perfect square */
  border-radius: 50%;
  object-fit: cover;
}
```

### `srcset` for Resolution Switching

```html
<img
  src="photo-800.jpg"
  srcset="
    photo-400.jpg 400w,
    photo-800.jpg 800w,
    photo-1200.jpg 1200w
  "
  sizes="
    (max-width: 600px) 400px,
    (max-width: 1000px) 800px,
    1200px
  "
  alt="Responsive photo"
>
```

### `<picture>` for Art Direction

```html
<picture>
  <!-- Mobile: cropped portrait version -->
  <source media="(max-width: 600px)" srcset="photo-mobile.jpg">
  <!-- Tablet: medium crop -->
  <source media="(max-width: 1024px)" srcset="photo-tablet.jpg">
  <!-- Desktop: full landscape -->
  <img src="photo-desktop.jpg" alt="Responsive photo">
</picture>
```

### Responsive Video Embeds

```css
/* Responsive embedded video (YouTube, Vimeo) */
.video-wrapper {
  position: relative;
  width: 100%;
  aspect-ratio: 16 / 9;
}

.video-wrapper iframe {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: none;
}
```

```html
<div class="video-wrapper">
  <iframe src="https://www.youtube.com/embed/VIDEO_ID"
          allowfullscreen
          title="Video Title"></iframe>
</div>
```

### Lazy Loading Images

```html
<!-- Native lazy loading — built into the browser -->
<img src="photo.jpg" alt="Description" loading="lazy">

<!-- Eager loading for above-the-fold images -->
<img src="hero.jpg" alt="Hero image" loading="eager">
```

### Image Gallery with Grid

```css
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}

.gallery__item {
  overflow: hidden;
  border-radius: 8px;
}

.gallery__item img {
  width: 100%;
  aspect-ratio: 4 / 3;
  object-fit: cover;
  transition: transform 0.3s;
}

.gallery__item img:hover {
  transform: scale(1.05);
}
```

---

## 🛠️ Hands-On Exercise

### Build a Responsive Image Gallery

Create a photo gallery page with:
- A hero image at the top (full width, `aspect-ratio: 21/9`)
- A grid of photos using `auto-fit` + `minmax()`
- All images use `object-fit: cover` and `aspect-ratio`
- Hover zoom effect on gallery images
- Lazy loading on all images below the fold

---

## 📝 Homework

1. Build a **video section** with 3 responsive YouTube embeds in a grid layout
2. Create a **team page** with circular avatars using `aspect-ratio: 1/1` and `object-fit: cover`
3. Implement a **hero section** with full-width background image that works on all screen sizes
4. Add `loading="lazy"` to all below-the-fold images in your gallery

---

## 💡 Pro Tips

> 📏 **aspect-ratio:** This single property replaces the old "padding-top hack" for maintaining ratios. Use it wherever you need consistent image/video dimensions.

> ⚡ **Performance:** Always use `loading="lazy"` for images below the fold. It can significantly reduce initial page load time.

> 🎨 **object-fit: cover** is your best friend for image grids — it ensures images fill their containers without distortion.

---

[← Day 4](day-04.md) | [Back to Week 3](README.md) | [Day 6 →](day-06.md)
