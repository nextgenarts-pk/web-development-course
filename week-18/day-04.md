# Week 18 - Day 4: Full Stack Testing Strategy

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Test backend endpoints and frontend behavior
- Catch integration regressions early
- Build confidence before deployment

## Testing Pyramid for Full Stack

1. Unit tests: utility functions, hooks, pure logic
2. Integration tests: API routes + DB behavior
3. End-to-end tests: complete user flows in browser

## Backend Example (Node test runner + supertest)

```js
import test from 'node:test';
import assert from 'node:assert/strict';
import request from 'supertest';
import app from '../src/app.js';

test('GET /health returns ok', async () => {
  const res = await request(app).get('/health');
  assert.equal(res.statusCode, 200);
  assert.equal(res.body.status, 'ok');
});
```

## Frontend Example (React Testing Library)

```jsx
import { render, screen } from '@testing-library/react';
import DashboardPage from './DashboardPage';

test('shows dashboard heading', () => {
  render(<DashboardPage />);
  expect(screen.getByText(/dashboard/i)).toBeInTheDocument();
});
```

## End-to-End Scope

- Signup -> login -> create item -> edit -> delete -> logout
- Unauthorized user blocked from admin route

## Hands-On Exercise

- Write 3 backend tests and 3 frontend tests
- Add one end-to-end scenario checklist

## Homework

- Add a CI command to run all tests before merge

---

[<- Day 3](day-03.md) | [Back to Week 18](README.md) | [Day 5 ->](day-05.md)
