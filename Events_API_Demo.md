---
title: "Events API Integration — Standup Demo"
date: 2026-09-30
repo: both
branch: "feature/events (both repos)"
tags: [demo, standup, events, frontend, backend]
related: ["[[Events_API_Integration_Plan]]", "[[Events_API_Integration]]"]
---

# Events API Integration: Standup Demo

## Summary

The Events page no longer uses hard-coded placeholder cards. Each event card now comes from the Django backend.

**Data flow:**

```
Postman / curl ──POST──▶ Django (DRF) ──▶ PostgreSQL
                                            │
Browser ──▶ Next.js server ──GET /api/v1/staff/events──┘
              │  toEventItem() maps API fields → card props
              ▼
          /events page → EventCardGrid → EventCard
```

**What's done:**

- **Backend** (`vip-web-backend`, sub-tasks 1–3; 60 tests pass, ruff is clean)
  - The `Event` model has the card fields: `category`, `tag`, `format`, `location`, `online_url`, `host`, `audience` and `url`. Migration `0003_event_card_fields` adds them.
  - The serializer enforces the per-format rules:
    - `in_person` needs `location`;
    - `online` needs `online_url`;
    - `hybrid` needs both.
  - These rules check the merged state, so a partial PATCH is validated too. Errors use the standard `{code, message, details}` envelope.
- **Frontend** (`vip-web-frontend`, sub-tasks 5–6; tsc and lint pass)
  - `lib/api/events.ts` has the following:
    - the `ApiEvent` type, which mirrors the serializer;
    - `getEvents()`, which fetches on the server with `no-store`;
    - `toEventItem()`, which maps the API data to the card.
  - `app/events/page.tsx` is an async server component that renders the API data.
  - The fetch runs on the **Next.js server**. The backend URL (`VIP_API_BASE_URL`) never reaches the browser, and no CORS is needed.
  - If the backend is down or errors, the page shows the grid's empty message instead of crashing.

**Field → card mapping:**

| API field | On the card |
|---|---|
| `category` | Top-left label and accent color (event = cyan, program = wisteria, workshop = sky, signature_event = orange) |
| `tag` | Top-right chip (hidden if empty) |
| `start_datetime` / `end_datetime` | "Date" and "Time", in Pacific time with the zone abbreviation |
| `format` + `location` | "Where": the location, "Online", or "{location} + Online" |
| `host` | "Host" |
| `audience` | "For", and drives the audience filter |
| `url` | **Register** button (opens in a new tab), or **Coming Soon** if empty |

---

## Before the meeting (about 5 minutes)

Start everything ahead of time so the demo doesn't wait on Docker.

### 1. Backend (Git Bash, from `C:\Users\mbg91918\vip-web-backend`)

```bash
git checkout feature/events
docker compose up -d db              # Postgres on host port 5433
source .venv/Scripts/activate
python manage.py migrate
python manage.py runserver           # http://localhost:8000
```

### 2. Seed events (second terminal)

The Postman collection still sends the old `event_type` field (that's sub-task 4), so seed with the curls below. Each one is self-contained: copy and paste it into Git Bash on its own. Together they create six events that cover all 4 categories, all 3 formats, all 4 audiences, and events with and without a `url`.

**1. XR Storytelling Workshop**: workshop · in_person · students · has `url`

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"XR Storytelling Workshop","description":"Hands-on intro to building immersive stories.","category":"workshop","tag":"limited_seats","start_datetime":"2026-10-14T18:00:00-07:00","end_datetime":"2026-10-14T20:00:00-07:00","format":"in_person","location":"CSUN Oviatt Library","host":"Global HSI Equity Innovation Hub","audience":"students","url":"https://example.com/register/xr"}'
```

**2. Equity in STEM Summit**: signature_event · hybrid · community · has `url`

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"Equity in STEM Summit","description":"Our flagship gathering of HSI partners.","category":"signature_event","tag":"flagship","start_datetime":"2026-11-05T09:00:00-08:00","end_datetime":"2026-11-07T17:00:00-08:00","format":"hybrid","location":"UT Dallas","online_url":"https://example.com/zoom/summit","host":"Global HSI Equity Innovation Hub","audience":"community","url":"https://example.com/register/summit"}'
```

