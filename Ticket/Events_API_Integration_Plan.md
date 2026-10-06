---
title: "Events API Integration — Plan & Progress"
date_started: 2026-09-28
repo: both
branch: "backend feature/events (off main) · frontend feature/events (off origin/feature/event-page)"
status: in-progress
parent: "[[Events_API_Integration]]"
standups: ["[[OneOnOne_2026-09-25_Backend_Infra]]", "[[Standup_2026-10-01]]"]
tags: [ticket, plan, frontend, backend, integration]
---
stex
# Events API Integration — Plan & Progress

This note is **the source of truth and memory** for [[Events_API_Integration]]. A new Claude chat should read it first, do **only the next unchecked sub-task**, then stop for review.

Goal: Events API → GET request → Events page → event cards. Events are added by hand with POST (Postman). This ticket does **not** include auth, an admin UI, a CMS, or deployment.

---

## Rules for Claude (read every session)

1. Read this whole note, then `.claude/tasks/CURRENT` if it exists. Run `git status` in both repos to confirm where things stand.
2. Do **one** sub-task at a time: the first unchecked box below. Don't start the next one until the user says so.
3. At the end of a sub-task, show a file table (`File | Status | Description`), the command output that proves it works, and a suggested commit message. Then **stop**.
4. After the user confirms, tick the box (`- [x]`) and add a line to the **Session Log** at the bottom. Put anything unexpected under **Notes / Deviations**.
5. **Never** run `git add`, `git commit`, or `git push`. Creating branches is fine.
6. If a decision below turns out wrong, or Leo answers one of the questions, update **Decisions** first and log it. Don't silently change the plan.
7. Stay in scope. Put adjacent fixes under **Follow-ups**.

Repos: `C:\Users\mbg91918\vip-web-backend` and `C:\Users\mbg91918\vip-web-frontend`.

---

## Sub-tasks

### Phase 0 — Setup

- [x] **0. Create branches.**
  - Backend: `git checkout main && git pull && git checkout -b feature/events`.
  - Frontend: `git fetch origin && git checkout -b feature/events origin/feature/event-page`.
  - The frontend has an uncommitted `.gitignore` change and an untracked `AMPLIFIER_CONTEXT.md` on `main`. Check with the user before switching branches.
  - *Done when:* both repos are on `feature/events` and nothing was lost.

### Phase 1 — Backend (`vip-web-backend`)

Contract first: the tests are written before the code.

- [x] **1. Contract tests (failing).** Update `tests/test_events_api.py`:
  - Update the `EVENT_KEYS` set and the `make_payload()` helper to the new fields.
  - Drop the `event_type` usage.
  - Add tests:
    - one happy path per format;
    - `in_person` without `location` → 400 on `location`;
    - `online` without `online_url` → 400;
    - `hybrid` missing either field → 400;
    - PATCH `format` to `online` without `online_url` → 400 (merged state);
    - invalid `category` / `audience` / `tag` → 400 `validation_error`;
    - `url`, `tag` and `slug` omitted → 201 with `""` or a generated slug;
    - malformed `url` → 400.
  - *Done when:* `pytest` runs and the new tests **fail for the right reason**, meaning the fields are missing, not a syntax error.
- [x] **2. Model + migration.**
  - `events/models.py`:
    - remove `EventType` and `event_type`;
    - replace the `Category` choices;
    - add the `Tag`, `Format` and `Audience` choices;
    - add the fields `tag`, `format`, `location`, `online_url`, `host`, `audience` and `url`.
  - Create the migration with `python manage.py makemigrations events` (it becomes `0003_…`). Give existing rows one-off defaults, and add a `RunPython` step that maps the old categories to `event`.
  - `events/serializers.py`: update the `fields` list (moved here from sub-task 3, see Decisions). Without it the serializer still names `event_type` and every test errors instead of failing.
  - *Done when:* `migrate` succeeds, `makemigrations --check --dry-run` is clean, and `pytest` is green **except** the 5 format-rule tests (22, 23, 24 ×2, 25) in `tests/test_events_fields.py`.
