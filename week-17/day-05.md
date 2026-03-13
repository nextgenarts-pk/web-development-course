# Week 17 - Day 5: Global State and UX Resilience

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Manage shared app state cleanly
- Build consistent loading, error, and empty states
- Improve reliability of user workflows

## Shared UI State Pattern

Use a predictable request state object:

```js
const initialRequestState = {
  loading: false,
  error: '',
  success: '',
};
```

## Reusable Request Hook

```jsx
// frontend/src/hooks/useRequest.js
import { useState } from 'react';

export function useRequest() {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');
  const [success, setSuccess] = useState('');

  async function execute(fn) {
    setLoading(true);
    setError('');
    setSuccess('');
    try {
      const result = await fn();
      return result;
    } catch (err) {
      setError(err.message || 'Request failed');
      throw err;
    } finally {
      setLoading(false);
    }
  }

  return { loading, error, success, setSuccess, execute };
}
```

## Better UX States

1. Loading state: skeletons/spinners
2. Empty state: actionable message
3. Error state: retry button
4. Success feedback: toast/banner

```jsx
if (loading) return <p>Loading...</p>;
if (error) return <button onClick={loadData}>Retry: {error}</button>;
if (!items.length) return <p>No data yet. Create your first item.</p>;
```

## Hands-On Exercise

- Add reusable loading/error components
- Ensure all list pages support retry
- Add toast messages for create/update/delete

## Homework

- Add client-side cache for last fetched notes
- Add debounce on search input to reduce API calls

---

[<- Day 4](day-04.md) | [Back to Week 17](README.md) | [Day 6 ->](day-06.md)
