# Project Structure

How the two VIP repositories and this documentation vault are organized, and how they relate.

---

## Overview

```
C:\Users\mbg91918\
├── vip-web-frontend/     Next.js public website          (github: innovation-hub-tech-team)
├── vip-web-backend/      Django REST API                  (github: innovation-hub-tech-team)
└── VIP-Docs/             This Obsidian vault — not in either repo
```

The repos never import each other. The only coupling is the HTTP contract: versioned `/api/v1/...` routes plus the [[Error_Envelope]].

---

## vip-web-backend

```
vip-web-backend/
├── backend/                 Django project package
│   ├── settings.py          Env-driven config, DRF + spectacular settings
│   ├── urls.py              Root URLconf: /, /healthz/, /api/v1/staff/, /api/schema/, /api/docs/, /admin/
│   ├── views.py             hello() → "Hello, World!"
│   ├── health.py            healthz() → DB probe
│   ├── asgi.py / wsgi.py
├── events/                  Events Django app
│   ├── models.py            Event model + generate_unique_slug()
│   ├── serializers.py       EventSerializer (cross-field date validation, slug autogen)
│   ├── views.py             EventListCreateView, EventDetailView (PATCH-only, soft/hard delete)
│   ├── urls.py              events, events/<uuid:pk>
│   ├── exceptions.py        drf_exception_handler → {code, message, details}
│   └── migrations/          0001_initial, 0002_event_deleted_at
├── tests/                   pytest-django suites (events API, healthz, hello)
├── postman/                 vip-events.postman_collection.json
├── docs/                    Untracked scratch (standup prep); transcripts now live in VIP-Docs/Standup
├── .github/workflows/ci.yml Lint + test on PR and push to main
├── Dockerfile               python:3.12-slim, runserver 0.0.0.0:8000
├── docker-compose.yml       web + db (postgres:16), healthcheck, named volume
├── requirements.txt         Pinned dependencies
├── pyproject.toml           Ruff + pytest config
└── .env.example             Documented env vars (no secrets)
```

## vip-web-frontend

```
vip-web-frontend/
├── app/                     App Router — one folder per route, thin page.tsx files
│   ├── layout.tsx           Fonts, ThemeProvider, MotionProvider, NavBar, Footer, skip link, AxeReporter
│   ├── globals.css          Tailwind 4 + brand tokens
│   └── <route>/page.tsx     about, team, gallery, engage-with-us, innovation-challenge, challenge-partners-amplifiers, innovation-support-ecosystem, vip, the-source, resources/*, national-steam-innovation-challenge/*
├── components/
│   ├── global/              NavBar/, Footer/, AnnouncementBanner, ScrollIndicator
│   ├── modular_sections/    Reusable page sections (Hero, Faq, MediaFeature, ResourceLibrary, …)
│   ├── pages/               Page-specific sections (About, Home, Resources, …)
│   └── ui/                  shadcn primitives + backgrounds/ (animated background registry)
├── data/                    Typed content objects, one per page (<page>Data.tsx)
├── hooks/                   use-focus-trap, use-motion-preference, use-mounted, use-now
├── lib/                     utils.ts (cn), youtube.ts
├── public/                  images/, files/ (downloadable PDFs)
├── docs/                    Untracked Obsidian scratch vault (PR write-ups, audit screenshots)
├── CLAUDE.md                Project context + frontend standards (the de facto API contract, §5 and §9)
└── AGENTS.md
```

Notable unmerged branches: `feature/event-page` (draft PR), `feature/reusable-CTA` (PR into `staging`), `feature/payload-cms` (unmerged since 2026-07-22).

## VIP-Docs (this vault)

```
VIP-Docs/
├── .obsidian/          Config + snippets/mermaid-fit.css (responsive diagrams — do not remove)
├── README.md           Vault index
├── Project/            Tech stack, structure, phases, testing
├── Backend/            Django API docs
├── Frontend/           Next.js site docs
├── Infrastructure/     Docker, CI, AWS (planned), Payload CMS branch
├── SystemDesign/       C4-Diagrams/ 00–07
├── Standup/            Meeting transcripts, prep notes, supervisor questions
├── Ticket/             Work history + Resume_Highlights
└── _Templates/         Standup_Template, Ticket_Template
```

---

## Branching

| Repo | Flow | Notes |
|------|------|-------|
| Frontend | `feature/*`, `chore/*`, `update/*` → `staging` → `main` | Leo merges `staging` → `main` |
| Backend | `feature/*` → `main` | PRs reviewed and merged by Leo |

---

## Related Notes

- [[Tech_Stack]]
- [[Project_Phases]]
- [[04-Component-Frontend]]
- [[05-Component-Backend]]