- [x] **3. Serializer + validation.**
  - `events/serializers.py`: add the format rules to `validate()`, using the existing merged-state pattern (`attrs.get(x, getattr(self.instance, x, None))`).
  - *Done when:* `pytest`, `ruff check .` and `ruff format --check .` all pass.
- [ ] **4. Docs + seed data.**
  - Postman collection: new request bodies, plus one sample POST per category.
  - `README.md`: the new field list and a "seed events locally" note.
  - Update `SPECTACULAR_SETTINGS.DESCRIPTION` only if it mentions event fields.
  - Check that `/api/docs/` shows the new fields.
  - Seed 4–6 events locally, mixing formats, with some missing a `url`.
  - *Done when:* `GET /api/v1/staff/events` returns the seeded events in the new shape.

### Phase 2 — Frontend (`vip-web-frontend`)

- [x] **5. API client.**
  - Create `lib/api/events.ts` with:
    - an `ApiEvent` type matching the contract below;
    - `getEvents()`: a server-side `fetch` of `${VIP_API_BASE_URL}/api/v1/staff/events` with `cache: "no-store"`. On a missing env var, a non-2xx response or a network error, it logs with `console.error` and returns `[]`;
    - the `toEventItem()` mapper.
  - Create `.env.example` containing `VIP_API_BASE_URL=http://localhost:8000`.
  - *Done when:* `npx tsc --noEmit` and `npm run lint` pass.
- [x] **6. Wire the page.**
  - `app/events/page.tsx`: make it `async`, call `getEvents()`, and pass the result as `events` into `EventCardGrid`.
  - `data/eventsData.tsx`: delete `PLACEHOLDER_EVENTS`. `eventsGridContent` keeps its config with `events: []`.
  - *Done when:* `/events` shows the seeded backend events, and the category chips and audience filter work.
- [ ] **6b. Static + API compatibility (added 2026-10-01).**
  - From [[Standup_2026-10-01]]: the page must work from static frontend data *and* the backend, so it can go live before AWS. Design in [[Next_Steps_2026-10-01]].
  - Static events file in the `ApiEvent` shape, mapped by the same `toEventItem()`.
  - `getEvents()`: env var unset → static only; backend error → static + `console.error`; backend OK → merge, dedupe by `slug` (API wins), sort by `start_datetime`.
  - *Done when:* tsc + lint pass, and `/events` renders in all three modes.
- [x] **7. "Coming Soon" CTA.**
  - `components/modular_sections/EventCardGrid/EventCard.tsx`: when `CardAction` has no `href`, render a real disabled button (`disabled`, grayed out, `cursor-not-allowed`).
  - This is the only component change.
  - *Done when:*
    - an event with a `url` shows Register, which opens in a new tab;
    - an event without one shows a disabled "Coming Soon" button.

### Phase 3 — Verify & wrap up

- [ ] **8. End-to-end check.**
  - Backend running, frontend `npm run dev`, then check `/events`:
    - the cards match the data;
    - the filters work;
    - both CTA states work;
    - empty optional fields don't break a card;
    - light/dark mode looks right at 375, 834 and 1440.
  - Stop Django and reload: the page must show the empty message, not an error.
- [ ] **9. Wrap-up.**
  - Backend: run `/commit-ready`.
  - Frontend: the file table and commit messages. Its PR targets `staging`.
  - Write open questions to `docs/standup_<Mon>_<D>.md`.
  - Update [[Events_API_Integration]] (status, what I did, resume bullets) using the vip-docs-sync skill.

---

## Decisions (2026-09-27)