**3. Educator Fellowship**: program · online · educators · no `url`

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"Educator Fellowship","description":"A semester-long program for faculty.","category":"program","tag":"accepting_applications","start_datetime":"2026-12-01T10:00:00-08:00","format":"online","online_url":"https://example.com/zoom/fellowship","host":"UT Dallas ATEC","audience":"educators"}'
```

**4. Family Science Night**: event · in_person · families · no `url`

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"Family Science Night","description":"Demos and activities for all ages.","category":"event","tag":"family_friendly","start_datetime":"2026-10-24T17:00:00-07:00","end_datetime":"2026-10-24T19:30:00-07:00","format":"in_person","location":"CSUN University Student Union","host":"CSUN","audience":"families"}'
```

**5. ATEC Summer Lab**: program · in_person · students · has `url`

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"ATEC Summer Lab","description":"On-campus creative technology program.","category":"program","tag":"on_campus","start_datetime":"2027-06-15T09:00:00-07:00","end_datetime":"2027-07-15T16:00:00-07:00","format":"in_person","location":"UT Dallas — ATEC","host":"UT Dallas ATEC","audience":"students","url":"https://example.com/register/atec"}'
```

**6. Open Office Hours**: event · online · community · has `url`, no tag

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"Open Office Hours","description":"Drop in with questions about the Hub.","category":"event","start_datetime":"2026-10-09T12:00:00-07:00","format":"online","online_url":"https://example.com/zoom/office-hours","host":"Global HSI Equity Innovation Hub","audience":"community","url":"https://example.com/register/office-hours"}'
```

Each command should print `201`. Run each one **once**: running it again creates a duplicate (with a `-2` slug). To start clean, run `docker compose down -v`, then `migrate` again.

### 3. Frontend (third terminal, from `C:\Users\mbg91918\vip-web-frontend`)

```bash
git checkout feature/events
# .env.local must contain: VIP_API_BASE_URL=http://localhost:8000
npm run dev                          # http://localhost:3000
```

### 4. Tabs to have open

- `http://localhost:3000/events`: the page itself
- `http://localhost:8000/api/v1/staff/events`: the raw JSON (DRF browsable API)
- `http://localhost:8000/api/docs/`: Swagger, which shows the new fields
- Postman, or a terminal for the live POST

---

## Demo script (about 5 minutes)

1. **The problem (30 s).** "The Events page used placeholder cards hard-coded in `data/eventsData.tsx`. Now they come from the Django API."
2. **The API (1 min).** Show the raw JSON tab: one event's shape, `category`, `tag`, `format`, `host`, `audience`, `url`. Then flip to Swagger to show the documented contract.
3. **The page (1 min).** Show `/events`. Point out what came from the API on one card:
   - the category label and color;
   - the tag chip;
   - the date and time;
   - "Online" and "{location} + Online" for the online and hybrid events;
   - the host and audience.
