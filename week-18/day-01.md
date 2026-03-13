# Week 18 - Day 1: Full Stack Project Architecture at Scale

> Full Stack Web Development Course - Integrated with AI | [NextGen Arts](https://nextgenarts.pk)

---

## Goals

- Organize frontend and backend cleanly
- Plan domain modules and ownership boundaries
- Prepare for team collaboration

## Architecture Styles

1. Separate repositories
- frontend repo and backend repo
- easy platform-specific pipelines

2. Monorepo
- shared tooling and shared types
- unified dependency and CI management

## Recommended App Boundaries

- Auth module
- Users module
- Notes/Tasks module
- Admin module
- Shared utility layer

## Example Monorepo Layout

```text
fullstack-app/
  apps/
    web/
    api/
  packages/
    shared-types/
    ui/
  docs/
```

## API Contract Discipline

- Version endpoints (/api/v1)
- Keep response shape consistent
- Never break existing clients without versioning

## Hands-On Exercise

- Refactor your app structure into clear modules
- Add one docs file that defines endpoint contracts

## Homework

- Create a decision document: monorepo vs separate repos for your project

---

[<- Back to Week 18](README.md) | [Day 2 ->](day-02.md)
