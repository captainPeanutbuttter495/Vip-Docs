---
title: "Events API Integration (model v2 + real data)"
date_started: 2026-09-24
date_finished:
repo: both
branch: "backend feature/events (off main, pushed) · frontend feature/events (off origin/feature/event-page, uncommitted)"
pr:
status: in-progress
standups: ["[[Standup_2026-09-24]]", "[[OneOnOne_2026-09-25_Backend_Infra]]", "[[Standup_2026-10-01]]"]
skills: [Django REST Framework, Data Modeling, Next.js, API Integration, Full-Stack]
tags: [ticket, frontend, backend, integration]
---

# Events API Integration (model v2 + real data)

The first frontend↔backend integration: remodel the Django `Event` to match the card design, then have the `/events` page GET real data from the local backend as a proof of concept.

**Status (2026-10-05):** the core flow is built (Events API → GET → Events page → event cards). Backend sub-tasks 1–3 are pushed. Frontend 5–6 are pushed to `origin/feature/events` (`c3b9c06`), and the disabled "Coming Soon" CTA (sub-task 7) is done and browser-verified. Still open:
- static + API compatibility (6b, next);
- the Postman/README/seed docs (4);
- the end-to-end check (8);
- the PRs (9).

The rate limiter and the ECS research are on hold until this ticket ships. See [[Events_API_Integration_Plan]] for the checklist and [[Events_API_Demo]] for the standup demo.

---

## Ticket

From the 2026-09-24 stand-up and the 2026-09-25 Backend Infra 1:1:

- Remodel `Event` "design first, then backend."
- POST some lorem events by hand.
- The Next.js page fetches them and builds cards, replacing the six hard-coded placeholders. Filters keep working.
- Register links out if there's a URL, or shows a disabled "Coming soon".

## Requirements vs. Done

| Ticket requirement | State |
|---|---|
| Model fields: id, title, slug (optional), description, category (4 values), date, where (in-person / online / hybrid), host, audience, url (optional) | **Done.** Date is `start_datetime` + optional `end_datetime`. Where is `format` + `location` + `online_url`, with per-format validation. Audiences: students, community, educators, families. Plus an optional `tag` for the card chip |
| GET the events from the page | **Done.** Server-side fetch in `lib/api/events.ts` |
| Replace the static event data | **Done.** `PLACEHOLDER_EVENTS` removed |
| Map API fields to the existing UI | **Done.** `toEventItem()`; no card component changes |
| Optional fields don't break the card | **In code.** Empty `tag` hides the chip, a null `end_datetime` shows the start only, an empty `url` gives "Coming Soon". Not yet checked in the browser (sub-task 8) |
| URL → active CTA | **Done.** Register opens the URL in a new tab |
| No URL → disabled, grayed-out "Coming Soon" | **Done** (2026-10-05). A real `disabled` button: grayed out, not clickable, skipped by Tab, `cursor-not-allowed`. Browser-verified |
| Events added by hand with POST | **Works** through the API. The Postman collection still sends the old `event_type` (sub-task 4); the demo note has a curl seed script |

## What I Did

- **Backend** (`vip-web-backend`, `feature/events`)
  - Contract tests first, split by feature: `tests/events_helpers.py`, `tests/test_events_api.py` (CRUD), `tests/test_events_fields.py` (card fields).
  - The `Event` model now has the card fields. `event_type` was removed and the `category` choices replaced. Hand-written migration `0003_event_card_fields`, including a `RunPython` step that maps the old categories.
  - `EventSerializer.validate()` enforces the per-format rules against the merged state, so a partial PATCH is validated too. All cross-field errors come back together.
- **Frontend** (`vip-web-frontend`, `feature/events`)
  - `lib/api/events.ts` has these parts:
    - the `ApiEvent` type, which mirrors the serializer;
    - `getEvents()`, a Next.js server-side `fetch` with `no-store`, reading the `VIP_API_BASE_URL` env var (which never reaches the browser). On any failure it logs and returns `[]`, so the grid shows its empty message;
    - `toEventItem()`, which maps the API data to the card. Dates are formatted in Pacific time with `Intl.DateTimeFormat`.
  - `app/events/page.tsx` is now an async server component.

## Testing

- **Backend:** pytest gives 60 passed. `ruff check`, `ruff format --check` and `makemigrations --check --dry-run` are clean.
- **Frontend:** `npx tsc --noEmit` is clean. `npm run lint` gives 0 errors; its 18 warnings are all pre-existing and none are in the changed files. Not yet checked in the browser.

## Open Questions (summary)

- Chip values
- "Apply" vs. "Register"
- Single vs. multiple audience
- Timezone and date format
- Sorting and hiding past events
- Public `GET /api/v1/events` vs. the staff route
- Whether drafts are shown

## Resume Bullets (draft — finalize when shipped)

- **Led** the first frontend-to-backend integration: reconciled a Figma-driven UI contract with the Django data model and designed a v2 schema and migration plan.
- **Remodeled** the Django `Event` API contract-first: wrote the failing pytest contract tests before the model, migration and serializer changes, finishing with 60 passing tests and format-dependent cross-field validation.
- **Connected** the Next.js Events page to the Django REST API with a server-side fetch and a typed mapper. Static placeholder data was replaced with no card component changes, and the page falls back to an empty state when the backend is unavailable.

---

## Related Notes

- [[Events_API_Integration_Plan]] — plan, sub-task checklist, session log
- [[Events_API_Demo]] — standup demo guide
- [[Events_API]]
- [[Events_Page]]
- [[Event_Page]]
- [[Events_CRUD_Swagger]]
