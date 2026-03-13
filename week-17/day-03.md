# Week 17 - Day 3: End-to-End CRUD in React + Express

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Implement full CRUD cycle in the frontend
- Keep frontend state in sync with backend data
- Build optimistic and pessimistic update patterns

## CRUD UI Strategy

1. Read: Fetch list and detail views
2. Create: Submit form, append new item to UI
3. Update: Edit form, patch backend, refresh state
4. Delete: Confirm action, remove from UI

## Create Flow Example

```jsx
import { useState } from 'react';
import { createNote } from '../services/notesService';

export default function CreateNoteForm({ onCreated }) {
  const [title, setTitle] = useState('');
  const [content, setContent] = useState('');
  const [saving, setSaving] = useState(false);

  async function handleSubmit(e) {
    e.preventDefault();
    setSaving(true);
    try {
      const result = await createNote({ title, content, category: 'personal' });
      onCreated(result.data);
      setTitle('');
      setContent('');
    } finally {
      setSaving(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={title} onChange={(e) => setTitle(e.target.value)} placeholder="Title" required />
      <textarea value={content} onChange={(e) => setContent(e.target.value)} placeholder="Content" required />
      <button disabled={saving}>{saving ? 'Saving...' : 'Create Note'}</button>
    </form>
  );
}
```

## Delete Flow Example

```jsx
import { deleteNote } from '../services/notesService';

async function handleDelete(id) {
  const confirmed = window.confirm('Delete this note?');
  if (!confirmed) return;

  await deleteNote(id);
  setNotes((prev) => prev.filter((n) => n._id !== id));
}
```

## API Error Mapping

```js
function mapApiError(error) {
  if (error.message.includes('Validation')) return 'Please check your input fields.';
  if (error.message.includes('401')) return 'You are not logged in.';
  if (error.message.includes('403')) return 'You do not have access to this action.';
  return 'Something went wrong. Please try again.';
}
```

## Hands-On Exercise

- Build note create/edit/delete UI on top of Week 15 API
- Show a success message after each action

## Homework

- Add client-side validation before sending forms
- Add a reusable confirm modal component for destructive actions

---

[<- Day 2](day-02.md) | [Back to Week 17](README.md) | [Day 4 ->](day-04.md)
