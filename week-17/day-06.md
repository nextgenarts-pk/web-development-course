# Week 17 - Day 6: Project Day - Full Stack Notes Dashboard

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Project Goal

Build a complete full stack notes dashboard using your Week 15 backend and React frontend.

## Required Features

- Auth pages: signup, login, logout
- Protected dashboard route
- Notes list with search, sort, pagination
- Create, update, delete notes
- Folder and tag filters
- Pin/favorite toggles
- User profile update

## Suggested Frontend Structure

```text
frontend/src/
  components/
    ProtectedRoute.jsx
    NoteCard.jsx
    NoteForm.jsx
    SearchBar.jsx
    Pagination.jsx
  context/
    AuthContext.jsx
  hooks/
    useRequest.js
  pages/
    LoginPage.jsx
    SignupPage.jsx
    DashboardPage.jsx
    ProfilePage.jsx
  services/
    apiClient.js
    authService.js
    notesService.js
```

## API Endpoints to Integrate

- POST /api/auth/signup
- POST /api/auth/login
- GET /api/auth/me
- PATCH /api/auth/update-me
- GET /api/notes
- POST /api/notes
- PATCH /api/notes/:id
- DELETE /api/notes/:id
- PATCH /api/notes/:id/pin
- PATCH /api/notes/:id/favorite

## Minimum Acceptance Checklist

- All protected pages redirect unauthenticated users to login
- User can manage notes without page reloads
- API errors are visible and actionable
- Loading states exist for all async screens
- App works on desktop and mobile widths

## Bonus Ideas

- Dark/light theme toggle
- Autosave draft notes
- Keyboard shortcuts (new note, search focus)

---

[<- Day 5](day-05.md) | [Back to Week 17](README.md) | [Next Week ->](../week-18/README.md)