| Topic | Decision |
|---|---|
| Card chip | New **optional** `tag` choice field with all 6 values. The existing `status` (draft/published/archived, read-only) is unchanged. |
| Date | Keep `start_datetime` (required) and `end_datetime` (optional). End ≥ start is still checked. |
| GET route | The frontend reads the existing `/api/v1/staff/events`. A public route and auth come later. |
| Fetch location | The fetch runs on the **Next.js server**, so no CORS is needed and the backend URL never reaches the browser. |
| `event_type` | Removed. The ticket leaves it out, and `category` now carries the type. |
| `host`, `audience` | Required. |
| Branches | Both repos use `feature/events`. Backend branches off `main`. Frontend branches off `origin/feature/event-page`, and its PR targets `staging`. |
| Sub-task order (2026-09-28) | The serializer `fields` list update moves from sub-task 3 into sub-task 2, so each step is a clean red→green cycle: 2 leaves only the 5 format-rule tests red, 3 turns them green. Don't push the branch until it's green (CI runs pytest). |
| Test layout (2026-09-28) | Tests are grouped by feature. Shared constants and helpers live in `tests/events_helpers.py`; `tests/test_events_api.py` keeps the CRUD lifecycle (tests 1–20), and `tests/test_events_fields.py` holds the card-field contract (tests 21–30). Split again only when a file gets hard to read or a second domain app arrives. |
| Sub-task order (2026-09-29) | Frontend first: do **5 → 6** next, then **4** (docs + seed data). The API client needs only the contract, not seeded data. Sub-task 6 is checked against lint/tsc and the empty-state message (the backend has no rows yet); its "shows the seeded events" check moves to after 4. Sub-task 4 must still be done before the backend PR (Postman still sends `event_type`). |
| Compatibility (2026-10-01) | Leo: the Events page must render from static frontend JSON *and* the backend, and ship as a PR **before** the rate limiter. This reverses sub-task 6's backend-only approach; new sub-task 6b. Order now: 6b → 7 → 4 → 8 → 9. |
| Scope + order (2026-10-05) | User: rate limiter, Duane work split and ECS research are **on hold**; finish this ticket first. Order now: **7 → 6b → 4 → 8 → 9** (7 first because it is small and independent of 6b). Frontend 5–6 committed as `c3b9c06` and pushed to `origin/feature/events`. It moves into `feature/event-page` (PR or fast-forward) only **after 6b**, so that branch never gets a backend-only, possibly empty page. |
| Timezone (assumed) | `America/Los_Angeles`, shown with the zone abbreviation. This is open question 8. |

### Changes from the 09-25 proposal

The earlier proposal is in [[OneOnOne_2026-09-25_Backend_Infra]]. This plan changes it in five places:

- `status` is **not** renamed. The chip gets `tag` (formerly `registration_status`).
- The chip keeps **all 6** values.
- `event_type` is removed.
- `host` and `audience` are required.
- The CTA link field is named `url`, not `registration_url`.

---

## API contract (event shape)

```
id, title, slug, description, status, category, tag,
start_datetime, end_datetime,
format, location, online_url,
host, audience, url,
created_at, updated_at
```

| Field | Required? | Values / rule | On the card |
|---|---|---|---|
| category | yes | `event` · `program` · `workshop` · `signature_event` | top-left label + color |
| tag | optional (`""`) | `open_registration` · `accepting_applications` · `limited_seats` · `flagship` · `on_campus` · `family_friendly` | top-right chip |
| start / end_datetime | start yes, end optional | end ≥ start | Date: |
| format | yes | `in_person` · `online` · `hybrid` | Where: |
| location | when format is in_person or hybrid | text | Where: |
| online_url | when format is online or hybrid | URL | "Online" only; the link isn't shown |
| host | yes | text ≤200 | Host: |
| audience | yes | `students` · `community` · `educators` · `families` | For: + filter |
| url | optional (`""`) | URL | Register link, or disabled "Coming Soon" if empty |

Existing conventions still apply:
- the `{code, message, details}` error envelope;
- `status` is read-only and always `draft` on create;
- the slug is generated automatically;
- soft delete, with the `deleted_at__isnull=True` filter.

### Frontend mapping (`toEventItem`)

| From the API | To the card |
|---|---|
| `category` | Label ("Signature Event"), plus the accent color: `event`→cyan, `program`→wisteria, `workshop`→sky, `signature_event`→orange |
| `tag`, `audience` | Labels |
| `start_datetime` / `end_datetime` | `date` + `time` strings via `Intl.DateTimeFormat` |
| `format` | `location` text: `in_person`→the location, `online`→"Online", `hybrid`→"{location} + Online" |
| `host` | `organization` |
| `url` | `cta: { label: "Register", href, external: true }`, or `{ label: "Coming Soon" }` when empty |

---

## Questions for Leo

The full list of 15 questions is in the plan review. The ones most likely to change the code:

