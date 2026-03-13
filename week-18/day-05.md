# Week 18 - Day 5: Deployment and Production Operations

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Deploy frontend and backend safely
- Configure environment variables correctly
- Set up production checks and monitoring basics

## Common Deployment Stack

- Frontend: Vercel / Netlify
- Backend: Render / Railway / Fly.io
- Database: MongoDB Atlas

## Deployment Checklist

1. Set production env vars
2. Configure CORS for frontend domain
3. Enforce HTTPS and secure cookies
4. Verify health endpoint
5. Test signup/login and protected routes
6. Validate logs and error handling

## Example Env Values

```env
NODE_ENV=production
CLIENT_URL=https://your-frontend-domain.com
MONGODB_URI=...
JWT_SECRET=...
```

## CI/CD Basic Flow

1. Push to main branch
2. Run test suite
3. Build frontend
4. Deploy API
5. Deploy Web app

## Hands-On Exercise

- Deploy backend and frontend
- Connect frontend to production API URL

## Homework

- Write a runbook with rollback steps for failed deployment

---

[<- Day 4](day-04.md) | [Back to Week 18](README.md) | [Day 6 ->](day-06.md)
