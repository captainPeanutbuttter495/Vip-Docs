---
date: 2026-09-21
type: supervisor-questions
repo: vip-web-frontend
ticket: "Events page"
source: "vip-web-frontend/docs/events-supervisor-questions.md"
tags: [standup]
---

Ticket: Events page — questions before the next slice
Branch: `feature/event-page` (pushed). Page is live at `/events` by URL only.

Context for each answer: what it changes in code, so we can move fast once decided.

## 1. Navigation — where does "Events" live?

1. **Placement** — top-level nav link, or a sub-item under an existing dropdown ("Engage With Us"? "About"?)?

   *Changes:* one entry in `NavData.ts`; header, mobile menu and footer all follow.
2. **Label** — "Events", "Events & Workshops", or "Signature Programs & Events" (the wireframe's H1)? Should the nav label and the page H1 match?

3. **Footer** — the footer builds from the same nav data, so it appears there automatically. OK, or should it be nav-only?

4. **Timing** — add the link now (page shows placeholder content to visitors of staging), or keep it unlinked until real events exist?

## 2. Hero buttons — "Innovation Hub Events" / "Partner Events"

5. **Emphasis** — the ticket says two *primary* buttons. Both filled red, or filled + outline (what's on the page now)? Is there a design reason to rank one above the other?
   *Changes:* a small optional prop on `Hero`; every other page's hero is unaffected.

6. **Destination** — what does each button do? Pick one:
   - (a) scroll to two sections on this page — Hub events and Partner events listed separately
   - (b) go to two separate pages (`/events/innovation-hub`, `/events/partners`)
   - (c) filter the one grid (Hub only / Partners only)
   - (d) placeholder for now, decide later
   *Changes:* (a) splits the grid into two `EventCardGrid`s and adds a `source: "hub" | "partner"` field per event; (b) needs two more routes and content; (c) needs the filter UI we deliberately skipped (client component).

6. **What counts as a "Partner Event"?** Any event hosted by a partner institution (CSUF, UT Dallas, etc.) vs. one hosted by the Hub itself? Or something else (e.g. industry partners only)? This decides which field the split keys on.

7. **Wireframe conflict** — the Figma hero has one "Browse All" button. Confirm the two ticket buttons replace it (not sit alongside it).

## 3. Confirm what stays on the page (from the wireframe, not the ticket)

9. **Featured Signature Event** section — keep? Is there a real signature event to feature at launch, or should it be hidden until one exists?

10. **Programming at ATEC** section — is this a real program to feature, and does "View ATEC Programs" go somewhere (internal page vs. UT Dallas site)?

11. **Filters + "All Audiences" dropdown** — now built (opt-in via `showFilters`). Three follow-ups:
    - Chip labels are the card's category text, singular ("Workshop"); the wireframe shows plurals ("Workshops"). Keep singular, or add a label map for plurals?
    - The wireframe's "Programming at ATEC" chip is a program, not a category, so it was not reproduced. Should ATEC events be a category of their own, or is the ATEC section (Q10) enough?
    - Chips use the site's existing filter style (red `cta` active state, from the resource library) rather than the wireframe's grey chips, for consistency. OK?

## 4. Content and assets

12. Are there **any approved events** (even one or two) to replace the six bracketed placeholders?

13. **Photos** — hero, featured event, ATEC all show the grey placeholder on purpose. Who supplies the images, and do they exist yet?

14. **Register links** — should they open in a new tab (external registration sites), and is "Register" always the label or per-event ("RSVP", "Learn more")?

15. **Eyebrow copy** — the wireframe literally reads "Tagline" above "Browse Programs & Events". I used "Events & Workshops"; is there approved copy?

16. **Filter labels** — "Filter by audience" is shown next to the dropdown as its visible label (the wireframe has none, but a select needs one for accessibility). Keep it visible, or hide it for screen readers only?

## 5. Process

17. **Audit workbooks** (CLAUDE.md §14.1) — the new page needs an `Events` sheet in both `Image & Alt Text Audit.xlsx` and `Downloads & External Links Audit.xlsx` before the PR can merge. All 3 images are placeholders and all 10 links are pending — fine to log them as "Still Needed" now and update as content lands, or wait until content is final?
18. **Draft PR now?** Everything code-side is done except what depends on the answers above. OK to open a draft PR against `staging` so component review runs in parallel with these decisions?
