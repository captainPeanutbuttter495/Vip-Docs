---
title: "Events Page"
date_started: 2026-09-18
date_finished:
repo: vip-web-frontend
branch: feature/event-page
pr: "draft"
status: in-review
standups: ["[[Questions_2026-09-21]]", "[[Questions_2026-09-21_Events_Page]]", "[[Standup_2026-09-24]]"]
skills: [Next.js App Router, React, TypeScript, Accessibility, Component Design, Figma-to-Code]
tags: [ticket, frontend]
---

# Events Page

Built the `/events` page: a hero, a featured event, a filterable event grid with a reusable `EventItem` contract, and an ATEC spotlight. It's designed so the data can later come from the Django API.

---

## What I Did

- Added the new route `app/events/page.tsx`, composed from `data/eventsData.tsx`.
- Built **`EventCardGrid/`** as a folder section:
  - a Server Component shell
  - a single `"use client"` `EventList` with category chips, an audience `<select>`, and a live count
  - `EventCard`, `types.ts`, and `labels.ts`
- Defined the **`EventItem`** contract with optional fields and presentation-only `accent`. Filters come from the data itself.
- Extended `MediaFeature` with `tags`, `meta` icon rows, a 16:9 aspect, and `ctaExternal`. All are backward-compatible across five existing consumers.
- Accessibility:
  - `<ul>` of `<article>`s with `<dl>` detail rows
  - `aria-pressed` chips
  - a labelled native select
  - `aria-live` count
  - no dead controls
- Fixed a client-boundary crash (`dcf3571`).
- Captured screenshots at desktop, tablet, and mobile in light and dark themes.

## Resume Bullets

- **Built** a filterable events listing in Next.js with a minimal client boundary, a typed data contract, and full keyboard and screen-reader support, designed so the data source could be swapped for a REST API.
- **Extended** a shared media component with four backward-compatible props without breaking its five existing consumers.

---

## Related Notes

- [[Events_Page]]
- [[Events_API_Integration]]
- [[Questions_2026-09-21_Events_Page]]
