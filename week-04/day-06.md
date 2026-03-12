# 📅 Day 6: Project Day — Tailwind Portfolio Site 🚀

> **Week 4 — Responsive Design & Tailwind CSS** | [NextGen Arts](https://nextgenarts.pk)

---

## ✅ Prerequisites

- Tailwind CSS utilities, responsive prefixes, dark mode
- All concepts from Weeks 1-4

---

## 🎯 Today's Goals

- 🚀 Build a complete portfolio site using only Tailwind CSS
- 🌙 Include dark mode toggle
- 📱 Fully responsive across all breakpoints
- 🎨 Use Tailwind components and state modifiers throughout

---

## 📘 Project Specifications

### Sections Required

| Section | Key Tailwind Features |
|---------|----------------------|
| Navbar | `flex`, `justify-between`, `hidden md:flex`, sticky |
| Hero | Gradient bg, responsive text, centered flexbox |
| About | `grid md:grid-cols-2`, `object-cover` for avatar |
| Skills | `flex flex-wrap gap-3`, skill badges |
| Projects | `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3` |
| Contact | `grid md:grid-cols-2`, form styling |
| Footer | `flex`, social icons |
| Dark Mode | `dark:` variants throughout |

---

## 📘 Project Template

```html
<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            brand: { 500: '#3b82f6', 600: '#2563eb', 700: '#1d4ed8' }
          }
        }
      }
    }
  </script>
  <title>Portfolio | Your Name</title>
</head>
<body class="bg-gray-50 dark:bg-gray-900 text-gray-900 dark:text-gray-100 transition-colors duration-300">

  <!-- Navbar -->
  <nav class="sticky top-0 z-50 bg-white/80 dark:bg-gray-900/80 backdrop-blur-md shadow-sm">
    <div class="max-w-6xl mx-auto px-4 py-4 flex items-center justify-between">
      <a href="#" class="text-2xl font-bold text-blue-600">YourName</a>
      <ul class="hidden md:flex gap-8">
        <li><a href="#about" class="hover:text-blue-500 transition">About</a></li>
        <li><a href="#skills" class="hover:text-blue-500 transition">Skills</a></li>
        <li><a href="#projects" class="hover:text-blue-500 transition">Projects</a></li>
        <li><a href="#contact" class="hover:text-blue-500 transition">Contact</a></li>
      </ul>
      <div class="flex items-center gap-4">
        <button onclick="document.documentElement.classList.toggle('dark')"
                class="p-2 rounded-full bg-gray-200 dark:bg-gray-700 hover:bg-gray-300 dark:hover:bg-gray-600 transition">
          🌙
        </button>
        <a href="#contact"
           class="hidden md:inline-block bg-blue-600 text-white px-5 py-2 rounded-lg hover:bg-blue-700 transition">
          Hire Me
        </a>
      </div>
    </div>
  </nav>

  <!-- Hero -->
  <section class="min-h-[80vh] flex items-center justify-center bg-gradient-to-br from-blue-600 to-purple-700 text-white">
    <div class="text-center px-4">
      <p class="text-lg md:text-xl mb-4 opacity-80">Hello, I'm</p>
      <h1 class="text-4xl md:text-6xl lg:text-7xl font-bold mb-6">Your Name</h1>
      <p class="text-xl md:text-2xl mb-8 opacity-90">Full Stack Web Developer</p>
      <div class="flex gap-4 justify-center">
        <a href="#projects" class="bg-white text-blue-600 px-8 py-3 rounded-lg font-semibold hover:bg-gray-100 transition">
          View Work
        </a>
        <a href="#contact" class="border-2 border-white text-white px-8 py-3 rounded-lg font-semibold hover:bg-white/10 transition">
          Contact Me
        </a>
      </div>
    </div>
  </section>

  <!-- About -->
  <section id="about" class="py-16 md:py-24">
    <div class="max-w-6xl mx-auto px-4 grid grid-cols-1 md:grid-cols-2 gap-12 items-center">
      <img src="https://picsum.photos/400/400" alt="Profile"
           class="w-64 h-64 md:w-80 md:h-80 rounded-full object-cover mx-auto shadow-xl">
      <div>
        <h2 class="text-3xl md:text-4xl font-bold mb-6">About Me</h2>
        <p class="text-gray-600 dark:text-gray-300 leading-relaxed mb-4">
          I am a passionate web developer from Gilgit-Baltistan, Pakistan.
          I love building beautiful, functional websites and applications.
        </p>
        <p class="text-gray-600 dark:text-gray-300 leading-relaxed">
          Currently learning Full Stack Web Development at NextGen Arts,
          integrating AI-powered features into modern web applications.
        </p>
      </div>
    </div>
  </section>

  <!-- Skills -->
  <section id="skills" class="py-16 bg-gray-100 dark:bg-gray-800">
    <div class="max-w-6xl mx-auto px-4 text-center">
      <h2 class="text-3xl md:text-4xl font-bold mb-12">My Skills</h2>
      <div class="flex flex-wrap justify-center gap-3">
        <span class="px-4 py-2 bg-orange-100 text-orange-700 dark:bg-orange-900/30 dark:text-orange-300 rounded-full font-medium">HTML5</span>
        <span class="px-4 py-2 bg-blue-100 text-blue-700 dark:bg-blue-900/30 dark:text-blue-300 rounded-full font-medium">CSS3</span>
        <span class="px-4 py-2 bg-cyan-100 text-cyan-700 dark:bg-cyan-900/30 dark:text-cyan-300 rounded-full font-medium">Tailwind CSS</span>
        <span class="px-4 py-2 bg-yellow-100 text-yellow-700 dark:bg-yellow-900/30 dark:text-yellow-300 rounded-full font-medium">JavaScript</span>
        <span class="px-4 py-2 bg-sky-100 text-sky-700 dark:bg-sky-900/30 dark:text-sky-300 rounded-full font-medium">React</span>
        <span class="px-4 py-2 bg-green-100 text-green-700 dark:bg-green-900/30 dark:text-green-300 rounded-full font-medium">Node.js</span>
        <span class="px-4 py-2 bg-emerald-100 text-emerald-700 dark:bg-emerald-900/30 dark:text-emerald-300 rounded-full font-medium">MongoDB</span>
      </div>
    </div>
  </section>

  <!-- Projects -->
  <section id="projects" class="py-16 md:py-24">
    <div class="max-w-6xl mx-auto px-4">
      <h2 class="text-3xl md:text-4xl font-bold text-center mb-12">My Projects</h2>
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        <!-- Project Card -->
        <div class="bg-white dark:bg-gray-800 rounded-xl shadow-lg overflow-hidden group hover:shadow-xl transition duration-300">
          <div class="overflow-hidden">
            <img src="https://picsum.photos/400/250?random=1" alt="Project"
                 class="w-full h-48 object-cover group-hover:scale-105 transition duration-500">
          </div>
          <div class="p-6">
            <h3 class="text-xl font-bold mb-2">Project Name</h3>
            <p class="text-gray-500 dark:text-gray-400 text-sm mb-4">A brief description of this project.</p>
            <div class="flex flex-wrap gap-2 mb-4">
              <span class="text-xs bg-blue-100 dark:bg-blue-900/30 text-blue-600 dark:text-blue-300 px-2 py-1 rounded">HTML</span>
              <span class="text-xs bg-blue-100 dark:bg-blue-900/30 text-blue-600 dark:text-blue-300 px-2 py-1 rounded">CSS</span>
              <span class="text-xs bg-blue-100 dark:bg-blue-900/30 text-blue-600 dark:text-blue-300 px-2 py-1 rounded">JS</span>
            </div>
            <div class="flex gap-4">
              <a href="#" class="text-blue-600 hover:underline text-sm font-medium">Live Demo →</a>
              <a href="#" class="text-gray-500 hover:underline text-sm font-medium">GitHub →</a>
            </div>
          </div>
        </div>
        <!-- Repeat for more project cards -->
      </div>
    </div>
  </section>

  <!-- Contact -->
  <section id="contact" class="py-16 bg-gray-100 dark:bg-gray-800">
    <div class="max-w-4xl mx-auto px-4 grid grid-cols-1 md:grid-cols-2 gap-12">
      <div>
        <h2 class="text-3xl font-bold mb-6">Get In Touch</h2>
        <form class="space-y-4">
          <input type="text" placeholder="Your Name"
                 class="w-full px-4 py-3 rounded-lg border border-gray-300 dark:border-gray-600 dark:bg-gray-700
                        focus:border-blue-500 focus:ring-2 focus:ring-blue-200 outline-none transition">
          <input type="email" placeholder="Your Email"
                 class="w-full px-4 py-3 rounded-lg border border-gray-300 dark:border-gray-600 dark:bg-gray-700
                        focus:border-blue-500 focus:ring-2 focus:ring-blue-200 outline-none transition">
          <textarea rows="5" placeholder="Your Message"
                    class="w-full px-4 py-3 rounded-lg border border-gray-300 dark:border-gray-600 dark:bg-gray-700
                           focus:border-blue-500 focus:ring-2 focus:ring-blue-200 outline-none transition resize-none"></textarea>
          <button class="w-full bg-blue-600 text-white py-3 rounded-lg font-semibold
                         hover:bg-blue-700 active:scale-[0.98] transition">
            Send Message
          </button>
        </form>
      </div>
      <div class="space-y-6">
        <h3 class="text-xl font-bold">Contact Info</h3>
        <p class="flex items-center gap-3 text-gray-600 dark:text-gray-300">📧 email@example.com</p>
        <p class="flex items-center gap-3 text-gray-600 dark:text-gray-300">📍 Gilgit-Baltistan, Pakistan</p>
        <p class="flex items-center gap-3 text-gray-600 dark:text-gray-300">🌐 nextgenarts.pk</p>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="bg-gray-900 text-gray-400 py-8">
    <div class="max-w-6xl mx-auto px-4 text-center">
      <p>© 2025 YourName. Built with ❤️ at <a href="https://nextgenarts.pk" class="text-blue-400 hover:underline">NextGen Arts</a></p>
    </div>
  </footer>

</body>
</html>
```

---

## ✅ Completion Checklist

- [ ] Sticky glassmorphism navbar with dark mode toggle
- [ ] Hero section with gradient background
- [ ] About section: responsive 2-column grid
- [ ] Skills section with colored badges
- [ ] Projects grid: responsive 1→2→3 columns
- [ ] Contact section with styled form + info
- [ ] Footer with links
- [ ] Dark mode works across all sections
- [ ] Hover effects on all interactive elements
- [ ] Fully responsive: mobile → tablet → desktop
- [ ] Smooth scrolling between sections

---

## 📝 Homework

1. **Complete** the portfolio with your real information and at least 4 project cards
2. Add a **mobile hamburger menu** with JavaScript toggle
3. Convert to **production Tailwind** (CLI + purge unused CSS)
4. Deploy to **GitHub Pages** or **Vercel**

---

## 💡 Pro Tips

> 🎨 **backdrop-blur-md** + **bg-white/80:** Creates a beautiful glassmorphism effect for sticky navbars — content is visible through the transparent header.

> 🚀 **Phase 1 Complete!** You now know HTML, CSS, Flexbox, Grid, responsive design, and Tailwind CSS. You have the foundation to build any static website. Next up: JavaScript!

---

[← Day 5](day-05.md) | [Back to Week 4](README.md) | [Week 5 →](../week-05/README.md)
