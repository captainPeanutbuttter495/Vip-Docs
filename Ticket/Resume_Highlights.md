---
role: "TODO confirm exact title — Global HSI Equity Innovation Hub, VIP Tech Team"
period: "2026-06 → present"
updated: 2026-09-27
tags: [resume]
---

# Resume Highlights

The strongest bullets from the [[Ticket/README|ticket history]], deduplicated and grouped by skill. Pick 3–5 per application, depending on the role.

---

## Summary Line

Full-stack developer on the Global HSI Equity Innovation Hub's Virtual Immersive Platform. I built the Django REST backend from scratch (Docker, PostgreSQL, CI, OpenAPI) and ship accessible, Figma-driven features on the Next.js/TypeScript public site.

---

## Backend / API

- Designed and built a versioned REST API (`/api/v1`) in **Django REST Framework** with UUID keys, slug generation, and cross-field validation. → [[Events_API_DRF]]
- Implemented full CRUD with recoverable **soft delete**, an opt-in hard delete, and PATCH-only updates validated against merged state. → [[Events_CRUD_Swagger]]
- Standardized every API error into one `{code, message, details}` envelope with a custom DRF exception handler, giving web and future mobile clients a predictable contract. → [[Events_API_DRF]]
- Generated interactive **OpenAPI 3 / Swagger** documentation with drf-spectacular. → [[Events_CRUD_Swagger]]
- Implemented a database-aware **health-check** endpoint with correct 200/503 semantics for load balancer and ECS readiness. → [[Health_Check_Endpoint]]

## DevOps / Infrastructure

- Containerized the backend with **Docker Compose** (Django + PostgreSQL 16), using health-gated startup and persistent volumes. → [[Backend_Docker_Setup]], [[PostgreSQL_Setup]]
- Built a **GitHub Actions** CI pipeline: parallel Ruff lint and pytest jobs, a Postgres service container, and a missing-migration gate on every PR. → [[GitHub_Actions_CI]]
- Hardened configuration with required environment variables and fail-fast startup. No secrets in the repo and no insecure fallbacks. → [[PostgreSQL_Setup]]

## Testing & Quality

- Grew the backend suite to **26 pytest tests** running in CI against PostgreSQL, including simulated database outages. → [[Events_CRUD_Swagger]], [[Health_Check_Endpoint]]

## Frontend (Next.js / React / TypeScript)

- Built a filterable **events listing** with a minimal client boundary and a typed data contract, designed so the data source could be swapped for a REST API. → [[Event_Page]]
- Translated Figma designs into responsive, theme-aware pages and reusable section components (card groups, FAQ, CTA cards). → [[VIP_Landing_Page]], [[National_Challenge_UI_Updates]], [[Reusable_CTA]]
- Diagnosed and fixed a viewport-height-dependent **CSS cropping bug** in a shared hero component used on four pages. → [[Hero_Cropping_Fix]]
- Extended shared components with backward-compatible props without breaking existing consumers. → [[Event_Page]], [[VIP_About_And_Resource_Cards]]

## Accessibility

- Built components to **WCAG 2.1 AA**: semantic lists and `<dl>`s, `aria-pressed` filter chips, `aria-live` result counts, sr-only labels, and verified contrast ratios (e.g. 5.4:1, 10:1). → [[Reusable_CTA]], [[Event_Page]]

## Data, Documentation & Collaboration

- Extracted partner-program content from intake forms into typed data, with every field traceable and nothing guessed. Kept a 12-item blockers log and 36 flagged decisions for stakeholders. → [[The_Source_Data]]
- Wrote PR write-ups with root-cause analysis, before/after screenshots, and accessibility notes. Prepared standup question lists that unblock decisions quickly. → [[Standup/README|Standups]]
- Reconciled a Figma-driven UI contract with the backend data model to plan the first frontend↔backend integration. → [[Events_API_Integration]]

---

## Tech Keywords

Python · Django · Django REST Framework · drf-spectacular / OpenAPI · PostgreSQL · pytest · Ruff · Docker · Docker Compose · GitHub Actions · Next.js (App Router) · React 19 · TypeScript · Tailwind CSS · shadcn/ui · Framer Motion · WCAG 2.1 AA · Figma · Postman · REST API design
