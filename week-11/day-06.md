# 📅 Week 11 — Day 6: Project Day — Multi-Page Blog App 📝

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Build a complete multi-page blog application
- Apply React Router (routes, params, nested layouts)
- Use Context API for theme and auth
- Fetch data from JSONPlaceholder API
- Implement full CRUD with service layer

---

## 🏗️ Project Overview

### Multi-Page Blog App

A fully-routed blog with pages for listing posts, reading individual posts, creating new posts, a user profile, and dark/light theme — all powered by React Router, Context API, and a REST API.

### Features

| Feature | Details |
|---------|---------|
| Home page | Hero section + latest 6 posts |
| Post list | All posts with search & pagination |
| Post detail | Full post + author info + comments |
| Create post | Form with validation, redirect on success |
| Theme toggle | Dark/light mode via Context, persisted |
| Auth simulation | Login/logout with protected "Create Post" route |
| 404 page | Catch-all with navigation back |

---

## 📁 Project Structure

```
blog-app/
├── public/
├── src/
│   ├── components/
│   │   ├── Navbar.jsx
│   │   ├── Footer.jsx
│   │   ├── PostCard.jsx
│   │   ├── CommentList.jsx
│   │   ├── SearchBar.jsx
│   │   ├── Pagination.jsx
│   │   ├── ThemeToggle.jsx
│   │   └── LoadingSpinner.jsx
│   ├── context/
│   │   ├── ThemeContext.jsx
│   │   └── AuthContext.jsx
│   ├── hooks/
│   │   └── useFetch.js
│   ├── layouts/
│   │   ├── RootLayout.jsx
│   │   └── ProtectedLayout.jsx
│   ├── pages/
│   │   ├── Home.jsx
│   │   ├── PostList.jsx
│   │   ├── PostDetail.jsx
│   │   ├── CreatePost.jsx
│   │   ├── Login.jsx
│   │   └── NotFound.jsx
│   ├── services/
│   │   └── api.js
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
├── .env
├── package.json
└── vite.config.js
```

---

## 💻 Full Implementation

### 1. Entry Point & Providers

```jsx
// main.jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { BrowserRouter } from 'react-router-dom';
import { ThemeProvider } from './context/ThemeContext';
import { AuthProvider } from './context/AuthContext';
import App from './App';
import './index.css';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <BrowserRouter>
      <AuthProvider>
        <ThemeProvider>
          <App />
        </ThemeProvider>
      </AuthProvider>
    </BrowserRouter>
  </React.StrictMode>
);
```

### 2. App Routes

```jsx
// App.jsx
import { Routes, Route } from 'react-router-dom';
import RootLayout from './layouts/RootLayout';
import ProtectedLayout from './layouts/ProtectedLayout';
import Home from './pages/Home';
import PostList from './pages/PostList';
import PostDetail from './pages/PostDetail';
import CreatePost from './pages/CreatePost';
import Login from './pages/Login';
import NotFound from './pages/NotFound';

function App() {
  return (
    <Routes>
      <Route path="/" element={<RootLayout />}>
        <Route index element={<Home />} />
        <Route path="posts" element={<PostList />} />
        <Route path="posts/:postId" element={<PostDetail />} />
        <Route path="login" element={<Login />} />

        {/* Protected routes */}
        <Route element={<ProtectedLayout />}>
          <Route path="posts/new" element={<CreatePost />} />
        </Route>

        <Route path="*" element={<NotFound />} />
      </Route>
    </Routes>
  );
}

export default App;
```

### 3. API Service Layer

```jsx
// services/api.js
const BASE = 'https://jsonplaceholder.typicode.com';

export async function getPosts({ page = 1, limit = 10, search = '' } = {}) {
  const res = await fetch(
    `${BASE}/posts?_page=${page}&_limit=${limit}`
  );
  if (!res.ok) throw new Error('Failed to fetch posts');
  const total = res.headers.get('X-Total-Count') || 100;
  const data = await res.json();

  const filtered = search
    ? data.filter(p =>
        p.title.toLowerCase().includes(search.toLowerCase())
      )
    : data;

  return { posts: filtered, total: Number(total) };
}

export async function getPost(id) {
  const res = await fetch(`${BASE}/posts/${id}`);
  if (!res.ok) throw new Error('Post not found');
  return res.json();
}

export async function getComments(postId) {
  const res = await fetch(`${BASE}/posts/${postId}/comments`);
  if (!res.ok) throw new Error('Failed to fetch comments');
  return res.json();
}

export async function getUser(userId) {
  const res = await fetch(`${BASE}/users/${userId}`);
  if (!res.ok) throw new Error('User not found');
  return res.json();
}

export async function createPost(data) {
  const res = await fetch(`${BASE}/posts`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error('Failed to create post');
  return res.json();
}
```

