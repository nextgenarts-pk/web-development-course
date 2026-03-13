# 📅 Week 14 — Day 3: File Uploads & Static Files 📁

> **Full Stack Web Development Course — Integrated with AI** | [NextGen Arts](https://nextgenarts.pk)

---

## 🎯 Today's Goals

- Handle file uploads with Multer
- Validate file types and sizes
- Serve uploaded files as static assets
- Add cover images to blog posts
- Implement image resizing concepts

---

## 📖 Lesson Content

### 1. How File Uploads Work

Files are sent as **multipart/form-data** — not JSON. Express's `express.json()` cannot parse this. We use **Multer**.

```
Client Side:
<form enctype="multipart/form-data">
  <input type="file" name="image" />
</form>

Server Side:
Multer parses the file → stores it → attaches to req.file
```

### 2. Setting Up Multer

```bash
npm install multer
```

```js
// middleware/upload.js
import multer from 'multer';
import path from 'path';
import { AppError } from '../utils/AppError.js';

// Storage configuration
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');  // Save files in uploads/ directory
  },
  filename: (req, file, cb) => {
    // Generate unique filename: timestamp-random.ext
    const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1e9)}`;
    const ext = path.extname(file.originalname);
    cb(null, `${file.fieldname}-${uniqueSuffix}${ext}`);
  },
});

// File filter — only allow images
function fileFilter(req, file, cb) {
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif', 'image/webp'];

  if (allowedTypes.includes(file.mimetype)) {
    cb(null, true);  // Accept file
  } else {
    cb(new AppError('Only JPEG, PNG, GIF, and WebP images are allowed', 400), false);
  }
}

// Create upload middleware
export const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024,  // 5MB max
    files: 1,                    // 1 file at a time
  },
});
```

### 3. Single File Upload

```js
// routes/postRoutes.js
import { upload } from '../middleware/upload.js';

// Upload cover image for a post
router.post(
  '/:id/cover',
  upload.single('cover'),  // 'cover' matches the field name
  asyncHandler(uploadCoverImage)
);
```

```js
// controllers/postController.js
export function uploadCoverImage(req, res) {
  const post = findPostOrThrow(req.params.id);

  if (!req.file) {
    throw new AppError('No image file provided', 400);
  }

  // Store the file path
  post.coverImage = `/uploads/${req.file.filename}`;
  post.updatedAt = new Date().toISOString();

  res.json({
    status: 'success',
    data: {
      coverImage: post.coverImage,
      fileInfo: {
        originalName: req.file.originalname,
        size: req.file.size,
        mimetype: req.file.mimetype,
      },
    },
  });
}
```

### 4. Multiple File Uploads

```js
// Upload multiple images (gallery)
router.post(
  '/:id/gallery',
  upload.array('images', 10),  // Max 10 files
  asyncHandler(uploadGallery)
);

export function uploadGallery(req, res) {
  const post = findPostOrThrow(req.params.id);

  if (!req.files || req.files.length === 0) {
    throw new AppError('No files uploaded', 400);
  }

  const imageUrls = req.files.map(file => `/uploads/${file.filename}`);

  // Add to post's gallery
  post.gallery = [...(post.gallery || []), ...imageUrls];
  post.updatedAt = new Date().toISOString();

  res.json({
    status: 'success',
    count: req.files.length,
    data: { gallery: post.gallery },
  });
}
```

### 5. Mixed Fields — Files + Text Data

```js
// Upload with body data simultaneously
const uploadFields = upload.fields([
  { name: 'cover', maxCount: 1 },
  { name: 'gallery', maxCount: 5 },
]);

router.post('/', uploadFields, asyncHandler(createPostWithImages));

export function createPostWithImages(req, res) {
  // req.body has text fields (title, content, etc.)
  // req.files.cover[0] has the cover image
  // req.files.gallery has gallery images

  const post = createPostModel(req.body);

  if (req.files?.cover?.[0]) {
    post.coverImage = `/uploads/${req.files.cover[0].filename}`;
  }

  if (req.files?.gallery) {
    post.gallery = req.files.gallery.map(f => `/uploads/${f.filename}`);
  }

  res.status(201).json({ status: 'success', data: post });
}
```

### 6. Serving Static Files

```js
// app.js — Serve uploaded files
import { fileURLToPath } from 'url';
import path from 'path';

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

// Serve files from the uploads directory
app.use('/uploads', express.static(path.join(__dirname, '..', 'uploads')));

