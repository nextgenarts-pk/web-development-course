# 📅 Week 11 — Day 5: API Integration in React 🔌

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Fetch data from REST APIs inside React components
- Handle loading, error, and empty states properly
- Build a reusable data-fetching pattern
- Implement create, update, and delete operations
- Use environment variables for API base URLs
- Organize API logic with a service layer

---

## 📖 Lesson Content

### 1. The Data Fetching Pattern in React

Every API call in a React component follows the same shape:

```jsx
const [data, setData] = useState(null);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);

useEffect(() => {
  let cancelled = false;

  async function fetchData() {
    try {
      setLoading(true);
      const res = await fetch(url);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const json = await res.json();
      if (!cancelled) setData(json);
    } catch (err) {
      if (!cancelled) setError(err.message);
    } finally {
      if (!cancelled) setLoading(false);
    }
  }

  fetchData();
  return () => { cancelled = true; };
}, [url]);
```

> The `cancelled` flag prevents setting state on an unmounted component.

### 2. Building a Posts Page

```jsx
// pages/PostsPage.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';

const API_BASE = 'https://jsonplaceholder.typicode.com';

function PostsPage() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function loadPosts() {
      try {
        const res = await fetch(`${API_BASE}/posts?_limit=20`);
        if (!res.ok) throw new Error('Failed to fetch posts');
        const data = await res.json();
        if (!cancelled) setPosts(data);
      } catch (err) {
        if (!cancelled) setError(err.message);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }

    loadPosts();
    return () => { cancelled = true; };
  }, []);

  if (loading) return <p className="loading">⏳ Loading posts...</p>;
  if (error) return <p className="error">❌ {error}</p>;
  if (posts.length === 0) return <p>No posts found.</p>;

  return (
    <div>
      <h1>📝 Blog Posts</h1>
      <div className="post-grid">
        {posts.map(post => (
          <article key={post.id} className="post-card">
            <h2>{post.title}</h2>
            <p>{post.body.slice(0, 100)}...</p>
            <Link to={`/posts/${post.id}`}>Read more →</Link>
          </article>
        ))}
      </div>
    </div>
  );
}

export default PostsPage;
```

### 3. Creating Data (POST Request)

```jsx
// components/CreatePostForm.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';

const API_BASE = 'https://jsonplaceholder.typicode.com';

function CreatePostForm() {
  const navigate = useNavigate();
  const [form, setForm] = useState({ title: '', body: '' });
  const [submitting, setSubmitting] = useState(false);
  const [error, setError] = useState(null);

  function handleChange(e) {
    setForm(prev => ({ ...prev, [e.target.name]: e.target.value }));
  }

  async function handleSubmit(e) {
    e.preventDefault();
    setSubmitting(true);
    setError(null);

    try {
      const res = await fetch(`${API_BASE}/posts`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ ...form, userId: 1 }),
      });

      if (!res.ok) throw new Error('Failed to create post');

      const newPost = await res.json();
      console.log('Created:', newPost);
      navigate('/posts');
    } catch (err) {
      setError(err.message);
    } finally {
      setSubmitting(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <h2>✍️ New Post</h2>

      {error && <p className="error">{error}</p>}

      <label>
        Title
        <input
          name="title"
          value={form.title}
          onChange={handleChange}
          required
        />
      </label>

      <label>
        Body
        <textarea
          name="body"
          value={form.body}
          onChange={handleChange}
          rows={6}
          required
        />
      </label>

      <button type="submit" disabled={submitting}>
        {submitting ? 'Creating...' : 'Create Post'}
      </button>
    </form>
  );
}

export default CreatePostForm;
```

### 4. Updating Data (PUT / PATCH)

```jsx
async function updatePost(postId, updates) {
  const res = await fetch(`${API_BASE}/posts/${postId}`, {
    method: 'PATCH',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates),
  });

  if (!res.ok) throw new Error('Failed to update post');
  return res.json();
}

// In a component:
async function handleSave() {
  try {
    setSaving(true);
    const updated = await updatePost(postId, { title, body });
    setPost(updated);
  } catch (err) {
    setError(err.message);
  } finally {
    setSaving(false);
  }
}
```

