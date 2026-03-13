# 📅 Week 11 — Day 2: Dynamic Routes & URL Parameters 🔗

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Create dynamic routes with URL parameters
- Read parameters using `useParams`
- Work with search/query parameters using `useSearchParams`
- Build detail pages that load data based on the URL
- Handle missing or invalid parameters gracefully

---

## 📖 Lesson Content

### 1. What Are Dynamic Routes?

Static routes like `/about` always show the same content. **Dynamic routes** contain variable segments — a blog post ID, a username, a product slug — that change the rendered content.

```
/posts/1      → shows post with id 1
/posts/42     → shows post with id 42
/users/ali    → shows Ali's profile
```

### 2. Defining Dynamic Routes

Use a colon `:` prefix for dynamic segments:

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom';
import PostList from './pages/PostList';
import PostDetail from './pages/PostDetail';
import UserProfile from './pages/UserProfile';

function App() {
  return (
    <Routes>
      <Route path="/" element={<PostList />} />
      <Route path="/posts/:postId" element={<PostDetail />} />
      <Route path="/users/:username" element={<UserProfile />} />
      <Route path="*" element={<h1>404</h1>} />
    </Routes>
  );
}
```

### 3. Reading URL Parameters with useParams

```jsx
// pages/PostDetail.jsx
import { useParams, Link } from 'react-router-dom';
import { useState, useEffect } from 'react';

function PostDetail() {
  const { postId } = useParams();
  const [post, setPost] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    setError(null);

    fetch(`https://jsonplaceholder.typicode.com/posts/${postId}`)
      .then(res => {
        if (!res.ok) throw new Error('Post not found');
        return res.json();
      })
      .then(data => setPost(data))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, [postId]);

  if (loading) return <p>Loading post...</p>;
  if (error) return <p className="error">❌ {error}</p>;
  if (!post) return <p>Post not found</p>;

  return (
    <article>
      <Link to="/">← Back to Posts</Link>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </article>
  );
}

export default PostDetail;
```

### 4. Linking to Dynamic Routes

```jsx
// pages/PostList.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';

function PostList() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/posts?_limit=10')
      .then(res => res.json())
      .then(data => setPosts(data));
  }, []);

  return (
    <div>
      <h1>📝 Blog Posts</h1>
      <ul>
        {posts.map(post => (
          <li key={post.id}>
            <Link to={`/posts/${post.id}`}>{post.title}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default PostList;
```

### 5. Multiple Parameters

```jsx
// Route
<Route path="/users/:userId/posts/:postId" element={<UserPost />} />

// Component
function UserPost() {
  const { userId, postId } = useParams();

  return (
    <div>
      <p>User: {userId}</p>
      <p>Post: {postId}</p>
    </div>
  );
}
```

### 6. Search Parameters with useSearchParams

Search params are the `?key=value` pairs after the URL path — perfect for filters, search, sorting, and pagination.

```jsx
import { useSearchParams } from 'react-router-dom';

function PostList() {
  const [searchParams, setSearchParams] = useSearchParams();
  const [posts, setPosts] = useState([]);

  const category = searchParams.get('category') || 'all';
  const page = parseInt(searchParams.get('page') || '1', 10);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=5`)
      .then(res => res.json())
      .then(data => setPosts(data));
  }, [page]);

  function handleCategoryChange(e) {
    setSearchParams({ category: e.target.value, page: '1' });
  }

  function nextPage() {
    setSearchParams({ category, page: String(page + 1) });
  }

  function prevPage() {
    if (page > 1) {
      setSearchParams({ category, page: String(page - 1) });
    }
  }

  return (
    <div>
      <h1>Posts</h1>

      <select value={category} onChange={handleCategoryChange}>
        <option value="all">All</option>
        <option value="tech">Tech</option>
        <option value="design">Design</option>
      </select>

      <ul>
        {posts.map(post => (
          <li key={post.id}>
            <Link to={`/posts/${post.id}`}>{post.title}</Link>
          </li>
        ))}
      </ul>

      <div className="pagination">
        <button onClick={prevPage} disabled={page === 1}>Previous</button>
        <span>Page {page}</span>
        <button onClick={nextPage}>Next</button>
      </div>
    </div>
  );
}
```

### 7. URL Params vs. Search Params

| | URL Params (`:id`) | Search Params (`?key=val`) |
|---|---|---|
| **Use for** | Identifying a resource | Filtering, sorting, pagination |
| **Required?** | Yes — route won't match without it | No — optional by nature |
| **Hook** | `useParams()` | `useSearchParams()` |
| **Example** | `/posts/42` | `/posts?page=2&sort=date` |
| **SEO** | Each value = unique page | Same page, different view |

### 8. Handling Invalid Parameters

```jsx
function PostDetail() {
  const { postId } = useParams();
  const navigate = useNavigate();

  useEffect(() => {
    // Validate parameter
    if (isNaN(Number(postId)) || Number(postId) < 1) {
      navigate('/not-found', { replace: true });
      return;
    }

    // ... fetch post
  }, [postId, navigate]);

  // ...
}
```

---

## 💪 Hands-On Exercise

### Build a User Directory

1. Fetch users from `https://jsonplaceholder.typicode.com/users`
2. List all users on `/users` with links to `/users/:userId`
3. On the detail page, fetch the user by ID and show name, email, company, address
4. Add a "View Posts" link on the user detail page → `/users/:userId/posts`
5. On that page, fetch posts by `?userId=X` and list them
6. Add pagination with `useSearchParams` (5 posts per page)

---

## 📝 Homework

1. Add a search bar on the `PostList` that writes the query to `?q=...` in the URL
2. Filter posts client-side based on the `q` search param
3. Make the search shareable — loading the URL with `?q=react` should auto-filter
4. Handle the case where `postId` doesn't exist (show a "Post Not Found" message)

---

## 💡 Pro Tips

- `useParams` values are always **strings** — convert to numbers when needed
- Put search/filter state in the URL with `useSearchParams` so users can share links
- Use `navigate('/path', { replace: true })` for redirects to avoid polluting browser history
- `useEffect` dependency on `postId` ensures the component refetches when navigating between posts

---

[← Day 1](day-01.md) | [Back to Week 11](README.md) | [Day 3 →](day-03.md)
