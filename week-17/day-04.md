# Week 17 - Day 4: Frontend Authentication Integration

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Integrate login/signup UI with auth API
- Persist auth state in frontend
- Protect frontend routes

## Auth Service

```js
// frontend/src/services/authService.js
import { apiClient } from './apiClient';

export const signup = (payload) =>
  apiClient('/auth/signup', { method: 'POST', body: JSON.stringify(payload) });

export const login = (payload) =>
  apiClient('/auth/login', { method: 'POST', body: JSON.stringify(payload) });

export const logout = () => apiClient('/auth/logout');

export const getMe = () => apiClient('/auth/me');
```

## Auth Context

```jsx
// frontend/src/context/AuthContext.jsx
import { createContext, useContext, useEffect, useState } from 'react';
import * as authService from '../services/authService';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    authService.getMe()
      .then((res) => setUser(res.data))
      .catch(() => setUser(null))
      .finally(() => setLoading(false));
  }, []);

  async function signIn(credentials) {
    const res = await authService.login(credentials);
    setUser(res.data);
    return res;
  }

  async function signOut() {
    await authService.logout();
    setUser(null);
  }

  return (
    <AuthContext.Provider value={{ user, loading, signIn, signOut, setUser }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext);
}
```

## Protected Route Component

```jsx
// frontend/src/components/ProtectedRoute.jsx
import { Navigate } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

export default function ProtectedRoute({ children }) {
  const { user, loading } = useAuth();

  if (loading) return <p>Checking authentication...</p>;
  if (!user) return <Navigate to="/login" replace />;

  return children;
}
```

## Hands-On Exercise

- Build login page and wire to backend
- Protect notes dashboard route
- Show current user name in navbar

## Homework

- Build signup page with field-level validation
- Add logout button and redirect flow

---

[<- Day 3](day-03.md) | [Back to Week 17](README.md) | [Day 5 ->](day-05.md)