### 4. Context — Theme

```jsx
// context/ThemeContext.jsx
import { createContext, useContext, useState, useEffect } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState(() => {
    return localStorage.getItem('blog-theme') || 'light';
  });

  useEffect(() => {
    localStorage.setItem('blog-theme', theme);
    document.documentElement.setAttribute('data-theme', theme);
  }, [theme]);

  const toggleTheme = () =>
    setTheme(prev => (prev === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be used within ThemeProvider');
  return ctx;
}
```

### 5. Context — Auth

```jsx
// context/AuthContext.jsx
import { createContext, useContext, useState } from 'react';

const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout, isLoggedIn: !!user }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth must be used within AuthProvider');
  return ctx;
}
```

### 6. Layouts

```jsx
// layouts/RootLayout.jsx
import { Outlet } from 'react-router-dom';
import { useTheme } from '../context/ThemeContext';
import Navbar from '../components/Navbar';
import Footer from '../components/Footer';

function RootLayout() {
  const { theme } = useTheme();

  return (
    <div className={`app ${theme}`}>
      <Navbar />
      <main className="container">
        <Outlet />
      </main>
      <Footer />
    </div>
  );
}

export default RootLayout;
```

```jsx
// layouts/ProtectedLayout.jsx
import { Outlet, Navigate } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

function ProtectedLayout() {
  const { isLoggedIn } = useAuth();

  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}

export default ProtectedLayout;
```

### 7. Components

```jsx
// components/Navbar.jsx
import { Link, NavLink } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';
import ThemeToggle from './ThemeToggle';

function Navbar() {
  const { user, logout, isLoggedIn } = useAuth();

  const linkClass = ({ isActive }) =>
    `nav-link ${isActive ? 'nav-link--active' : ''}`;

  return (
    <nav className="navbar">
      <Link to="/" className="logo">📝 BlogApp</Link>

      <div className="nav-center">
        <NavLink to="/" end className={linkClass}>Home</NavLink>
        <NavLink to="/posts" className={linkClass}>Posts</NavLink>
        {isLoggedIn && (
          <NavLink to="/posts/new" className={linkClass}>Write</NavLink>
        )}
      </div>

      <div className="nav-right">
        <ThemeToggle />
        {isLoggedIn ? (
          <div className="user-menu">
            <span>👤 {user.name}</span>
            <button onClick={logout} className="btn btn-sm">Logout</button>
          </div>
        ) : (
          <Link to="/login" className="btn btn-primary btn-sm">Login</Link>
        )}
      </div>
    </nav>
  );
}

export default Navbar;
```

```jsx
// components/ThemeToggle.jsx
import { useTheme } from '../context/ThemeContext';

function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();

  return (
    <button onClick={toggleTheme} className="theme-toggle" title="Toggle theme">
      {theme === 'light' ? '🌙' : '☀️'}
    </button>
  );
}

export default ThemeToggle;
```

```jsx
// components/PostCard.jsx
import { Link } from 'react-router-dom';

function PostCard({ post }) {
  return (
    <article className="post-card">
      <h3>
        <Link to={`/posts/${post.id}`}>{post.title}</Link>
      </h3>
      <p>{post.body.slice(0, 120)}...</p>
      <Link to={`/posts/${post.id}`} className="read-more">
        Read more →
      </Link>
    </article>
  );
}

export default PostCard;
```

```jsx
// components/CommentList.jsx
function CommentList({ comments }) {
  if (comments.length === 0) return <p>No comments yet.</p>;

  return (
    <div className="comment-list">
      <h3>💬 Comments ({comments.length})</h3>
      {comments.map(comment => (
        <div key={comment.id} className="comment">
          <strong>{comment.name}</strong>
          <span className="comment-email">{comment.email}</span>
          <p>{comment.body}</p>
        </div>
      ))}
    </div>
  );
}

export default CommentList;
```

