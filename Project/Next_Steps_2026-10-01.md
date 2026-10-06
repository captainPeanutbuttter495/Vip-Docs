---
title: "Next Steps after the Oct 1 Stand-up"
date: 2026-10-01
status: proposal
repo: both
standups: ["[[Standup_2026-10-01]]"]
tags: [project, plan, proposal]
---

# Next Steps after the Oct 1 Stand-up

A proposal to discuss with Duane and Leo. It covers what the Oct 1 stand-up asked for, the full backlog of backend work, and a suggested way to split it. **Nothing here has been started.** It's a draft for review.

---

## What Leo asked for (Oct 1)

From [[Standup_2026-10-01]], in Leo's priority order:

1. **Front-end/back-end compatibility, then a PR.** The Events page must render from static JSON in the frontend *and* from the backend, so it can go live before AWS is ready. "Focus on the compatibility first… do a PR, so that we can actually have the events page already up and running."
2. **POST rate limiter.** Backend middleware by client IP, e.g. 10 POSTs per IP per 20 minutes.
3. **Automated tests** for the rate limiter.
4. **Research ECS Express vs. plain ECS:** ease of deployment, cost, pros and cons.
5. **Split the work with Duane**, target about **Wed, Oct 7**.

Pinned: auth (bearer tokens later; a name + email platform login is being considered) and the site redesign.

> [!warning] Conflict with the current plan
> Sub-task 6 of [[Events_API_Integration_Plan]] deleted the static `PLACEHOLDER_EVENTS`, so the page is backend-only. On Amplify there's no backend yet, so the grid would be empty. Item 1 replaces that approach (sub-task **6b** below).

---

## Part 1 — Finish the Events API Integration ticket

Owner: Matthew. Order follows Leo's priority: compatibility → PR → the rest.

| Step | What | Repo |
|---|---|---|
| **6b (new)** | **Static + API compatibility** (design below) | frontend |
| 7 | Disabled "Coming Soon" CTA in `EventCard.tsx` | frontend |
| 4 | Postman bodies (still send the old `event_type`), README field list, seed notes. Required before the backend PR | backend |
| 8 | End-to-end check in three modes: static-only, backend up, backend down | both |
| 9 | Commit prep. Backend PR → `main`; frontend PR → `staging` (check whether `feature/event-page` must merge first) | both |

### 6b design — one shape, two sources

- Keep a **static events file in the API shape** (`ApiEvent[]`) in the frontend, e.g. `data/events.json`. Both sources then go through the existing `toEventItem()` mapper in `lib/api/events.ts`. There's one contract and one mapper, so the two can't drift.
- `getEvents()` behavior:

| Situation | Result |
|---|---|
| `VIP_API_BASE_URL` not set (Amplify today) | static events only |
| Backend unreachable / error | static events, plus a server-side `console.error` |
| Backend OK | static + API events merged, **deduplicated by `slug`** (the API version wins), sorted by `start_datetime` |

- This answers Leo's duplication concern. It also means going live needs no backend, and switching to the backend later is a config change (set the env var), not a code change.

---

## Part 2 — Backlog (brainstorm)

