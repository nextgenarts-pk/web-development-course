# 📅 Week 14 — Day 2: Advanced CRUD Patterns 🔄

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Understand PUT vs PATCH for updates
- Implement bulk operations (create/update/delete many)
- Build soft delete functionality
- Handle data relationships (one-to-many, many-to-many)
- Implement slugs and auto-generated fields

---

## 📖 Lesson Content

### 1. PUT vs PATCH — When to Use Which

| Method | Purpose | Behavior |
|--------|---------|----------|
| `PUT` | Replace entire resource | Must send ALL fields — missing fields reset to defaults |
| `PATCH` | Partial update | Only send fields you want to change |

```js
// PUT — full replacement
// Client must send complete object
export function replacePost(req, res) {
  const post = findPostOrThrow(req.params.id);
  const { title, content, author, categoryId, tags, status } = req.body;

  // All fields are overwritten (missing = default)
  post.title = title;
  post.slug = generateSlug(title);
  post.content = content;
  post.excerpt = content.substring(0, 150) + '...';
  post.author = author;
  post.categoryId = categoryId || null;
  post.tags = tags || [];
  post.status = status || 'draft';
  post.updatedAt = new Date().toISOString();

  res.json({ status: 'success', data: post });
}

// PATCH — partial update
// Only update provided fields
export function updatePost(req, res) {
  const post = findPostOrThrow(req.params.id);
  const allowedFields = ['title', 'content', 'author', 'categoryId', 'tags', 'status'];

  for (const field of allowedFields) {
    if (req.body[field] !== undefined) {
      post[field] = req.body[field];
    }
  }

  // Recalculate derived fields
  if (req.body.title) {
    post.slug = generateSlug(req.body.title);
  }
  if (req.body.content) {
    post.excerpt = req.body.content.substring(0, 150) + '...';
  }

  post.updatedAt = new Date().toISOString();
  res.json({ status: 'success', data: post });
}
```

### 2. Slug Generation

Slugs are URL-friendly versions of titles:

```js
// utils/helpers.js
export function generateSlug(title) {
  return title
    .toLowerCase()
    .trim()
    .replace(/[^a-z0-9\s-]/g, '')    // Remove special chars
    .replace(/\s+/g, '-')            // Spaces → hyphens
    .replace(/-+/g, '-')             // Collapse multiple hyphens
    .replace(/(^-|-$)/g, '');        // Trim leading/trailing hyphens
}

// "My First Blog Post!" → "my-first-blog-post"
```

Ensure unique slugs:

```js
export function generateUniqueSlug(title, existingPosts) {
  let slug = generateSlug(title);
  let counter = 1;

  while (existingPosts.some(p => p.slug === slug)) {
    slug = `${generateSlug(title)}-${counter++}`;
  }

  return slug;
}
```

### 3. Soft Delete

Instead of permanently removing data, mark it as deleted:

```js
// models/Post.js
export function createPostModel(data) {
  return {
    id: nextPostId++,
    title: data.title,
    // ...other fields
    isDeleted: false,
    deletedAt: null,
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };
}
```

```js
// controllers/postController.js

// Soft delete
export function deletePost(req, res) {
  const post = findPostOrThrow(req.params.id);

  post.isDeleted = true;
  post.deletedAt = new Date().toISOString();
  post.updatedAt = new Date().toISOString();

  res.json({
    status: 'success',
    message: `Post "${post.title}" moved to trash`,
  });
}

// Restore from trash
export function restorePost(req, res) {
  const post = findDeletedPostOrThrow(req.params.id);

  post.isDeleted = false;
  post.deletedAt = null;
  post.updatedAt = new Date().toISOString();

  res.json({ status: 'success', data: post });
}

// Permanent delete
export function permanentlyDeletePost(req, res) {
  const post = findDeletedPostOrThrow(req.params.id);
  posts = posts.filter(p => p.id !== post.id);

  res.status(204).end();
}

// Filter out deleted posts in all queries
export function getAllPosts(req, res) {
  let result = posts.filter(p => !p.isDeleted);
  // ...filtering, sorting, pagination
}
```

