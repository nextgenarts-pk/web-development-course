# Week 18 - Day 6: Project Day - Full Stack Task Manager Pro

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Project Goal

Build and ship a production-ready full stack task manager with authentication, role-based views, and deployment.

## Core Requirements

- User auth: signup, login, logout, profile
- Task CRUD with status, priority, due date
- Filter/sort/search tasks
- Protected routes for authenticated users
- Admin dashboard for user management
- Responsive UI and strong loading/error states

## Technical Requirements

- React frontend with service layer + protected routes
- Express + MongoDB backend with JWT auth
- Role checks for admin actions
- Security middleware enabled
- Deployment to public URL

## Suggested API Endpoints

- POST /api/auth/signup
- POST /api/auth/login
- GET /api/auth/me
- GET /api/tasks
- POST /api/tasks
- PATCH /api/tasks/:id
- DELETE /api/tasks/:id
- GET /api/admin/users

## Acceptance Criteria

- All key flows work in production deployment
- No sensitive fields leaked in API responses
- Route guards prevent unauthorized access
- At least one automated backend test exists

## Bonus

- Team collaboration mode
- Activity log timeline
- Drag-and-drop task board

---

[<- Day 5](day-05.md) | [Back to Week 18](README.md) | [Next Phase ->](../week-19/README.md)