| #   | Task                                             | Repo    | Size | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --- | ------------------------------------------------ | ------- | ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A   | **POST rate limiting**                           | backend | M    | DRF `ScopedRateThrottle` on writes. `10/20min` needs a small custom rate parser, since DRF only supports per s/m/h/d. 429 goes through the `{code, message, details}` envelope as `throttled`, with `Retry-After`. Counts must be shared across gunicorn workers and ECS tasks, so use Django's **DatabaseCache** (no new infra), not the per-process LocMem cache. Behind the ALB, the client IP comes from `X-Forwarded-For`, so set DRF `NUM_PROXIES` to avoid spoofing. |
| B   | **Rate-limit tests**                             | backend | S    | The 11th POST → 429, GET not throttled, IPs counted separately, window reset. Contract-first, like the events tests.                                                                                                                                                                                                                                                                                                                                                        |
| C   | **ECS Express vs. ECS (Fargate) decision doc**   | docs    | M    | Setup steps, what Express manages for us (ALB, scaling, certs), cost at our scale, lock-in, CI/CD fit, recommendation.                                                                                                                                                                                                                                                                                                                                                      |
| D   | **Production container**                         | backend | M    | gunicorn; `DEBUG` / `ALLOWED_HOSTS` / `CSRF_TRUSTED_ORIGINS` from env; static files (whitenoise); non-root user; ECS health check on `/healthz/`.                                                                                                                                                                                                                                                                                                                           |
| E   | **GitHub Actions → ECR push** on merge to `main` | backend | M    | ECS deploy step later, once C is decided.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| F   | **Public read-only `GET /api/v1/events`**        | both    | M    | Published events only, `deleted_at` filter, soonest first, hides past events, optional pagination. Then the frontend reads it instead of the staff route. Needed before staff routes get auth.                                                                                                                                                                                                                                                                              |
| G   | **`seed_events` management command**             | backend | S    | Loads a JSON fixture in the same shape as the frontend's static file, so the two data sources share one format.                                                                                                                                                                                                                                                                                                                                                             |
| H   | **Structured logging** (JSON logs, request id)   | backend | S–M  | CloudWatch-ready.                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| I   | **Event detail by slug**                         | both    | M    | `GET /api/v1/events/<slug>` and a frontend detail page.                                                                                                                                                                                                                                                                                                                                                                                                                     |
| J   | **Auth design doc** (pinned)                     | docs    | —    | Bearer tokens on staff routes; options for a name + email platform login. Design only, no code.                                                                                                                                                                                                                                                                                                                                                                             |
| K   | **S3 presigned downloads + download tracking**   | backend | L    | Phase 2. Listed for the roadmap, not to start.                                                                                                                                                                                                                                                                                                                                                                                                                              |

---

## Part 3 — Proposed split

Goal: Duane gets **real backend ownership**, a full feature end to end rather than only tests, and the work streams don't block each other.

| Matthew | Duane |
|---|---|
| Part 1: compatibility PR | **F** public events endpoint (backend + the frontend switch, so his frontend experience counts) |
| **A + B** rate limiter + tests | **G** seed command |
| **C** ECS decision doc → **D** production container → **E** ECR pipeline | **I** event detail by slug |
| Review Duane's PRs; onboarding walkthrough | Review Matthew's PRs |

**Onboarding (shared):** a short walkthrough of the backend:
- the repo layout ([[Backend_Code_Organization]]);
- the error envelope ([[Error_Envelope]]);
- the test helpers in `tests/events_helpers.py`;
- the contract-first workflow (tests before code);
- the Docker/Postgres setup ([[Docker_Local_Dev]]).

### Timeline to Wed, Oct 7

| Days | Matthew | Duane |
|---|---|---|
| Thu–Fri (Oct 1–2) | Compatibility PR (6b, 7, 4, 8, 9) | Local setup, onboarding walkthrough, start F |
| Mon–Tue (Oct 5–6) | Rate limiter (A + B) | F |
| Wed (Oct 7) | ECS research doc (C) → report to Leo | F PR, with Matthew's review |

---

## Questions to settle

**With Duane:**
- Does this split work? Would he rather take a different backend item (e.g. D or H)?
- How much time does he have before Oct 7, given front-end requests from Leo?

**With Leo:**
- Confirm compatibility → PR comes before the rate limiter (as said on Oct 1), and that the frontend PR can go to `staging` before `feature/event-page` merges.
- Rate-limit numbers: 10 per 10 min or 10 per 20 min? Only POST, or PATCH/DELETE too? (Delete was called "fine.")
- Should the deployed page read the static data only until the backend is hosted? (The proposal: yes, via the env var.)

---

## Related Notes

- [[Standup_2026-10-01]]: the meeting this comes from
- [[Events_API_Integration_Plan]]: sub-task checklist (6b added)
- [[Events_API_Integration]]
- [[AWS_Architecture]]: planned AWS hosting
- [[Events_API]] · [[Error_Envelope]] · [[Docker_Local_Dev]]
