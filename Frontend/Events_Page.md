# Events Page

The `/events` page, with a hero, featured Signature Event, filterable **Browse Programs & Events** grid, and Programming at ATEC spotlight. It is the first frontend page to read from the Django API (on the unmerged `feature/events` branch).

---

## Overview

- **Branch:** `feature/event-page` (draft PR, not merged). Reachable by URL only; there is no nav entry yet.
- **Content on `feature/event-page`:** `data/eventsData.tsx` has six bracketed placeholder cards mirroring the wireframe. All images are the grey `/images/shared/shared_cardPlaceholder_1.png` with `alt=""`, and no CTA has an `href`.
- **Content on `feature/events` (uncommitted, 2026-09-30):** the placeholders are gone. The page is an async Server Component that calls `getEvents()` from `lib/api/events.ts` and maps each event with `toEventItem()`. See [[#API Wiring (feature/events)]] and [[Events_API_Integration]].

---

## Page Composition — `app/events/page.tsx`

| Section | Component | Notes |
|---------|-----------|-------|
| Hero | `Hero` (`split-artwork`, `artworkFit: "cover"`) | Two buttons: "Innovation Hub Events", "Partner Events". Destinations are an open question |
| Featured Signature Event | `MediaFeature` | New optional props: `tags`, `meta` (icon + text rows), `imageAspect: "video"`, `ctaExternal` |
| Browse Programs & Events | `EventCardGrid` | `showFilters: true` |
| Programming at ATEC | `MediaFeature` | |

## `EventCardGrid/`

| File | Role |
|------|------|
| `EventCardGrid.tsx` | Section shell and header. Server Component. Re-exports the types |
| `EventList.tsx` | `"use client"`: category chips, audience `<select>`, live count, grid. The only client boundary |
| `EventCard.tsx` | One tile: accent border, category label, status chip, title, description, and a `<dl>` of When/Where/Host/For rows |
| `types.ts` | `EventItem`, `EventAction`, `EventCardGridProps`, `EventCardLabels`, `EventFilterLabels` |
| `labels.ts` | Default chrome strings |

```ts
interface EventItem {
  id: string;
  title: string;
  description: string;
  category?: string;      // label + filter chip
  status?: string;        // chip, e.g. "Open Registration"
  accent?: "cyan" | "wisteria" | "orange" | "sky" | "neutral"; // presentation only
  date: string;           // display string
  time?: string;          // display string
  location: string;
  organization?: string;  // Host:
  audience?: string;      // For: + filter
  cta?: { label: string; href?: string; external?: boolean };
}
```

Filter options come from the events' own `category` and `audience` values, in order of first appearance. A control only renders when there is more than one value.

---

## API Wiring (feature/events)

`lib/api/events.ts`:

| Export | Role |
|--------|------|
| `ApiEvent` (+ choice unions) | Mirrors the backend serializer `fields` |
| `getEvents()` | Server-only `fetch` of `${VIP_API_BASE_URL}/api/v1/staff/events` with `cache: "no-store"`. Calls `connection()` first so the page renders per request. A missing env var, non-2xx, network error or non-array body is logged and returns `[]`, so the grid shows its empty message |
| `toEventItem()` | Maps `ApiEvent` → `EventItem` (table below) |

| API field | `EventItem` |
|-----------|-------------|
| `category` | `category` label + `accent` (event→cyan, program→wisteria, workshop→sky, signature_event→orange) |
| `tag` | `status` chip label; omitted when `""` |
| `start_datetime` / `end_datetime` | `date` + `time` via `Intl.DateTimeFormat` in `America/Los_Angeles` (assumed). Multi-day ranges drop `time` |
| `format` + `location` | `location`: the location, `"Online"`, or `"{location} + Online"` |
| `host` | `organization` |
| `audience` | `audience` label |
| `url` | `cta: { label: "Register", href, external: true }`, or `{ label: "Coming Soon" }` when empty |

`VIP_API_BASE_URL` has no `NEXT_PUBLIC_` prefix, so it stays on the server and no CORS is needed. `.env.example` documents it, but the `.env*` gitignore rule currently excludes it.

Not done yet: the no-`href` CTA is still a clickable button, not a disabled one (sub-task 7 in [[Events_API_Integration_Plan]]).

## Frontend `EventItem` vs Backend `Event` (as of `main`, before v2)

The original gap analysis. The backend `feature/events` branch closes these gaps.

| Card needs | Backend has (`main`) | Gap |
|------------|---------------------|-----|
| `id`, `title`, `description` | same | OK |
| `date` + `time` (display strings) | `start_datetime` / `end_datetime` (UTC) | Needs formatting and a timezone decision |
| `location` (required) | — | **missing** |
| `organization` (Host) | — | **missing** |
| `audience` (For) | — | **missing** |
| `cta` registration link | — | **missing** |
| `status` = registration badge | `status` = draft / published / archived | **Same name, different meaning** |
| `category` = Event / Program / Workshop / Signature Event | `general` / `challenge` / `community` | **No overlap** |
| `accent` | — | Presentation. Stays in code, derived from category |
| — | `event_type`, `slug` | No frontend counterpart yet |

---

## Open Questions

From [[Questions_2026-09-21_Events_Page]] and [[OneOnOne_2026-09-25_Backend_Infra]]:

- Hero button destinations
- What counts as a "Partner Event"
- Nav placement and label
- Timezone and date format
- The "Coming soon" disabled Register button
- Whether to use the public `GET /api/v1/events` or the staff route
- Whether drafts are shown

---

## Accessibility

- Grid is a `<ul>` of `<article>`s. Detail rows are a `<dl>`.
- Chips are `<button aria-pressed>` in a `role="group"`. The audience filter is a native `<select>` with a bound `<label>`. The count is `aria-live="polite"`.
- Cards with no `cta` render no button. On `feature/events` every card has a `cta`. A `cta` with no `href` ("Coming Soon") renders a real `disabled` button: grayed out (`disabled:opacity-50`), skipped by Tab, announced as unavailable, with a `cursor-not-allowed` that overrides the base Button's `disabled:pointer-events-none` (sub-task 7, 2026-10-05).

---

## Key Files

| File | Purpose |
|------|---------|
| `app/events/page.tsx` | Route |
| `components/modular_sections/EventCardGrid/*` | Grid section |
| `components/modular_sections/MediaFeature.tsx` | Extended for featured and ATEC sections |
| `data/eventsData.tsx` | Page copy and grid config. Placeholder cards on `feature/event-page`; `events: []` on `feature/events` |
| `lib/api/events.ts` | API client and mapper (`feature/events`) |

---

## Related Notes

- [[Events_API]]
- [[Events_API_Integration]]
- [[Events_API_Demo]]
- [[Event_Page]]
- [[07-Dynamic-Diagrams]]