4. **Filters (30 s).** Click a category chip, then change the audience filter.
5. **CTAs (30 s).** Click **Register** on an event with a `url`; it opens a new tab. Point out the **Coming Soon** button on the Fellowship and Family Science Night cards, which have no `url`.
6. **Live add (1 min).** POST a new event (any of the seed curls above with a new title), then refresh `/events`. It appears right away (`cache: "no-store"`). The newest event is listed first.
7. **PATCH (1 min).** Update the two existing events, then refresh `/events` after each one. See [[#Live API commands]] below for the commands.
   - **Intro to AR Workshop:** move it to a new room and clear its `url`. The card's "Where" changes and **Register** becomes **Coming Soon**.
   - **Equity in STEM Summit:** switch `format` from `hybrid` to `online`. "Where" changes from "UT Dallas + Online" to "Online". This passes validation because the stored `online_url` is merged in.
8. **Soft delete (1 min).** DELETE the Summit. It returns `204`, a GET by id now returns a `404 not_found` envelope, and the card disappears. The database query still shows the row, with `deleted_at` set.
9. **Hard delete (30 s).** DELETE the AR Workshop with `?permanent=true`. It returns `204`, and this time the database query shows the row is gone.
10. **Validation (30 s).** POST an `in_person` event without `location`. It returns `400` with `{"code":"validation_error", ..., "details":{"location":[...]}}`.
11. **Resilience (30 s).** Stop `runserver` (Ctrl+C) and refresh `/events`. The page still renders and shows "No upcoming events right now — check back soon." instead of an error. Restart the server afterwards.

---

## Live API commands

Every block below is self-contained, so you can paste it into Git Bash on its own. Run them in this order: the deletes remove the events that the PATCHes change.

The IDs are the ones in the local database as of 2026-10-01:

| Event | id |
|---|---|
| Equity in STEM Summit | `ff004841-9b87-473a-afd5-4e730b49e0c1` |
| Intro to AR Workshop | `94730b55-31a2-44c8-9bf4-451da8fa1d1f` |

If you reset the database (`docker compose down -v`), the IDs change. List the current ones with:

```bash
curl -s http://localhost:8000/api/v1/staff/events | python -c "import sys,json; [print(e['id'], e['title']) for e in json.load(sys.stdin)]"
```

### PATCH: move the AR Workshop and clear its registration link

Expect `HTTP 200`, the new `location`, and `"url":""` (the card shows **Coming Soon**).

```bash
curl -s -w "\nHTTP %{http_code}\n" -X PATCH http://localhost:8000/api/v1/staff/events/94730b55-31a2-44c8-9bf4-451da8fa1d1f \
  -H "Content-Type: application/json" \
  -d '{"location":"Oviatt Library, Room 210","url":""}'
```

### PATCH: make the Summit online-only

Expect `HTTP 200` and `"format":"online"`. Only `format` is sent; the stored `online_url` satisfies the online rule.

```bash
curl -s -w "\nHTTP %{http_code}\n" -X PATCH http://localhost:8000/api/v1/staff/events/ff004841-9b87-473a-afd5-4e730b49e0c1 \
  -H "Content-Type: application/json" \
  -d '{"format":"online"}'
```

### Soft delete: the Summit

Expect `HTTP 204` with an empty body.

```bash
curl -s -w "HTTP %{http_code}\n" -X DELETE http://localhost:8000/api/v1/staff/events/ff004841-9b87-473a-afd5-4e730b49e0c1
```

The API now treats it as gone. Expect `HTTP 404` with `{"code":"not_found","message":...,"details":{}}`:

```bash
curl -s -w "\nHTTP %{http_code}\n" http://localhost:8000/api/v1/staff/events/ff004841-9b87-473a-afd5-4e730b49e0c1
```

The row is still in Postgres, with `deleted_at` set:

```bash
docker compose exec db sh -c 'psql -U "$POSTGRES_USER" -d "$POSTGRES_DB" -c "SELECT title, deleted_at FROM events_event;"'
```

### Hard delete: the AR Workshop

Expect `HTTP 204`.

```bash
curl -s -w "HTTP %{http_code}\n" -X DELETE "http://localhost:8000/api/v1/staff/events/94730b55-31a2-44c8-9bf4-451da8fa1d1f?permanent=true"
```

Run the same query again. This time "Intro to AR Workshop" is gone from the table, while the soft-deleted Summit row is still there:

```bash
docker compose exec db sh -c 'psql -U "$POSTGRES_USER" -d "$POSTGRES_DB" -c "SELECT title, deleted_at FROM events_event;"'
```

Run the `docker compose exec` commands from `C:\Users\mbg91918\vip-web-backend` so Compose can find the `db` service.

### Coming Soon CTA: event with no registration link

Added 2026-10-05 for sub-task 7. Expect `HTTP 201` and `"url":""` in the response. Refresh `/events`: the **AR Design Sprint** card shows a grayed-out **Coming Soon** button that can't be clicked, can't be reached with Tab, and shows the not-allowed cursor on hover. Compare it with a card that has a `url`, where **Register** still opens a new tab.

```bash
curl -s -w "\nHTTP %{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"AR Design Sprint","description":"A two-day sprint prototyping AR experiences.","category":"workshop","tag":"limited_seats","start_datetime":"2026-11-12T10:00:00-08:00","end_datetime":"2026-11-13T16:00:00-08:00","format":"in_person","location":"CSUN Oviatt Library","host":"Global HSI Equity Innovation Hub","audience":"students"}'
```

### Validation error: in-person event with no location

Expect `HTTP 400` with `"code":"validation_error"` and a `location` entry in `details`. Nothing is created.

```bash
curl -s -w "\nHTTP %{http_code}\n" -X POST http://localhost:8000/api/v1/staff/events \
  -H "Content-Type: application/json" \
  -d '{"title":"Campus VR Open House","description":"Try the headsets in person.","category":"event","start_datetime":"2026-10-20T15:00:00-07:00","end_datetime":"2026-10-20T17:00:00-07:00","format":"in_person","host":"CSUN","audience":"students"}'
```

---

