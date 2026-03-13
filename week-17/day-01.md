# Week 17 - Day 1: Full Stack Architecture and API Integration

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Understand frontend-backend contract design
- Connect React frontend to Express backend
- Standardize API response patterns

## Full Stack Request Lifecycle

1. User action in React triggers an event
2. Frontend sends HTTP request to backend endpoint
3. Backend validates input, processes logic, queries MongoDB
4. Backend returns JSON response with status and data
5. Frontend updates UI state and renders results

## Recommended API Response Shape

```json
{
  "status": "success",
  "message": "Notes fetched",
  "data": [],
  "meta": {
    "page": 1,
    "limit": 10,
    "total": 42
  }
}
```

## Environment Setup

```env
# frontend/.env
VITE_API_BASE_URL=http://localhost:3000/api
```

```js
// frontend/src/config/api.js
export const API_BASE_URL = import.meta.env.VITE_API_BASE_URL;
```

## First Integration Example

```js
// frontend/src/services/notesService.js
import { API_BASE_URL } from '../config/api';

export async function fetchNotes() {
  const response = await fetch(`${API_BASE_URL}/notes`);
  if (!response.ok) {
    throw new Error('Failed to fetch notes');
  }
  const json = await response.json();
  return json.data;
}
```

```jsx
// frontend/src/pages/NotesPage.jsx
import { useEffect, useState } from 'react';
import { fetchNotes } from '../services/notesService';

export default function NotesPage() {
  const [notes, setNotes] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');

  useEffect(() => {
    fetchNotes()
      .then(setNotes)
      .catch((err) => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <p>Loading notes...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <section>
      <h1>Notes</h1>
      <ul>
        {notes.map((note) => (
          <li key={note._id}>{note.title}</li>
        ))}
      </ul>
    </section>
  );
}
```

## Hands-On Exercise

- Connect one existing backend endpoint to a React page
- Render backend data in UI
- Handle loading and error states

## Homework

- Draw a full request-response sequence diagram for one CRUD action
- Add one more page that fetches another resource (folders/tags/users)

---

[<- Back to Week 17](README.md) | [Day 2 ->](day-02.md)
