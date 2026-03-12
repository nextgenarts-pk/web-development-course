# 📅 Day 5: HTML Forms, Inputs & Tables 📋

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- Semantic HTML from Day 4
- Comfortable with HTML document structure

---

## 🎯 Today's Goals

- Build HTML forms with various input types
- Understand form attributes and validation
- Create HTML tables for structured data
- Learn form accessibility best practices

---

## 📖 Lesson Content

### 1. HTML Forms Basics

```html
<form action="/submit" method="POST">
    <label for="name">Full Name:</label>
    <input type="text" id="name" name="name" placeholder="Enter your name" required>

    <label for="email">Email:</label>
    <input type="email" id="email" name="email" placeholder="you@example.com" required>

    <button type="submit">Submit</button>
</form>
```

**Form Attributes:**
- `action` — URL where form data is sent
- `method` — HTTP method (GET or POST)
- `required` — Field must be filled before submitting
- `placeholder` — Hint text inside the input

### 2. Input Types

```html
<!-- Text Inputs -->
<input type="text" placeholder="Your name">
<input type="email" placeholder="your@email.com">
<input type="password" placeholder="Password">
<input type="tel" placeholder="Phone number">
<input type="url" placeholder="https://yoursite.com">
<input type="number" min="1" max="100">
<input type="search" placeholder="Search...">

<!-- Date & Time -->
<input type="date">
<input type="time">
<input type="datetime-local">

<!-- Selection -->
<input type="checkbox" id="agree">
<label for="agree">I agree to terms</label>

<input type="radio" name="gender" value="male" id="male">
<label for="male">Male</label>
<input type="radio" name="gender" value="female" id="female">
<label for="female">Female</label>

<input type="range" min="0" max="100">
<input type="color">
<input type="file">

<!-- Buttons -->
<button type="submit">Submit</button>
<button type="reset">Reset</button>
```

### 3. Select Dropdown & Textarea

```html
<!-- Dropdown -->
<label for="course">Select Course:</label>
<select id="course" name="course">
    <option value="">-- Choose a course --</option>
    <option value="webdev">Web Development</option>
    <option value="design">Graphic Design</option>
    <option value="ai">AI & Machine Learning</option>
</select>

<!-- Multi-line text -->
<label for="message">Your Message:</label>
<textarea id="message" name="message" rows="5" cols="40" placeholder="Write your message here..."></textarea>
```

### 4. Complete Registration Form

```html
<form action="/register" method="POST">
    <fieldset>
        <legend>Student Registration</legend>

        <label for="fullname">Full Name:</label>
        <input type="text" id="fullname" name="fullname" required>

        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>

        <label for="phone">Phone:</label>
        <input type="tel" id="phone" name="phone">

        <label for="dob">Date of Birth:</label>
        <input type="date" id="dob" name="dob">

        <label for="course">Course:</label>
        <select id="course" name="course" required>
            <option value="">Select a course</option>
            <option value="fullstack">Full Stack Web Development</option>
            <option value="frontend">Frontend Development</option>
        </select>

        <label for="bio">Tell us about yourself:</label>
        <textarea id="bio" name="bio" rows="4"></textarea>

        <label>
            <input type="checkbox" name="terms" required>
            I agree to the terms and conditions
        </label>

        <button type="submit">Register</button>
    </fieldset>
</form>
```

### 5. HTML Tables

```html
<table>
    <caption>Course Schedule — Week 1</caption>
    <thead>
        <tr>
            <th>Day</th>
            <th>Topic</th>
            <th>Duration</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Monday</td>
            <td>How the Internet Works</td>
            <td>2 hours</td>
        </tr>
        <tr>
            <td>Tuesday</td>
            <td>Setting Up VS Code & Git</td>
            <td>2 hours</td>
        </tr>
        <tr>
            <td>Wednesday</td>
            <td>HTML5 Fundamentals</td>
            <td>2 hours</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td colspan="2"><strong>Total</strong></td>
            <td><strong>6 hours</strong></td>
        </tr>
    </tfoot>
</table>
```

---

## ✍️ Hands-On Exercise

Build a **Student Registration Form** that includes:
1. Text inputs for name, email, phone
2. Date input for date of birth
3. Radio buttons for gender
4. Dropdown for course selection
5. Checkbox for terms agreement
6. Textarea for additional notes
7. Submit and Reset buttons
8. A table showing the weekly schedule

---

## 📝 Homework

- Read: [MDN — HTML Forms Guide](https://developer.mozilla.org/en-US/docs/Learn/Forms)
- Practice: Create a contact form with validation attributes
- Challenge: Build a survey form with at least 10 different input types

---

## 💡 Tips & Best Practices

- 🏷️ Always use `<label>` with `for` attribute linked to input `id`
- ✅ Use HTML5 validation attributes (`required`, `minlength`, `pattern`)
- ♿ Use `<fieldset>` and `<legend>` to group related form controls
- 📊 Always use `<thead>`, `<tbody>`, `<tfoot>` in tables

---

[← Previous Day](day-04.md) | [Back to Week 1](README.md) | [Next Day →](day-06.md)
