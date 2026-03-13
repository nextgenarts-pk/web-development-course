# 📋 Week 16 Assignments — Authentication & Security

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 📝 Daily Exercises

### Day 1 — Password Hashing
1. Create a **standalone script** that benchmarks bcrypt at rounds 8, 10, 12, 14 — log the time taken for each
2. Build a **User model** with name, email, password (hashed), phone (optional, regex validated)
3. Test the pre-save hook: create a user → update their name → verify password hash is unchanged

### Day 2 — JWT Authentication
1. Write a script that **signs**, **decodes**, and **verifies** a JWT token and logs each step
2. Build `/api/auth/signup` and `/api/auth/login` endpoints
3. Open [jwt.io](https://jwt.io) and decode your token — verify the payload contains only the user ID (no password)

### Day 3 — Protected Routes
1. Add `protect` middleware to `/api/auth/me`
2. Test: call the route without a token → verify 401 response
3. Test: call with a valid token → verify user data returned
4. Test: Change password → use old token → verify 401 (token invalidated)

### Day 4 — Role-Based Access
1. Create a **seeder** that generates 1 admin, 1 editor, and 3 regular users
2. Build an admin-only `GET /api/admin/users` endpoint
3. Test all three scenarios: admin ✅, editor ❌, user ❌

### Day 5 — Security
1. Test **NoSQL injection**: `{"email": {"$gt": ""}}` — verify mongoSanitize blocks it
2. Trigger the **rate limiter** (11 attempts in 1 hour) and verify 429 response
3. Review your app's HTTP headers at [securityheaders.com](https://securityheaders.com) (after deploying to a public URL)

---

## 🏆 Weekly Project: Secure User Auth System

Build the complete Auth System from Day 6, then add **3 extra features** from the bonus list.

### Core Requirements

| Category | Requirement |
|----------|-------------|
| **Registration** | Name, email, password, confirmPassword; validation on all fields |
| **Login** | JWT issued on success; generic error on failure |
| **Account Lockout** | 5 failed attempts → 30-minute lock |
| **Protected Routes** | Token required for profile endpoints |
| **Password Management** | Change password; old tokens invalidated after change |
| **Role System** | user / editor / admin roles with different access |
| **Admin Endpoints** | List users, change roles, activate/deactivate |
| **Security** | bcrypt, helmet, rate limiting, mongoSanitize, XSS protection |
| **httpOnly Cookie** | Set JWT in httpOnly cookie on login/signup |
| **Error Handling** | Production-safe errors; no stack traces |

### Bonus Features (pick 3+)

- [ ] Email verification — send verification link on signup (use Nodemailer or mock)
- [ ] Password reset flow — forgot password → email link → reset
- [ ] Refresh token system — short-lived access token + long-lived refresh token
- [ ] Google OAuth integration (using Passport.js)
- [ ] Two-Factor Authentication (TOTP with speakeasy)
- [ ] JWT stored in Redis for instant revocation
- [ ] Audit log model — log every login attempt with IP and timestamp
- [ ] Swagger/OpenAPI documentation for all auth endpoints

---

## 📊 Grading Rubric

| Criteria | Points |
|----------|--------|
| **Authentication** — Signup, login, logout work correctly with JWT | 20 |
| **Password Security** — bcrypt hashing, minimum requirements, confirm match | 10 |
| **Protected Routes** — Middleware blocks unauthenticated requests | 15 |
| **Token Invalidation** — Old tokens rejected after password change | 10 |
| **Role-Based Access** — restrictTo works for admin routes | 15 |
| **Account Lockout** — Locks after 5 failed attempts, unlocks after 30 minutes | 10 |
| **Security Middleware** — helmet, rate limit, mongoSanitize, XSS, HPP applied | 10 |
| **Code Quality** — MVC, clean middleware chain, ES Modules, .env | 10 |
| **Bonus Features** — 3+ extras implemented | +15 |
| **Total** | **100 + 15 bonus** |

---

## 📅 Submission

- **Due**: End of Week 16
- **Format**: GitHub repository link
- **Include**: README with setup instructions, `.env.example`, API endpoint documentation
- **Test**: Provide a Postman collection or curl test script

---

## 🎉 Phase 4 Completion

With this project, you complete **Phase 4: Backend Development**!

| Week | Topic | Status |
|------|-------|--------|
| 13 | Node.js & Express | ✅ |
| 14 | RESTful API Design | ✅ |
| 15 | MongoDB & Mongoose | ✅ |
| 16 | Authentication & Security | ✅ |

Next up: **Phase 5 — Full Stack Integration** 🚀

---

[← Back to Week 16](README.md) | [Next Phase →](../week-17/README.md)
