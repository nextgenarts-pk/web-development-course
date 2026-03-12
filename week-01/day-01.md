# 📅 Day 1: How the Internet Works — DNS, HTTP & Browsers 🌐

> **Week 1: Introduction to Web Development & HTML Basics** | [NextGen Arts](https://nextgenarts.pk)

---

## 📋 Prerequisites

- A working computer with internet access
- Curiosity about how websites work!

---

## 🎯 Today's Goals

- Understand what the internet is and how it works
- Learn the client-server model
- Understand DNS, HTTP/HTTPS protocols
- Know what a web browser does behind the scenes

---

## 📖 Lesson Content

### 1. What is the Internet?

The internet is a global network of interconnected computers that communicate using standardized protocols. When you visit a website, your computer sends a request to another computer (server) that sends back the website data.

```
Your Computer (Client)  ──→  Internet  ──→  Web Server
        ↑                                       │
        └───────────── Response ←───────────────┘
```

### 2. Client-Server Model

- **Client:** Your web browser (Chrome, Firefox, Edge)
- **Server:** A computer that stores and serves website files
- **Request:** Client asks the server for a webpage
- **Response:** Server sends back HTML, CSS, JS files

### 3. How DNS Works

DNS (Domain Name System) translates human-readable domain names into IP addresses.

```
You type: www.google.com
    ↓
DNS Server looks up the IP: 142.250.80.46
    ↓
Your browser connects to that IP address
    ↓
Google's server sends back the webpage
```

### 4. HTTP & HTTPS

- **HTTP** (HyperText Transfer Protocol) — Rules for transferring web data
- **HTTPS** — Secure version with encryption (SSL/TLS)
- **HTTP Methods:** GET (retrieve), POST (send data), PUT (update), DELETE (remove)

### 5. What Happens When You Visit a Website?

```
1. You type a URL (e.g., https://nextgenarts.pk)
2. Browser asks DNS for the IP address
3. Browser sends an HTTP GET request to the server
4. Server processes the request
5. Server sends back HTML, CSS, JS files
6. Browser renders the webpage on your screen
```

### 6. Web Browsers

- Chrome, Firefox, Safari, Edge
- **Browser Engine:** Renders HTML/CSS (Blink, Gecko, WebKit)
- **JavaScript Engine:** Executes JS code (V8, SpiderMonkey)
- **DevTools:** Built-in developer tools for inspecting pages

---

## ✍️ Hands-On Exercise

1. Open Chrome and press `F12` to open Developer Tools
2. Go to the **Network** tab
3. Visit any website and observe the HTTP requests
4. Find the **Status Code** (200 = success, 404 = not found)
5. Click on a request and examine the **Headers**

---

## 📝 Homework

- Watch: [How the Internet Works in 5 Minutes](https://www.youtube.com/watch?v=7_LPdttKXPc)
- Read: [MDN — How the Web Works](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/How_the_Web_works)
- Write down 5 things you learned about how the internet works

---

## 💡 Tips & Best Practices

- 🔒 Always look for **HTTPS** (padlock icon) when visiting websites
- 🧪 Developer Tools are your best friend — get comfortable using them early
- 📝 Take notes by hand — it improves retention

---

[← Previous Day](../README.md) | [Back to Week 1](README.md) | [Next Day →](day-02.md)
