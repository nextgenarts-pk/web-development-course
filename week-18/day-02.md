# Week 18 - Day 2: Advanced Auth Flows and Route Guards

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Implement robust session handling
- Build role-aware frontend route guards
- Design refresh-safe auth UX

## Route Guard Types

1. Public-only (login/signup pages)
2. Auth-required (dashboard/profile)
3. Role-restricted (admin panel)

```jsx
// RoleProtectedRoute.jsx
import { Navigate } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

export default function RoleProtectedRoute({ roles, children }) {
  const { user, loading } = useAuth();

  if (loading) return <p>Loading...</p>;
  if (!user) return <Navigate to="/login" replace />;
  if (!roles.includes(user.role)) return <Navigate to="/forbidden" replace />;

  return children;
}
```

## Refresh-Safe Session Pattern

- App boot: call /auth/me
- If valid session: hydrate auth state
- If invalid: clear local auth state and redirect

## Hands-On Exercise

- Add admin-only page with role guard
- Add forbidden page for unauthorized access

## Homework

- Implement remember-me behavior with controlled token lifetime

---

[<- Day 1](day-01.md) | [Back to Week 18](README.md) | [Day 3 ->](day-03.md)
