# 📅 Day 6: HTML Media, Embeds & Project Day 🎬

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- All HTML concepts from Days 3-5
- Student registration form from Day 5

---

## 🎯 Today's Goals

- Embed audio, video, and iframe content in HTML
- Learn HTML5 media elements and attributes
- Build and complete the Week 1 project: **Personal Bio Page**
- Push the project to GitHub

---

## 📖 Lesson Content

### 1. HTML Video Element

```html
<!-- Local video file -->
<video width="640" height="360" controls>
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    Your browser does not support the video tag.
</video>

<!-- With additional attributes -->
<video width="640" controls autoplay muted loop poster="thumbnail.jpg">
    <source src="intro.mp4" type="video/mp4">
</video>
```

**Video Attributes:**
- `controls` — Show play/pause buttons
- `autoplay` — Start playing automatically
- `muted` — Start muted (required for autoplay in most browsers)
- `loop` — Replay continuously
- `poster` — Thumbnail image before playing

### 2. HTML Audio Element

```html
<audio controls>
    <source src="music.mp3" type="audio/mpeg">
    <source src="music.ogg" type="audio/ogg">
    Your browser does not support the audio tag.
</audio>
```

### 3. Embedding YouTube Videos

```html
<!-- YouTube embed using iframe -->
<iframe
    width="560"
    height="315"
    src="https://www.youtube.com/embed/VIDEO_ID"
    title="YouTube video player"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
    allowfullscreen>
</iframe>
```

### 4. Embedding Google Maps

```html
<iframe
    src="https://www.google.com/maps/embed?pb=!1m18..."
    width="600"
    height="450"
    style="border:0;"
    allowfullscreen=""
    loading="lazy"
    title="Location Map">
</iframe>
```

### 5. HTML Favicon

```html
<head>
    <link rel="icon" type="image/png" href="favicon.png">
    <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
```

---

## 🚀 Week 1 Project: Personal Bio Page

Build a complete **Personal Bio Page** using everything you've learned. Your page should include:

### Requirements:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Your Name — Personal Bio</title>
    <link rel="icon" href="favicon.png">
</head>
<body>

    <!-- Header with name and navigation -->
    <header>
        <h1>Your Full Name</h1>
        <nav>
            <ul>
                <li><a href="#about">About</a></li>
                <li><a href="#skills">Skills</a></li>
                <li><a href="#education">Education</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <!-- About section with photo -->
        <section id="about">
            <h2>About Me</h2>
            <figure>
                <img src="your-photo.jpg" alt="Your Name" width="200">
                <figcaption>Your Name — Aspiring Web Developer</figcaption>
            </figure>
            <p>Write 2-3 paragraphs about yourself...</p>
        </section>

        <!-- Skills list -->
        <section id="skills">
            <h2>Skills I'm Learning</h2>
            <ul>
                <li>HTML5</li>
                <li>CSS3</li>
                <li>JavaScript</li>
                <li>React.js</li>
                <li>Node.js</li>
            </ul>
        </section>

        <!-- Education table -->
        <section id="education">
            <h2>Education</h2>
            <table>
                <thead>
                    <tr>
                        <th>Year</th>
                        <th>Degree</th>
                        <th>Institution</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>2026</td>
                        <td>Web Development Certificate</td>
                        <td>NextGen Arts</td>
                    </tr>
                </tbody>
            </table>
        </section>

        <!-- Contact form -->
        <section id="contact">
            <h2>Contact Me</h2>
            <form>
                <label for="name">Name:</label>
                <input type="text" id="name" required>

                <label for="email">Email:</label>
                <input type="email" id="email" required>

                <label for="message">Message:</label>
                <textarea id="message" rows="4"></textarea>

                <button type="submit">Send Message</button>
            </form>
        </section>
    </main>

    <footer>
        <p>&copy; 2026 Your Name. All rights reserved.</p>
        <p>
            <a href="https://github.com/yourusername">GitHub</a> |
            <a href="https://linkedin.com/in/yourusername">LinkedIn</a>
        </p>
    </footer>

</body>
</html>
```

### Checklist:

- [ ] Proper HTML5 document structure
- [ ] Semantic HTML elements (header, nav, main, section, footer)
- [ ] Navigation menu with anchor links
- [ ] Personal photo with figure and figcaption
- [ ] Skills list (unordered list)
- [ ] Education table
- [ ] Contact form with validation
- [ ] Social media links in footer
- [ ] Pushed to GitHub

---

## 📝 Homework

- Complete the Personal Bio Page project
- Push to GitHub (commit with meaningful messages)
- Share your GitHub repo link with classmates
- Read ahead: [MDN — CSS First Steps](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps)

---

## 💡 Tips & Best Practices

- 🎯 Always test your page in multiple browsers
- 📱 Open your page on a mobile device to see how it looks
- ✅ Validate your HTML at [validator.w3.org](https://validator.w3.org/)
- 🔍 Use descriptive `alt` text for all images

---

[← Previous Day](day-05.md) | [Back to Week 1](README.md) | [Next Week →](../week-02/README.md)