**Routes for soft delete:**

```js
router.delete('/:id', asyncHandler(deletePost));           // Soft delete
router.patch('/:id/restore', asyncHandler(restorePost));    // Restore
router.delete('/:id/permanent', asyncHandler(permanentlyDeletePost));  // Hard delete

// Get trashed posts
router.get('/trash', asyncHandler(getTrashedPosts));
```

### 4. Bulk Operations

```js
// POST /api/posts/bulk — Create multiple posts
export function bulkCreatePosts(req, res) {
  const { posts: postsData } = req.body;

  if (!Array.isArray(postsData) || postsData.length === 0) {
    throw new AppError('posts must be a non-empty array', 400);
  }

  if (postsData.length > 50) {
    throw new AppError('Maximum 50 posts per bulk operation', 400);
  }

  const created = postsData.map(data => createPostModel(data));

  res.status(201).json({
    status: 'success',
    count: created.length,
    data: created,
  });
}

// PATCH /api/posts/bulk — Update multiple posts
export function bulkUpdatePosts(req, res) {
  const { updates } = req.body;
  // updates: [{ id: 1, status: 'published' }, { id: 2, status: 'published' }]

  const results = updates.map(update => {
    const post = posts.find(p => p.id === update.id && !p.isDeleted);
    if (!post) return { id: update.id, error: 'Not found' };

    const { id, ...fields } = update;
    Object.assign(post, fields, { updatedAt: new Date().toISOString() });
    return { id: post.id, status: 'updated' };
  });

  res.json({ status: 'success', data: results });
}

// DELETE /api/posts/bulk — Delete multiple posts
export function bulkDeletePosts(req, res) {
  const { ids } = req.body;

  if (!Array.isArray(ids) || ids.length === 0) {
    throw new AppError('ids must be a non-empty array', 400);
  }

  let deleted = 0;
  for (const id of ids) {
    const post = posts.find(p => p.id === id && !p.isDeleted);
    if (post) {
      post.isDeleted = true;
      post.deletedAt = new Date().toISOString();
      deleted++;
    }
  }

  res.json({ status: 'success', message: `${deleted} posts deleted` });
}
```

### 5. Data Relationships — Posts & Comments

**One-to-Many**: A post has many comments.

```js
// models/Comment.js
let comments = [];
let nextCommentId = 1;

export function createCommentModel(postId, data) {
  const comment = {
    id: nextCommentId++,
    postId,
    author: data.author,
    content: data.content,
    createdAt: new Date().toISOString(),
    updatedAt: new Date().toISOString(),
  };

  comments.push(comment);
  return comment;
}

export function getCommentsByPostId(postId) {
  return comments.filter(c => c.postId === postId);
}

export function getCommentById(id) {
  return comments.find(c => c.id === id);
}

export function deleteCommentsByPostId(postId) {
  comments = comments.filter(c => c.postId !== postId);
}

export { comments };
```

### 6. Comment Controller

```js
// controllers/commentController.js
import { AppError } from '../utils/AppError.js';
import { getPostById } from '../models/Post.js';
import {
  createCommentModel,
  getCommentsByPostId,
  getCommentById,
  comments,
} from '../models/Comment.js';

// GET /api/posts/:id/comments
export function getPostComments(req, res) {
  const postId = parseId(req.params.id);
  const post = getPostById(postId);
  if (!post) throw new AppError('Post not found', 404);

  const postComments = getCommentsByPostId(postId);

  res.json({
    status: 'success',
    count: postComments.length,
    data: postComments,
  });
}

// POST /api/posts/:id/comments
export function addComment(req, res) {
  const postId = parseId(req.params.id);
  const post = getPostById(postId);
  if (!post) throw new AppError('Post not found', 404);

  const comment = createCommentModel(postId, req.body);

  res.status(201).json({ status: 'success', data: comment });
}

// DELETE /api/posts/:postId/comments/:commentId
export function deleteComment(req, res) {
  const postId = parseId(req.params.id);
  const commentId = parseId(req.params.commentId);

  const comment = getCommentById(commentId);
  if (!comment || comment.postId !== postId) {
    throw new AppError('Comment not found', 404);
  }

  const index = comments.indexOf(comment);
  comments.splice(index, 1);

  res.json({ status: 'success', message: 'Comment deleted' });
}

function parseId(val) {
  const id = Number(val);
  if (isNaN(id)) throw new AppError('Invalid ID format', 400);
  return id;
}
```