### 5. Deleting Data (DELETE)

```jsx
async function handleDelete(postId) {
  if (!window.confirm('Delete this post?')) return;

  try {
    const res = await fetch(`${API_BASE}/posts/${postId}`, {
      method: 'DELETE',
    });
    if (!res.ok) throw new Error('Failed to delete');

    // Remove from local state
    setPosts(prev => prev.filter(p => p.id !== postId));
  } catch (err) {
    setError(err.message);
  }
}
```

### 6. Service Layer — Separating API Logic

Keep fetch calls out of components:

```jsx
// services/postService.js
const API_BASE = 'https://jsonplaceholder.typicode.com';

export async function getPosts(limit = 20) {
  const res = await fetch(`${API_BASE}/posts?_limit=${limit}`);
  if (!res.ok) throw new Error('Failed to fetch posts');
  return res.json();
}

export async function getPost(id) {
  const res = await fetch(`${API_BASE}/posts/${id}`);
  if (!res.ok) throw new Error('Post not found');
  return res.json();
}

export async function createPost(data) {
  const res = await fetch(`${API_BASE}/posts`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error('Failed to create post');
  return res.json();
}

export async function deletePost(id) {
  const res = await fetch(`${API_BASE}/posts/${id}`, {
    method: 'DELETE',
  });
  if (!res.ok) throw new Error('Failed to delete post');
}
```

Now components are clean:

```jsx
import { getPosts } from '../services/postService';

useEffect(() => {
  let cancelled = false;

  getPosts(20)
    .then(data => { if (!cancelled) setPosts(data); })
    .catch(err => { if (!cancelled) setError(err.message); })
    .finally(() => { if (!cancelled) setLoading(false); });

  return () => { cancelled = true; };
}, []);
```

### 7. Reusable useFetch Hook

```jsx
// hooks/useFetch.js
import { useState, useEffect } from 'react';

export function useFetch(fetchFn, deps = []) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);
    setError(null);

    fetchFn()
      .then(result => { if (!cancelled) setData(result); })
      .catch(err => { if (!cancelled) setError(err.message); })
      .finally(() => { if (!cancelled) setLoading(false); });

    return () => { cancelled = true; };
  }, deps);

  return { data, loading, error };
}
```

Usage:

```jsx
import { useFetch } from '../hooks/useFetch';
import { getPosts } from '../services/postService';

function PostsPage() {
  const { data: posts, loading, error } = useFetch(() => getPosts(20), []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

### 8. Environment Variables in Vite

```bash
# .env
VITE_API_BASE=https://jsonplaceholder.typicode.com
```

```jsx
// services/postService.js
const API_BASE = import.meta.env.VITE_API_BASE;
```

> Vite exposes only variables prefixed with `VITE_`. Never put secrets in frontend env vars.

---

## 💪 Hands-On Exercise

### Build a Full CRUD Posts Manager

1. Create a `postService.js` with `getPosts`, `getPost`, `createPost`, `updatePost`, `deletePost`
2. `/posts` — list all posts with a delete button on each
3. `/posts/new` — create post form, redirect to list on success
4. `/posts/:id` — view single post with edit and delete buttons
5. `/posts/:id/edit` — edit form pre-filled with existing data
6. Use the `useFetch` hook for all GET requests
7. Show loading spinners and error messages

---

## 📝 Homework

1. Add optimistic delete — remove the post from the UI immediately, restore it if the API fails
2. Add a "Refresh" button that refetches the post list
3. Show a success toast after creating or deleting a post
4. Add a comments section on the post detail page (fetch from `/posts/:id/comments`)

---

## 💡 Pro Tips

- Always handle three states: **loading**, **error**, **success** — never leave the user guessing
- The `cancelled` flag pattern prevents the "Can't perform a React state update on an unmounted component" warning
- Service layers make it easy to swap APIs later (e.g., from JSONPlaceholder to your own backend)
- Never put API keys or secrets in frontend environment variables — they're visible in the browser

---

[← Day 4](day-04.md) | [Back to Week 11](README.md) | [Day 6 →](day-06.md)
