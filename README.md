# VIP — Virtual Immersive Platform

Documentation vault for the **Global HSI Equity Innovation Hub / Virtual Immersive Platform (VIP)** — the public website (`vip-web-frontend`) and its Django API (`vip-web-backend`). VIP is meant to grow into a wider ecosystem (web apps, iOS, React Native, Apple Vision Pro, AR/VR), with the Django backend becoming the shared data layer for every client.

This vault lives **outside both repos** (`C:\Users\mbg91918\VIP-Docs`) so it can document the whole system and be updated independently when either repo merges to `main`.

---

## Repositories

| Repo | Path | Stack | Phase |
|------|------|-------|-------|
| `vip-web-frontend` | `C:\Users\mbg91918\vip-web-frontend` | Next.js 16, React 19, TypeScript, Tailwind 4, shadcn/ui, Motion | Phase 1 — Version 0 website |
| `vip-web-backend` | `C:\Users\mbg91918\vip-web-backend` | Django 6, DRF 3.17, drf-spectacular, PostgreSQL 16, Docker | Phase 1.5 — Backend foundation |

Both live under `github.com/innovation-hub-tech-team`. Work flows `feature/*` → `staging` → `main` on the frontend, and `feature/*` → `main` on the backend.

---

## Current State (as of 2026-09-30)

- **Frontend** is a static, content-driven site: every page reads a typed content object from `data/<page>Data.tsx`. There is no data fetching yet.
- **Backend** has a working staff events CRUD API at `/api/v1/staff/events`, a `/healthz/` probe, a standard error envelope, and Swagger UI at `/api/docs/`.
- **The two are not wired together on `main` yet.** On the unmerged `feature/events` branches, the Events page fetches events from the staff API on the Next.js server and renders them as cards. See [[Events_API_Integration]] and [[Events_API_Demo]].
- **AWS hosting is planned, not built.** See [[AWS_Architecture]].

---

## Documentation

### Quick Links

- [[Tech_Stack]] — Technologies across the whole project
- [[Project_Structure]] — Both repos and this vault
- [[Project_Phases]] — Phase 1 / 1.5 / 2 / 3 scope
- [[Testing]] — Test strategy and tooling
- [[Next_Steps_2026-10-01]] — *(proposal)* Backlog and work split after the Oct 1 stand-up
- [[SystemDesign/README|System Design]] — C4 architecture diagrams

---

### Backend

- [[Events_API]] — Staff events CRUD, soft/hard delete, validation
- [[Health_Endpoints]] — `/healthz/` database probe and hello route
- [[Error_Envelope]] — `{code, message, details}` API error contract
- [[OpenAPI_Swagger]] — drf-spectacular schema, Swagger UI, Postman
- [[Configuration]] — Environment variables and settings

---

### Frontend

- [[Pages_And_Routing]] — App Router page map
- [[Modular_Sections]] — Reusable section component catalog
- [[Content_Data_Contract]] — `data/` content objects and the future-CMS contract
- [[Events_Page]] — `/events` page and the `EventItem` type
- [[Navigation_Layout]] — Root layout, NavBar, Footer
- [[Theming_Motion_Backgrounds]] — Tokens, dark mode, motion, animated backgrounds
- [[Accessibility]] — a11y requirements and tooling

---

### Infrastructure

- [[Docker_Local_Dev]] — Docker Compose `web` + `db`
- [[CI_GitHub_Actions]] — Backend lint + test pipeline
- [[AWS_Architecture]] — *(planned)* Amplify, ECS/ECR, RDS, S3 + CloudFront
- [[Payload_CMS_Branch]] — *(unmerged)* Payload CMS experiment and ownership question

---

### System Design

- [[00-C4-Overview]] — C4 index and key decisions
- [[01-SystemLandscape]] · [[02-SystemContext]] · [[03-Container]]
- [[04-Component-Frontend]] · [[05-Component-Backend]] · [[06-Code]] · [[07-Dynamic-Diagrams]]

---

### Standup

Meeting transcripts, stand-up prep notes, and supervisor question lists. Read the relevant ones before starting a ticket. See [[Standup/README|Standup index]].

---

### Ticket

A running history of the work I've shipped, with resume-ready bullets. See [[Ticket/README|Ticket index]] and [[Resume_Highlights]].

---

## Conventions

- File names use `PascalCase_Underscores`. Notes link with `[[wiki-links]]`.
- C4 files are numbered `NN-Name.md`.
- Diagrams are **Mermaid** (native in Obsidian). The palette and init block are in [[00-C4-Overview]].
- **Accuracy rule:** anything planned but not built is labeled *(planned)* and drawn dashed.
- New standups and tickets start from `_Templates/`.
- Claude keeps this vault in sync using the `vip-docs-sync` skill (`~/.claude/skills/vip-docs-sync/`).
