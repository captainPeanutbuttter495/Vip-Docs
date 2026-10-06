---
date: 2026-09-21
type: supervisor-questions
repo: vip-web-frontend
ticket: "Reusable CTA + Event page review"
source: "vip-web-frontend/docs/supervisor-questions_Sept_21.md"
tags: [standup]
---

Questions for supervisor — Sept 21
Two features pushed, neither reviewed with him yet: `feature/reusable-CTA` (PR open against `staging`) and `feature/event-page` (draft PR).

Items marked **⚑ decided & pushed** are calls I made to keep moving — they need a yes or a reversal, not a design session.

---

## A. Reusable "Ready to Submit?" CTA — `feature/reusable-CTA`

Live on `/national-steam-innovation-challenge`, `/get-started`, `/innovation-pathways`.

### Links & copy
1. **K–12 & Learning "Submit" has no destination** — it's a non-navigating button right now. Where should it go? Same Zealous portal as Competition, or a different form?
2. **Competition "Submit"** ⚑ decided & pushed — points to the Zealous portal (`zealous.co/globalhsiequityinnovationhub1/creative-opportunities/`), same URL the "Submit Project" process step already uses, opens in a new tab. Correct?
3. **Card descriptions** — Figma still says `[Approved Description]`. I wrote interim copy marked `TODO`. Is approved copy coming, and from whom?
4. **Button label** — "Submit" on both (per Figma), or audience-specific ("Submit a K–12 Project")?

### Behaviour
5. **Challenge is closed** — Get Started's hero says "Challenge Status: Closed" but the Submit buttons still show. Hide, disable, or leave as-is while closed?
6. **Card order** ⚑ decided & pushed — K–12 first (ticket order). Figma shows Competition first. Which wins?

### Placement
7. **Two CTA bands back-to-back on the landing page** — "Join the Challenge" banner → "Ready to Submit?" → FAQ. Ticket said "before FAQ", so that's where it is. Keep, move the banner, or alternate the band colour (`surface: "alt"`)?
8. **Innovation Pathways** ⚑ decided & pushed — it's the last section before the footer. OK?
9. **Any other page** that should carry it (e.g. `/innovation-challenge`)?

### Design not in Figma (both ⚑ decided & pushed)
10. **Responsive** — one column below 834px, two-up from there.
11. **Light mode** — parchment band, onyx/30 borders, titles in `cta` red (light) / `cta-dark` pink (dark).

### Flag, not a question
12. Innovation Pathways still uses a legacy hero in `components/pages/` instead of the shared `Hero`. Separate ticket?

---

## B. Events page — `feature/event-page`

Live at `/events` (URL only — not in the nav). Everything on it is placeholder text and grey placeholder images by design.

### Hero — most important, changes the data shape
13. **Where do "Innovation Hub Events" and "Partner Events" go?** (a) scroll to two groups on this page, (b) two separate pages, (c) preset the grid filter, (d) decide later. Right now they do nothing. Option (a) means splitting the grid and adding a `source` field to every event.
14. **What counts as a "Partner Event"** — anything hosted by a partner institution (CSUF, UT Dallas…) vs the Hub itself? Industry partners only?
15. **Both buttons filled?** Ticket says two *primary* buttons; today the second is outline (only thing `Hero` supports without a small prop change).
16. Confirm the two buttons **replace** the wireframe's single "Browse All".

### Navigation
17. **Where in the nav** — top-level "Events", or under "Engage With Us"? Footer follows automatically.
18. **Label** — "Events", "Events & Workshops", or "Signature Programs & Events" (the wireframe H1)? Should nav label and H1 match?
19. **Link it now** (placeholder content visible on staging) or keep it hidden until real events exist?

### Sections from the wireframe that aren't in the ticket
20. **Featured Signature Event** — keep? Is there a real one for launch, or hide until there is?
21. **Programming at ATEC** — real program to feature? Where does "View ATEC Programs" go?

### Filters ⚑ decided & pushed
22. Built category chips + audience dropdown (wireframe had them; ticket didn't). Three details to confirm:
    - Chip labels are singular ("Workshop") — wireframe shows plurals. Keep or map to plurals?
    - Wireframe's "Programming at ATEC" chip isn't a category, so it's not there. Should ATEC events be their own category?
    - Chips use the site's existing red filter style (from the resource library), not the wireframe's grey.
23. "Filter by audience" is a visible label next to the dropdown (wireframe has none; a select needs one for accessibility). Keep visible or screen-reader-only?

### Card fields ⚑ decided & pushed
24. Cards show **Host:** (institution, from the ticket) **and** **For:** (audience, from the wireframe) as optional rows. OK to keep both?
25. Date and time are **display strings** typed by whoever enters content (e.g. "Oct 14, 2026 · 6:00–7:30 PM PT"), not real dates — so nothing sorts or auto-hides past events. Fine for Phase 1?

### Content & assets
26. Any **approved events** (even one) to replace the six bracketed placeholders?
27. **Photos** — hero, featured event, ATEC all show the grey placeholder on purpose. Who supplies them, and when?
28. **Register links** — open in a new tab? Always "Register", or per-event ("RSVP", "Learn more")?
29. **Eyebrow copy** — wireframe literally says "Tagline"; I used "Events & Workshops".

---

## C. Process (both PRs)
30. **Audit workbooks** (CLAUDE.md §14.1) — both PRs need rows in the Image/Alt and Downloads/Links spreadsheets. The CTA workbook edit exists but is stashed; the events page needs an `Events` sheet (3 placeholder images, 10 pending links). Log them as "Still Needed" now, or wait for final content?
31. **Events draft PR** — OK to keep it as a draft against `staging` and let component review happen while these are decided?
32. **Merge order** — CTA first, then events? They're independent branches, so either order works.

**If there's only time for five:** 1, 13, 14, 17, 20.