### 7. Comment Routes (Nested)

```js
// routes/commentRoutes.js
import { Router } from 'express';
import { asyncHandler } from '../utils/asyncHandler.js';
import { validateBody } from '../middleware/validate.js';
import { getPostComments, addComment, deleteComment } from '../controllers/commentController.js';

// mergeParams allows access to :id from parent router
const router = Router({ mergeParams: true });

const commentSchema = {
  author: { required: true, type: 'string', minLength: 2, maxLength: 100 },
  content: { required: true, type: 'string', minLength: 1, maxLength: 2000 },
};

router
  .route('/:id/comments')
  .get(asyncHandler(getPostComments))
  .post(validateBody(commentSchema), asyncHandler(addComment));

router
  .route('/:id/comments/:commentId')
  .delete(asyncHandler(deleteComment));

export default router;
```

### 8. Categories — Many-to-One Relationship

```js
// models/Category.js
let categories = [];
let nextCategoryId = 1;

export function createCategoryModel(data) {
  const category = {
    id: nextCategoryId++,
    name: data.name,
    slug: data.name.toLowerCase().replace(/\s+/g, '-'),
    description: data.description || '',
    createdAt: new Date().toISOString(),
  };

  categories.push(category);
  return category;
}

export function getAllCategories() {
  return categories;
}

export function getCategoryById(id) {
  return categories.find(c => c.id === id);
}

export { categories };
```

### 9. Including Related Data

When fetching a post, include its comments and category:

```js
// controllers/postController.js
export function getOnePost(req, res) {
  const post = findPostOrThrow(req.params.id);

  // Include related data
  const postComments = getCommentsByPostId(post.id);
  const category = post.categoryId ? getCategoryById(post.categoryId) : null;

  // Increment view count
  post.views++;

  res.json({
    status: 'success',
    data: {
      ...post,
      category,
      comments: postComments,
      commentCount: postComments.length,
    },
  });
}
```

---

## 💪 Hands-On Exercise

### Extend the Blog API with Relationships

1. Create the `Comment` model with `createCommentModel`, `getCommentsByPostId`, `deleteCommentsByPostId`
2. Add nested comment routes: `GET /api/posts/:id/comments`, `POST /api/posts/:id/comments`
3. Create the `Category` model and CRUD endpoints
4. When deleting a post, also delete its comments (cascade)
5. When fetching a single post, include its comments and category name
6. Implement soft delete for posts with `DELETE`, `PATCH /:id/restore`, and `GET /trash`

---

## 📝 Homework

1. Implement `PATCH` for partial post updates (only update provided fields)
2. Add a `PUT` endpoint that replaces the entire post
3. Build a bulk publish endpoint: `PATCH /api/posts/bulk` that sets `status: 'published'` for an array of IDs
4. Add `commentCount` to each post in the `GET /api/posts` list response
5. Implement unique slug generation — if "my-post" exists, create "my-post-1"

---

## 💡 Pro Tips

- Use `PATCH` for most updates — clients shouldn't need to send the full object just to change one field
- Soft delete is essential for production apps — users expect an "undo" option
- Always validate array lengths in bulk operations to prevent abuse
- Use `mergeParams: true` when creating nested route handlers
- Include related data counts (commentCount) in list endpoints to reduce API calls

---

[← Day 1](day-01.md) | [Back to Week 14](README.md) | [Day 3 →](day-03.md)