```jsx
// components/SearchBar.jsx
import { useState } from 'react';

function SearchBar({ onSearch }) {
  const [query, setQuery] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    onSearch(query);
  }

  return (
    <form className="search-bar" onSubmit={handleSubmit}>
      <input
        type="text"
        placeholder="Search posts..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      <button type="submit">🔍</button>
    </form>
  );
}

export default SearchBar;
```

```jsx
// components/Pagination.jsx
function Pagination({ currentPage, totalPages, onPageChange }) {
  const pages = Array.from({ length: totalPages }, (_, i) => i + 1);

  return (
    <div className="pagination">
      <button
        onClick={() => onPageChange(currentPage - 1)}
        disabled={currentPage === 1}
      >
        ← Prev
      </button>

      {pages.map(page => (
        <button
          key={page}
          onClick={() => onPageChange(page)}
          className={page === currentPage ? 'active' : ''}
        >
          {page}
        </button>
      ))}

      <button
        onClick={() => onPageChange(currentPage + 1)}
        disabled={currentPage === totalPages}
      >
        Next →
      </button>
    </div>
  );
}

export default Pagination;
```

```jsx
// components/LoadingSpinner.jsx
function LoadingSpinner() {
  return (
    <div className="loading-spinner">
      <div className="spinner" />
      <p>Loading...</p>
    </div>
  );
}

export default LoadingSpinner;
```

```jsx
// components/Footer.jsx
function Footer() {
  return (
    <footer className="footer">
      <p>© 2025 BlogApp — Built with React & React Router</p>
      <p>
        <a href="https://nextgenarts.pk" target="_blank" rel="noopener noreferrer">
          NextGen Arts
        </a>
      </p>
    </footer>
  );
}

export default Footer;
```

### 8. Pages

```jsx
// pages/Home.jsx
import { useState, useEffect } from 'react';
import { Link } from 'react-router-dom';
import { getPosts } from '../services/api';
import PostCard from '../components/PostCard';
import LoadingSpinner from '../components/LoadingSpinner';

function Home() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let cancelled = false;
    getPosts({ limit: 6 })
      .then(({ posts }) => { if (!cancelled) setPosts(posts); })
      .catch(() => {})
      .finally(() => { if (!cancelled) setLoading(false); });
    return () => { cancelled = true; };
  }, []);

  return (
    <div className="home">
      <section className="hero">
        <h1>Welcome to BlogApp 📝</h1>
        <p>Discover stories, ideas, and insights from our community.</p>
        <Link to="/posts" className="btn btn-primary">Browse All Posts</Link>
      </section>

      <section className="latest-posts">
        <h2>Latest Posts</h2>
        {loading ? (
          <LoadingSpinner />
        ) : (
          <div className="post-grid">
            {posts.map(post => (
              <PostCard key={post.id} post={post} />
            ))}
          </div>
        )}
      </section>
    </div>
  );
}

export default Home;
```

```jsx
// pages/PostList.jsx
import { useState, useEffect } from 'react';
import { useSearchParams } from 'react-router-dom';
import { getPosts } from '../services/api';
import PostCard from '../components/PostCard';
import SearchBar from '../components/SearchBar';
import Pagination from '../components/Pagination';
import LoadingSpinner from '../components/LoadingSpinner';

const POSTS_PER_PAGE = 10;

function PostList() {
  const [searchParams, setSearchParams] = useSearchParams();
  const [posts, setPosts] = useState([]);
  const [total, setTotal] = useState(0);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const page = parseInt(searchParams.get('page') || '1', 10);
  const search = searchParams.get('q') || '';

  useEffect(() => {
    let cancelled = false;
    setLoading(true);

    getPosts({ page, limit: POSTS_PER_PAGE, search })
      .then(({ posts, total }) => {
        if (!cancelled) {
          setPosts(posts);
          setTotal(total);
        }
      })
      .catch(err => { if (!cancelled) setError(err.message); })
      .finally(() => { if (!cancelled) setLoading(false); });

    return () => { cancelled = true; };
  }, [page, search]);

  function handleSearch(query) {
    setSearchParams({ q: query, page: '1' });
  }

  function handlePageChange(newPage) {
    const params = { page: String(newPage) };
    if (search) params.q = search;
    setSearchParams(params);
    window.scrollTo(0, 0);
  }

  const totalPages = Math.ceil(total / POSTS_PER_PAGE);

  return (
    <div className="post-list-page">
      <h1>📚 All Posts</h1>
      <SearchBar onSearch={handleSearch} />

      {loading && <LoadingSpinner />}
      {error && <p className="error">❌ {error}</p>}

      {!loading && !error && (
        <>
          {posts.length === 0 ? (
            <p>No posts found.</p>
          ) : (
            <div className="post-grid">
              {posts.map(post => (
                <PostCard key={post.id} post={post} />
              ))}
            </div>
          )}

          {totalPages > 1 && (
            <Pagination
              currentPage={page}
              totalPages={totalPages}
              onPageChange={handlePageChange}
            />
          )}
        </>
      )}
    </div>
  );
}

export default PostList;
```

