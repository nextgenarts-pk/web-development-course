# Week 17 - Day 2: Data Fetching Patterns and Service Layer

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Build reusable API utilities
- Separate UI from networking logic
- Standardize request error handling

## Why a Service Layer?

Instead of writing fetch logic directly in every component, centralize it in service files.

Benefits:

- Cleaner components
- Reusable requests
- Easier testing and refactoring

## API Client Utility

```js
// frontend/src/services/apiClient.js
import { API_BASE_URL } from '../config/api';

export async function apiClient(path, options = {}) {
  const response = await fetch(`${API_BASE_URL}${path}`, {
    headers: {
      'Content-Type': 'application/json',
      ...(options.headers || {}),
    },
    credentials: 'include',
    ...options,
  });

  const body = await response.json().catch(() => ({}));

  if (!response.ok) {
    throw new Error(body.message || 'Request failed');
  }

  return body;
}
```

## Resource Service Example

```js
// frontend/src/services/notesService.js
import { apiClient } from './apiClient';

export function getNotes(params = '') {
  return apiClient(`/notes${params ? `?${params}` : ''}`);
}

export function getNoteById(id) {
  return apiClient(`/notes/${id}`);
}

export function createNote(payload) {
  return apiClient('/notes', {
    method: 'POST',
    body: JSON.stringify(payload),
  });
}

export function updateNote(id, payload) {
  return apiClient(`/notes/${id}`, {
    method: 'PATCH',
    body: JSON.stringify(payload),
  });
}

export function deleteNote(id) {
  return apiClient(`/notes/${id}`, { method: 'DELETE' });
}
```

## Component Usage Pattern

```jsx
const [notes, setNotes] = useState([]);
const [loading, setLoading] = useState(false);
const [error, setError] = useState('');

async function loadNotes() {
  setLoading(true);
  setError('');
  try {
    const result = await getNotes('sort=-createdAt&limit=20');
    setNotes(result.data);
  } catch (err) {
    setError(err.message);
  } finally {
    setLoading(false);
  }
}
```

## Hands-On Exercise

- Build one apiClient utility
- Refactor at least two pages to use service modules

## Homework

- Add request timeout handling with AbortController
- Add support for query object to query string conversion

---

[<- Day 1](day-01.md) | [Back to Week 17](README.md) | [Day 3 ->](day-03.md)