// Now files are accessible at: http://localhost:3000/uploads/cover-12345.jpg
```

Create the uploads directory:

```bash
mkdir uploads
```

Add to `.gitignore`:

```
uploads/*
!uploads/.gitkeep
```

### 7. File Deletion

```js
import fs from 'fs/promises';
import path from 'path';

export async function deleteCoverImage(req, res) {
  const post = findPostOrThrow(req.params.id);

  if (!post.coverImage) {
    throw new AppError('Post has no cover image', 400);
  }

  // Delete file from disk
  const filePath = path.join('uploads', path.basename(post.coverImage));
  try {
    await fs.unlink(filePath);
  } catch (err) {
    // File may already be deleted — that's ok
    console.warn(`File not found: ${filePath}`);
  }

  post.coverImage = null;
  post.updatedAt = new Date().toISOString();

  res.json({ status: 'success', message: 'Cover image removed' });
}
```

### 8. Multer Error Handling

```js
// middleware/upload.js — Add error handler
export function handleUploadError(err, req, res, next) {
  if (err instanceof multer.MulterError) {
    const messages = {
      LIMIT_FILE_SIZE: 'File too large — maximum 5MB allowed',
      LIMIT_FILE_COUNT: 'Too many files uploaded',
      LIMIT_UNEXPECTED_FILE: 'Unexpected file field name',
    };

    return res.status(400).json({
      status: 'error',
      message: messages[err.code] || 'File upload error',
    });
  }

  next(err);
}
```

```js
// app.js — Register after routes, before errorHandler
app.use(handleUploadError);
app.use(errorHandler);
```

### 9. Memory Storage (for Processing)

If you need to process files before saving (e.g., resize):

```js
// Store file in memory as a Buffer (don't save to disk yet)
const memoryStorage = multer({
  storage: multer.memoryStorage(),
  fileFilter,
  limits: { fileSize: 5 * 1024 * 1024 },
});

// req.file.buffer contains the file data
export function processAndSave(req, res) {
  if (!req.file) throw new AppError('No file provided', 400);

  console.log('File buffer size:', req.file.buffer.length);
  console.log('MIME type:', req.file.mimetype);

  // You could process the buffer here (sharp, etc.)
  // Then save manually with fs.writeFile
}
```

### 10. Complete Upload Routes

```js
// routes/postRoutes.js
import { upload, handleUploadError } from '../middleware/upload.js';

// Post CRUD
router.route('/').get(asyncHandler(getAllPosts)).post(asyncHandler(createPost));
router.route('/:id').get(asyncHandler(getOnePost)).patch(asyncHandler(updatePost)).delete(asyncHandler(deletePost));

// File uploads
router.post('/:id/cover', upload.single('cover'), asyncHandler(uploadCoverImage));
router.delete('/:id/cover', asyncHandler(deleteCoverImage));
router.post('/:id/gallery', upload.array('images', 10), asyncHandler(uploadGallery));
```

### 11. Testing File Uploads

```bash
# Upload a cover image with curl
curl -X POST http://localhost:3000/api/posts/1/cover \
  -F "cover=@/path/to/image.jpg"

# Upload multiple gallery images
curl -X POST http://localhost:3000/api/posts/1/gallery \
  -F "images=@photo1.jpg" \
  -F "images=@photo2.jpg"

# Delete cover image
curl -X DELETE http://localhost:3000/api/posts/1/cover
```

In **Postman**:
1. Set method to `POST`
2. Go to **Body** → **form-data**
3. Set key to `cover`, change type to **File**
4. Select your image file

---

## 💪 Hands-On Exercise

### Add Image Support to the Blog API

1. Install Multer and configure disk storage in `middleware/upload.js`
2. Create the `uploads/` directory and serve it as static
3. Add `POST /api/posts/:id/cover` — upload single cover image
4. Add `DELETE /api/posts/:id/cover` — remove cover image
5. Add file validation (JPEG/PNG only, max 5MB)
6. Handle Multer errors with descriptive messages
7. Test uploads with curl or Postman

---

## 📝 Homework

1. Implement `POST /api/posts/:id/gallery` for multiple image uploads (max 10)
2. Add a `GET /api/posts/:id/gallery` endpoint that returns all gallery images
3. Ensure cover image URL is included in the post response from `GET /api/posts/:id`
4. Add an avatar upload for comment authors: `POST /api/posts/:id/comments` with a file field
5. Write a cleanup script that deletes orphaned files (files not referenced by any post)

---

## 💡 Pro Tips

- Always validate MIME types server-side — client extensions can be faked
- Use `path.basename()` when constructing file paths to prevent directory traversal attacks
- Set reasonable file size limits — 5MB for images, 50MB for videos
- Use memory storage when you need to process files (resize, compress) before saving
- Consider cloud storage (AWS S3, Cloudinary) for production — local storage doesn't scale
- Always add `uploads/` to `.gitignore` — don't commit uploaded files

---

[← Day 2](day-02.md) | [Back to Week 14](README.md) | [Day 4 →](day-04.md)
