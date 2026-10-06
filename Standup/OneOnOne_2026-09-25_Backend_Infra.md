---
date: 2026-09-25
type: one-on-one
repo: vip-web-frontend
ticket: "Events API integration"
source: "vip-web-frontend/docs/questions/events-api-integration_Sept_25.md"
tags: [standup]
---

Meeting Title: Backend Infra 1:1
Date: Sep 25
Ticket: Events API integration — remodel the `Event` model to match the events card design, and have the Events page GET real data from the local backend (proof of concept)

## Goal (from the 1:1)

- Remodel the Django `Event` to match what the Figma card shows ("design first, then backend").
- POST a few lorem-ipsum events by hand (Postman/Swagger). No CMS or automation needed yet.
- The local Next.js `/events` page GETs the array and builds the cards from it, replacing the 6 hard-coded placeholders. Filters keep working.
- Register button: link if the event has a URL; greyed-out **"Coming soon"** if it doesn't.
- Later, not in this ticket: bearer-token auth on POST, AWS test environment, per-event slug pages.

## What I found

- **Backend** (`vip-web-backend`, `main`): `status` is already used for the CMS publishing state (draft / published / archived) and is read-only. `event_type` is workshop / webinar / conference / other. `category` is general / challenge / community. None of these match the card design.
- Routes today: `GET/POST /api/v1/staff/events` and `GET/PATCH/DELETE /api/v1/staff/events/<uuid>`. No pagination (returns a plain JSON array), no auth, no CORS.
- **Frontend** (`feature/event-page`): `EventCardGrid` already takes `events: EventItem[]` as a prop and handles empty lists and filters, so the change is mostly fetching and mapping data, not rewriting the component.
- The fetch will run on the Next.js server, not in the browser, so Django doesn't need CORS setup for this proof of concept.

## Proposed model

| Field | Required? | Type / choices | Where it shows on the card |
|---|---|---|---|
| `id` | auto | UUID | — |
| `title` | yes | string | heading |
| `slug` | optional | auto-generated; for a future event detail page, not the register link | — |
| `description` | yes | text | body |
| `category` | yes | `event` · `program` · `workshop` · `signature_event` | top-left label + card color |
| `registration_status` | optional | `open_registration` · `accepting_applications` · `limited_seats` | top-right chip |
| `event_type` | optional | free text ("Networking", "Resume workshop"); not in the design yet | — |
| `start_datetime` | yes | datetime | Date: |
| `end_datetime` | optional | datetime | Date: (range) |
| `format` | yes | `in_person` · `online` · `hybrid` | Where: |
| `location` | optional | string | Where: |
| `online_url` | optional | URL (Zoom) | — |
| `host` | optional | string | Host: |
| `audience` | optional | `students` · `educators` · `community` · `families` | For: + filter |
| `registration_url` | optional | URL | Register button; missing → "Coming soon" |
| `publish_status`, `created_at`, `updated_at`, `deleted_at` | auto / optional | existing CMS fields, kept | — |

## Questions

### Model / naming
1. **Rename `status` → `publish_status`?** The existing `status` field already means draft / published / archived. The card chip would get its own field, `registration_status`, so "status" doesn't mean two things. OK?
2. **Drop Flagship, On Campus, and Family Friendly as chip values?** They describe other things that already have fields: Flagship → `category = signature_event`, On Campus → `location`, Family Friendly → `audience = families`. That leaves the chip with Open Registration, Accepting Applications, and Limited Seats. Or are any of those three meant to be separate badges?
3. **Keep "Open Registration" and "Accepting Applications" both?** You wondered in the meeting whether we need them. They differ in a way people will notice: "register now" versus "apply and get selected." Should the button text change with the status ("Apply" versus "Register")?
4. **Audience: one or several?** Can an event be for more than one audience (e.g. Students + Educators)? If so it becomes a list and the filter works slightly differently.
5. **Rules for `format`:** should `location` be required for in-person and hybrid events, and `online_url` required for online and hybrid? Or keep both optional for the proof of concept?
6. **Old enum values:** the current `event_type` (workshop / webinar / conference) and `category` (general / challenge / community) values get replaced. Any of them worth keeping? Local data is all lorem ipsum, so a destructive migration is fine. Confirm?

### Display
7. **Timezone for dates and times:** Central (UT Dallas / ATEC), Pacific, or the visitor's local time?
8. **Date format on the card:** e.g. "Oct 14, 2026 · 6:00 PM" and "Oct 14 – 16, 2026" for multi-day. OK?
9. **"Coming soon" button:** a disabled, greyed-out button labeled "Coming soon" that can't be clicked or focused. Match what you pictured?
10. **Card colors come from `category`** (event → cyan, program → wisteria, workshop → sky, signature event → orange), worked out on the frontend. The backend never stores a color. OK?

### API
11. **Which endpoint does the frontend read?** For the proof of concept, should the frontend read `/api/v1/staff/events` directly, or should I add a public read-only `GET /api/v1/events` now that only returns published events? Staff routes will get auth later, so the public page shouldn't depend on them long-term.
12. **Should unpublished events show up?** New events default to `draft`. Should the proof of concept show every event regardless of `publish_status`, or only published ones (which means setting them to published by hand)?
13. **Branches:** backend `feature/events-model-v2` off `main`; frontend `feature/events-api` off `feature/event-page` (still unmerged). OK?

### Follow-up (out of scope, flagging)
- Bearer-token auth on POST/PATCH/DELETE.
- AWS test environment so the deployed frontend reads a hosted backend.
- Per-event detail pages driven by `slug`.
- The featured-event banner and the "Programming at ATEC" section are still hard-coded; should they come from the API later?
- Recheck the card layout once real (long) descriptions exist.