```jsx
// pages/PostDetail.jsx
import { useState, useEffect } from 'react';
import { useParams, Link, useNavigate } from 'react-router-dom';
import { getPost, getComments, getUser } from '../services/api';
import CommentList from '../components/CommentList';
import LoadingSpinner from '../components/LoadingSpinner';

function PostDetail() {
  const { postId } = useParams();
  const navigate = useNavigate();
  const [post, setPost] = useState(null);
  const [author, setAuthor] = useState(null);
  const [comments, setComments] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;
    setLoading(true);

    async function loadPost() {
      try {
        const postData = await getPost(postId);
        if (cancelled) return;
        setPost(postData);

        const [userData, commentData] = await Promise.all([
          getUser(postData.userId),
          getComments(postId),
        ]);

        if (!cancelled) {
          setAuthor(userData);
          setComments(commentData);
        }
      } catch (err) {
        if (!cancelled) setError(err.message);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }

    loadPost();
    return () => { cancelled = true; };
  }, [postId]);

  if (loading) return <LoadingSpinner />;
  if (error) return <p className="error">❌ {error}</p>;
  if (!post) return null;

  return (
    <article className="post-detail">
      <button onClick={() => navigate(-1)} className="btn btn-back">
        ← Back
      </button>

      <h1>{post.title}</h1>

      {author && (
        <div className="post-author">
          <span>✍️ {author.name}</span>
          <span className="author-email">{author.email}</span>
          <span className="author-company">🏢 {author.company.name}</span>
        </div>
      )}

      <div className="post-body">
        <p>{post.body}</p>
      </div>

      <hr />

      <CommentList comments={comments} />

      <div className="post-nav">
        {Number(postId) > 1 && (
          <Link to={`/posts/${Number(postId) - 1}`}>← Previous Post</Link>
        )}
        <Link to={`/posts/${Number(postId) + 1}`}>Next Post →</Link>
      </div>
    </article>
  );
}

export default PostDetail;
```

```jsx
// pages/CreatePost.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { createPost } from '../services/api';

function CreatePost() {
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
      await createPost({ ...form, userId: 1 });
      navigate('/posts');
    } catch (err) {
      setError(err.message);
    } finally {
      setSubmitting(false);
    }
  }

  return (
    <div className="create-post">
      <h1>✍️ Write a New Post</h1>

      {error && <p className="error">❌ {error}</p>}

      <form onSubmit={handleSubmit} className="post-form">
        <label>
          Title
          <input
            name="title"
            value={form.title}
            onChange={handleChange}
            placeholder="Enter post title..."
            required
          />
        </label>

        <label>
          Content
          <textarea
            name="body"
            value={form.body}
            onChange={handleChange}
            placeholder="Write your post content..."
            rows={10}
            required
          />
        </label>

        <div className="form-actions">
          <button type="submit" className="btn btn-primary" disabled={submitting}>
            {submitting ? 'Publishing...' : '🚀 Publish Post'}
          </button>
          <button
            type="button"
            className="btn"
            onClick={() => navigate(-1)}
          >
            Cancel
          </button>
        </div>
      </form>
    </div>
  );
}

export default CreatePost;
```

```jsx
// pages/Login.jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useAuth } from '../context/AuthContext';

function Login() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const [name, setName] = useState('');

  function handleSubmit(e) {
    e.preventDefault();
    if (!name.trim()) return;
    login({ name: name.trim(), email: `${name.toLowerCase().replace(/\s/g, '')}@example.com` });
    navigate('/posts');
  }

  return (
    <div className="login-page">
      <h1>🔐 Login</h1>
      <p>Enter your name to get started.</p>
      <form onSubmit={handleSubmit} className="login-form">
        <input
          type="text"
          value={name}
          onChange={e => setName(e.target.value)}
          placeholder="Your name"
          required
        />
        <button type="submit" className="btn btn-primary">Login</button>
      </form>
    </div>
  );
}

export default Login;
```

