# 📅 Day 2: Setting Up VS Code, Git & GitHub 🛠️

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- Understanding of how the internet works (Day 1)
- A working computer with internet access

---

## 🎯 Today's Goals

- Install and configure VS Code as your code editor
- Install essential VS Code extensions
- Install Git and configure it
- Create a GitHub account and your first repository

---

## 📖 Lesson Content

### 1. Installing VS Code

1. Download from [code.visualstudio.com](https://code.visualstudio.com/)
2. Install with default settings
3. Open VS Code and explore the interface

**Key Areas:**
- **Explorer** (left sidebar) — File navigation
- **Editor** (center) — Where you write code
- **Terminal** (bottom) — Built-in command line
- **Extensions** (left sidebar) — Add-ons for productivity

### 2. Essential VS Code Extensions

Install these extensions from the Extensions panel (`Ctrl+Shift+X`):

| Extension | Purpose |
|-----------|---------|
| Live Server | Auto-refresh browser on save |
| Prettier | Automatic code formatting |
| Auto Rename Tag | Rename paired HTML tags |
| HTML CSS Support | CSS class autocomplete in HTML |
| Path Intellisense | File path autocomplete |
| Material Icon Theme | Beautiful file icons |

### 3. Installing Git

1. Download from [git-scm.com](https://git-scm.com/)
2. Install with default settings
3. Open terminal in VS Code and verify:

```bash
# Check Git version
git --version

# Configure your name and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Verify configuration
git config --list
```

### 4. Creating a GitHub Account

1. Go to [github.com](https://github.com/) and sign up
2. Choose a professional username (this will be on your portfolio!)
3. Add a profile picture and bio

### 5. Your First Repository

```bash
# Create a project folder
mkdir my-first-website
cd my-first-website

# Initialize Git
git init

# Create your first file
echo "Hello World" > index.html

# Stage the file
git add index.html

# Commit the file
git commit -m "Initial commit: Add index.html"
```

### 6. Pushing to GitHub

```bash
# Add remote origin (use your repo URL)
git remote add origin https://github.com/yourusername/my-first-website.git

# Push to GitHub
git push -u origin main
```

### 7. Essential Git Commands

```bash
git init          # Initialize a new repository
git add .         # Stage all changes
git commit -m ""  # Commit with a message
git status        # Check current status
git log           # View commit history
git push          # Push to remote repository
git pull          # Pull latest changes
```

---

## ✍️ Hands-On Exercise

1. Install VS Code and all recommended extensions
2. Install Git and configure your name/email
3. Create a GitHub account
4. Create a new repository called `web-dev-practice`
5. Create an `index.html` file with "Hello, World!"
6. Push it to GitHub

---

## 📝 Homework

- Customize your VS Code theme (try "One Dark Pro" or "GitHub Theme")
- Read: [Git Handbook (GitHub)](https://guides.github.com/introduction/git-handbook/)
- Practice: Make 3 more commits to your repository

---

## 💡 Tips & Best Practices

- ⌨️ Learn VS Code keyboard shortcuts — they'll save you hours
- 💬 Write meaningful commit messages (e.g., "Add navigation bar" not "update")
- 📁 Keep your projects organized in a dedicated folder

---

[← Previous Day](day-01.md) | [Back to Week 1](README.md) | [Next Day →](day-03.md)
