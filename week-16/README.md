# 📅 Week 16 — Authentication & Security 🔐

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Week Overview

Master **user authentication** and **API security** — the skills that separate hobby projects from production applications. Build a complete authentication system with registration, login, JWT tokens, protected routes, role-based access control, and security best practices.

---

## 📋 Learning Objectives

By the end of this week, you will:

- Hash passwords securely with **bcrypt**
- Generate and verify **JSON Web Tokens (JWT)**
- Build **registration and login** endpoints
- Protect routes with **authentication middleware**
- Implement **role-based access control** (RBAC)
- Apply security best practices against common attacks
- Build a **Secure User Auth System** as a capstone project

---

## 📅 Daily Breakdown

| Day | Topic | Key Concepts |
|-----|-------|-------------|
| 1 | User Model & Password Hashing | bcrypt, User schema, password validation, pre-save hooks |
| 2 | JWT Authentication | jsonwebtoken, sign/verify, login/signup endpoints, token flow |
| 3 | Protected Routes & Auth Middleware | Token extraction, route protection, current user, ownership |
| 4 | Role-Based Access Control | Roles & permissions, admin routes, resource ownership |
| 5 | Security Best Practices | XSS, CSRF, injection, rate limiting, security headers |
| 6 | **Project Day** | Secure User Auth System |

---

## 🛠️ Technologies

- **bcrypt** — Password hashing
- **jsonwebtoken** — JWT creation & verification
- **express** — HTTP server & middleware
- **mongoose** — MongoDB ODM with User model
- **helmet** — Security headers
- **express-rate-limit** — Brute-force protection
- **cookie-parser** — Secure cookie handling

---

## 📁 Key Concepts

### Authentication vs Authorization
- **Authentication**: *Who are you?* — Verify identity (login)
- **Authorization**: *What can you do?* — Check permissions (roles)

### JWT Flow
1. User sends credentials → Server verifies → Server issues JWT
2. Client stores token → Sends in `Authorization: Bearer <token>` header
3. Server verifies token on each request → Grants/denies access

### Security Layers
- Input validation & sanitization
- Password hashing (never store plain text)
- JWT with expiration
- Rate limiting on auth routes
- Security headers (helmet)
- HTTPS in production

---

[Back to Course](../courseoutline.md) | [Day 1 →](day-01.md)
