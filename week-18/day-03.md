# Week 18 - Day 3: Performance and Data Efficiency

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Reduce unnecessary network traffic
- Improve perceived performance in UI
- Optimize API query performance

## Frontend Optimizations

- Debounced search requests
- Pagination and lazy loading
- Memoization for expensive renders
- Cache list responses per filter key

```jsx
import { useMemo } from 'react';

const visibleNotes = useMemo(() => {
  return notes.filter((n) => n.title.toLowerCase().includes(search.toLowerCase()));
}, [notes, search]);
```

## Backend Optimizations

- Add indexes for frequently queried fields
- Return only required fields with select
- Use lean() where document methods are not needed
- Support limit/page query params

```js
const notes = await Note.find(filter)
  .select('title category isPinned createdAt')
  .sort('-createdAt')
  .skip((page - 1) * limit)
  .limit(limit)
  .lean();
```

## Hands-On Exercise

- Add debounce search to one page
- Add pagination controls and backend support

## Homework

- Measure one endpoint before and after optimization

---

[<- Day 2](day-02.md) | [Back to Week 18](README.md) | [Day 4 ->](day-04.md)