1. Should the chip keep all six values, or drop Flagship, On Campus and Family Friendly? Those overlap with category, location and audience.
2. Should "Accepting Applications" change the button to "Apply"?
3. One audience or several per event? Is the audience list right?
4. Is `host` required?
5. Should `event_type` be dropped or kept as optional free text?
6. Should the format rules be enforced now?
7. Is it OK for the Zoom link to be public in the API?
8. Which timezone: Central, Pacific or the visitor's local time?
9. Is the date format OK ("Oct 14, 2026 · 6:00 PM", or "Oct 14 – 16, 2026" for multi-day)?
10. Sort soonest first? Hide past events?
11. Is a grayed-out, disabled "Coming Soon" button what you pictured?
12. Show drafts in the proof of concept, or published events only?
13. When do the public route and auth land: this ticket or the next?
14. Should `feature/event-page` merge into `staging` first, or should this branch stack on it?
15. Should the featured banner and the ATEC section come from the API later?

**Answers:** *(record them here with the date, then update Decisions)*

---

## Follow-ups (out of scope)

- A public read-only `GET /api/v1/events` (published only), plus bearer-token auth on the staff routes.
- Sort by `start_datetime` and hide past events.
- Pagination.
- A seed management command or fixture.
- Per-event detail pages that use `slug`.
- A test environment on AWS.
- Merge `feature/event-page` into `staging` before this frontend PR.

## Notes / Deviations

*(Anything that differed from the plan during implementation.)*

- 2026-09-28 (sub-task 0): the frontend's unstaged `.gitignore` change (`/docs`) and untracked `AMPLIFIER_CONTEXT.md` were carried onto `feature/events` and are deliberately **not** to be committed (user decision). The frontend `feature/events` tracks `origin/feature/event-page`, so push with `git push -u origin feature/events`.
- 2026-09-28 (sub-task 1): tests go beyond the plan's list in three places: required `category`/`format`/`host`/`audience` (test 26), every choice value accepted (test 28), and malformed `online_url` → 400 (test 30). The old `general` category is now rejected. Local env set up on Windows: `.venv`, gitignored `.env` (generated local secrets), compose db on a fresh volume.
- 2026-09-28 (sub-task 1b, unplanned): split the events tests by feature (see Decisions → Test layout). Pure move, no test logic changed: per-test results identical before and after (50 failed / 10 passed). Updated the `EVENT_KEYS` location in the local-only CLAUDE.md and the contract-tests / task-brief skills.
- 2026-09-29 (sub-task 2): migration `0003_event_card_fields.py` was hand-written (`makemigrations` prompts interactively for one-off defaults); `makemigrations --check --dry-run` confirms it matches the model. One-off backfill defaults for pre-existing rows only (`preserve_default=False`): `format="in_person"`, `host="TBD"`, `audience="community"`. Local DB had no rows. `location`/`online_url` are `blank=True, default=""` at the model level; the per-format requirement is sub-task 3's `validate()` rule. Postman still sends `event_type` until sub-task 4.
- 2026-09-30 (sub-task 5): `.env.example` was created, but the frontend `.gitignore` rule `.env*` ignores it, so it won't be committed unless `!.env.example` is added. `getEvents()` also treats a non-array body as a failure (logs and returns `[]`), and calls `connection()` before its `try` so Next doesn't prerender the page with `[]` at build time.
- 2026-09-30 (sub-task 6): sub-tasks 5 and 6 were confirmed by the user without a browser check of the empty state or seeded cards; that check moves to sub-task 8. Frontend changes are still uncommitted. Wrote a standup demo guide: [[Events_API_Demo]].
- 2026-10-05 (sub-task 6b): the stand-up never said which events the static file should hold, so it ships with `[Approved …]` placeholders while Leo picks between real events, placeholders or empty. Only `data/events.json` changes with his answer. An unset `VIP_API_BASE_URL` no longer logs an error (static-only is the normal pre-AWS mode). Sub-task 8's "stop Django → empty message" check now expects the static events. The frontend `.env.local` sets `VIP_API_BASE_URL`; to test static-only, run with `NODE_ENV=test` (Next then skips `.env.local`).

