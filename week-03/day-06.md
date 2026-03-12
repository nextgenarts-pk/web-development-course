# 📅 Day 6: Project Day — Responsive Portfolio Layout 🚀

> **Week 3 — CSS Layouts: Flexbox & Grid** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Flexbox containers, items, alignment
- CSS Grid, named areas, `auto-fit`, `minmax()`
- Responsive images, `object-fit`, `aspect-ratio`

---

## 🎯 Today's Goals

- 🚀 Build a complete responsive portfolio layout
- 🧭 Use Flexbox for the navigation bar
- 🗂️ Use CSS Grid for the page layout and project gallery
- 📱 Make everything fully responsive (mobile, tablet, desktop)

---

## 📘 Project Specifications

### Portfolio Layout Requirements

| Section | Layout Method | Requirements |
|---------|--------------|--------------|
| Navbar | Flexbox | Logo, links, CTA button, sticky |
| Hero | Flexbox | Centered text, background image |
| About | Grid (2 col) | Image + text side by side |
| Skills | Flexbox | Skill tags/badges |
| Projects | Grid auto-fit | Responsive card grid |
| Contact | Grid (2 col) | Form + info |
| Footer | Flexbox | Links, social, copyright |

### Breakpoints

```css
/* Mobile First */
/* Default: 0 - 767px (mobile) */
/* Tablet: 768px - 1023px */
/* Desktop: 1024px+ */
```

---

## 📘 Project Walkthrough

### HTML Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Portfolio — Your Name</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <nav class="navbar">
    <div class="navbar__logo">YourName</div>
    <ul class="navbar__links">
      <li><a href="#about">About</a></li>
      <li><a href="#skills">Skills</a></li>
      <li><a href="#projects">Projects</a></li>
      <li><a href="#contact">Contact</a></li>
    </ul>
    <a href="#contact" class="btn btn--primary">Hire Me</a>
  </nav>

  <section class="hero">
    <h1>Hi, I'm <span class="highlight">Your Name</span></h1>
    <p>Full Stack Web Developer</p>
  </section>

  <section id="about" class="about">
    <img src="https://picsum.photos/400/400" alt="Profile" class="about__image">
    <div class="about__text">
      <h2>About Me</h2>
      <p>I am a passionate web developer from Gilgit-Baltistan...</p>
    </div>
  </section>

  <section id="skills" class="skills">
    <h2>My Skills</h2>
    <div class="skills__grid">
      <span class="skill-tag">HTML5</span>
      <span class="skill-tag">CSS3</span>
      <span class="skill-tag">JavaScript</span>
      <span class="skill-tag">React</span>
      <span class="skill-tag">Node.js</span>
      <span class="skill-tag">MongoDB</span>
    </div>
  </section>

  <section id="projects" class="projects">
    <h2>My Projects</h2>
    <div class="projects__grid">
      <!-- 4-6 project cards -->
      <article class="project-card">
        <img src="https://picsum.photos/400/250?random=1" alt="Project">
        <div class="project-card__body">
          <h3>Project Title</h3>
          <p>Brief description of the project.</p>
          <div class="project-card__tags">
            <span>HTML</span><span>CSS</span><span>JS</span>
          </div>
          <div class="project-card__links">
            <a href="#">Live Demo</a>
            <a href="#">GitHub</a>
          </div>
        </div>
      </article>
    </div>
  </section>

  <section id="contact" class="contact">
    <div class="contact__form">
      <h2>Get In Touch</h2>
      <form>
        <input type="text" placeholder="Your Name" required>
        <input type="email" placeholder="Your Email" required>
        <textarea placeholder="Your Message" rows="5" required></textarea>
        <button type="submit" class="btn btn--primary">Send Message</button>
      </form>
    </div>
    <div class="contact__info">
      <h3>Contact Info</h3>
      <p>📧 email@example.com</p>
      <p>📍 Gilgit-Baltistan, Pakistan</p>
    </div>
  </section>

  <footer class="footer">
    <p>© 2025 YourName. Built with ❤️ at NextGen Arts</p>
  </footer>
</body>
</html>
```

### Key CSS Patterns

```css
/* === Base Reset === */
*, *::before, *::after { margin: 0; padding: 0; box-sizing: border-box; }
img { max-width: 100%; height: auto; display: block; }

/* === Navbar (Flexbox) === */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  position: sticky;
  top: 0;
  background: white;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  z-index: 100;
}

/* === Hero (Flexbox centering) === */
.hero {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  min-height: 80vh;
  text-align: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
}

/* === About (Grid 2-column) === */
.about {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
  padding: 4rem 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

@media (min-width: 768px) {
  .about {
    grid-template-columns: 300px 1fr;
    align-items: center;
  }
}

/* === Projects Grid (auto-responsive) === */
.projects__grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
}

/* === Contact (Grid 2-column) === */
.contact {
  display: grid;
  grid-template-columns: 1fr;
  gap: 2rem;
  padding: 4rem 2rem;
  max-width: 1000px;
  margin: 0 auto;
}

@media (min-width: 768px) {
  .contact { grid-template-columns: 2fr 1fr; }
}

/* === Footer (Flexbox) === */
.footer {
  display: flex;
  justify-content: center;
  padding: 2rem;
  background: #2c3e50;
  color: white;
}
```

---

## ✅ Completion Checklist

- [ ] Sticky navigation bar with Flexbox
- [ ] Hero section with centered content
- [ ] About section with 2-column Grid layout
- [ ] Skills section with Flexbox tag badges
- [ ] Projects section with auto-responsive Grid
- [ ] Contact section with form + info
- [ ] Footer with centered content
- [ ] All images use `object-fit` and `aspect-ratio`
- [ ] Mobile-first responsive design
- [ ] Smooth hover effects on cards and buttons
- [ ] Clean, consistent spacing throughout

---

## 📝 Homework

1. **Complete** the portfolio with full styling and content
2. Add **smooth scrolling** (`html { scroll-behavior: smooth; }`)
3. Add a **back-to-top button** fixed at the bottom right
4. Push your portfolio to **GitHub** and deploy on **GitHub Pages**

---

## 💡 Pro Tips

> 🎯 **Layout Strategy:** Grid for page structure + sections, Flexbox for components inside sections. This is the modern standard approach.

> 🚀 **GitHub Pages:** Free hosting for static sites! Push to a `gh-pages` branch or enable Pages in your repo settings.

---

[← Day 5](day-05.md) | [Back to Week 3](README.md) | [Week 4 →](../week-04/README.md)