```jsx
// pages/NotFound.jsx
import { Link } from 'react-router-dom';

function NotFound() {
  return (
    <div className="not-found">
      <h1>404</h1>
      <p>The page you're looking for doesn't exist.</p>
      <Link to="/" className="btn btn-primary">Go Home</Link>
    </div>
  );
}

export default NotFound;
```

### 9. Styles (index.css)

```css
/* index.css */
:root {
  --bg: #ffffff;
  --bg-secondary: #f3f4f6;
  --text: #1f2937;
  --text-secondary: #6b7280;
  --primary: #3b82f6;
  --primary-hover: #2563eb;
  --border: #e5e7eb;
  --card-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

[data-theme="dark"] {
  --bg: #111827;
  --bg-secondary: #1f2937;
  --text: #f9fafb;
  --text-secondary: #9ca3af;
  --primary: #60a5fa;
  --primary-hover: #3b82f6;
  --border: #374151;
  --card-shadow: 0 1px 3px rgba(0,0,0,0.4);
}

* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  font-family: 'Inter', system-ui, sans-serif;
  background: var(--bg);
  color: var(--text);
  line-height: 1.6;
}

.container { max-width: 1100px; margin: 0 auto; padding: 2rem 1rem; }

/* Navbar */
.navbar {
  display: flex; align-items: center; justify-content: space-between;
  padding: 1rem 2rem; background: var(--bg-secondary); border-bottom: 1px solid var(--border);
}
.logo { font-size: 1.4rem; font-weight: 700; text-decoration: none; color: var(--text); }
.nav-link { text-decoration: none; color: var(--text-secondary); padding: 0.5rem 1rem; }
.nav-link--active { color: var(--primary); font-weight: 600; }

/* Post Grid & Cards */
.post-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(320px, 1fr)); gap: 1.5rem; }
.post-card {
  background: var(--bg-secondary); border: 1px solid var(--border);
  border-radius: 8px; padding: 1.5rem; box-shadow: var(--card-shadow);
}
.post-card h3 a { text-decoration: none; color: var(--text); }
.post-card h3 a:hover { color: var(--primary); }
.read-more { color: var(--primary); text-decoration: none; font-weight: 500; }

/* Buttons */
.btn {
  padding: 0.5rem 1.25rem; border-radius: 6px; border: 1px solid var(--border);
  cursor: pointer; font-size: 0.95rem; background: var(--bg-secondary); color: var(--text);
}
.btn-primary { background: var(--primary); color: white; border: none; }
.btn-primary:hover { background: var(--primary-hover); }
.btn-sm { padding: 0.3rem 0.75rem; font-size: 0.85rem; }

/* Pagination */
.pagination { display: flex; gap: 0.5rem; justify-content: center; margin-top: 2rem; }
.pagination button.active { background: var(--primary); color: white; }

/* Footer */
.footer {
  text-align: center; padding: 2rem; margin-top: 3rem;
  border-top: 1px solid var(--border); color: var(--text-secondary);
}
```

---

## 🎯 Stretch Goals

1. Add an edit post page (`/posts/:id/edit`) with pre-filled form
2. Add a delete button on the post detail page
3. Show the author's other posts as "More by this author"
4. Add transition animations with `framer-motion` on route changes
5. Create a tag/category filter on the post list page

---

## 📝 Homework

1. Complete all features listed in the table above
2. Ensure dark/light theme persists on refresh
3. The "Write" nav link should only appear when logged in
4. Search in the URL (`?q=...`) should be bookmarkable and shareable

---

## 💡 Pro Tips

- Structure your project by **feature cluster**: layouts, pages, components, context, services, hooks
- `<Outlet />` is the key to nested layouts — think of it like `{children}` for routes
- Put auth state in context so `Navbar`, `ProtectedLayout`, and pages all read from one source
- Always handle loading, error, and empty states — users notice

---

[← Day 5](day-05.md) | [Back to Week 11](README.md) | [Week 12 →](../week-12/README.md)