## Session Log

| Date | Sub-task | What happened |
|---|---|---|
| 2026-09-28 | — | Plan approved and split into sub-tasks 0–9. No code written yet. |
| 2026-09-28 | 0 | Created `feature/events` in both repos (backend off `main`, frontend off `origin/feature/event-page`). Nothing lost; frontend `.gitignore` + `AMPLIFIER_CONTEXT.md` left uncommitted per user. |
| 2026-09-28 | 1 | Contract tests written in `tests/test_events_api.py` (tests 21–30, new `EVENT_KEYS`/`make_payload`). pytest: 50 failed / 10 passed, all failures from the missing fields (`event_type` required, `workshop` not a valid category). ruff clean. Plan change: `fields` list moved into sub-task 2. |
| 2026-09-28 | 1b | Split tests by feature: `tests/events_helpers.py` (shared), `test_events_api.py` (CRUD, 17 failed / 4 passed), `test_events_fields.py` (card fields, 33 failed / 1 passed). Totals unchanged, ruff clean. |
| 2026-09-29 | 2 | Model + migration `0003_event_card_fields` + serializer `fields` list. `migrate` OK, `makemigrations --check` clean, pytest 5 failed / 55 passed (only tests 22, 23, 24×2, 25), ruff clean. Reviewed by user. |
| 2026-09-29 | 3 | Per-format rules in `EventSerializer.validate()` (`REQUIRED_BY_FORMAT`, merged state; all cross-field errors returned together). pytest 60 passed, ruff check + format clean, `makemigrations --check` clean. Sub-tasks 1–3 handed to user to commit and push as two commits (tests, then model/migration/serializer). Plan change: frontend 5 → 6 before 4 (see Decisions). |
| 2026-09-30 | 5 | `lib/api/events.ts`: `ApiEvent` type, `getEvents()` (server-side, `no-store`, logs and returns `[]` on any failure), `toEventItem()` mapper. `.env.example` with `VIP_API_BASE_URL`. `npx tsc --noEmit` clean; `npm run lint` 0 errors (18 pre-existing warnings, none in changed files). Confirmed by user. |
| 2026-09-30 | 6 | `app/events/page.tsx` is async and passes `getEvents()` mapped through `toEventItem()` to `EventCardGrid`. `PLACEHOLDER_EVENTS` removed; `eventsGridContent.events = []`. Confirmed by user; browser check deferred to sub-task 8. Next: sub-task 4, then 7. |
| 2026-10-01 | — | Presented at stand-up ([[Standup_2026-10-01]]). Leo asked for static + API compatibility first, then a POST rate limiter, tests, and ECS Express research. Added sub-task 6b; next-steps proposal in [[Next_Steps_2026-10-01]]. No code changed. |
| 2026-10-05 | 5–6 | Status review before the 1:1 (questions in backend `docs/standup_Oct_5.md`). User committed and pushed frontend 5–6 as `c3b9c06` → `origin/feature/events` (`feature/event-page` unchanged at `ff75c5c`). tsc clean; lint 0 errors / 18 pre-existing warnings. |
| 2026-10-05 | 7 | `EventCard.tsx` `CardAction`: no `href` → `<Button type="button" disabled>` with `disabled:pointer-events-auto disabled:cursor-not-allowed` and no hover tint. tsc clean, lint 0 errors; backend gate 60 passed, ruff clean, no missing migrations. **Browser-verified by user**: a POSTed "AR Design Sprint" (no `url`) showed a disabled Coming Soon button, while Register still opens a new tab. Curl added to [[Events_API_Demo]]. Next: 6b. |
| 2026-10-05 | 6b (code) | `data/events.json` (6 `[Approved …]` placeholders in the `ApiEvent` shape) + `getEvents()` in `lib/api/events.ts`: unset → static only; backend error → static + `console.error`; OK → merged, deduped by slug (API wins), sorted by `start_datetime`. tsc clean, lint 0 errors / 18 pre-existing warnings; all three modes and the slug dedupe verified. User committing the code now. **Box left unticked**: the static content is waiting on Leo (asked on Discord; write-up + screenshots in backend `docs/static_api_compatability/`). Next: 4. |
