---
title: "Reusable \"Ready to Submit?\" CTA"
date_started: 2026-09-18
date_finished:
repo: vip-web-frontend
branch: feature/reusable-CTA
pr: "open (→ staging)"
status: in-review
standups: ["[[Standup_2026-09-18]]", "[[Questions_2026-09-21]]"]
skills: [React Server Components, TypeScript, Figma-to-Code, Accessibility, Design Tokens]
tags: [ticket, frontend]
---

# Reusable "Ready to Submit?" CTA

A new prop-driven `CtaCards` section, placed on three Culture Is Currency routes, with one shared content object.

---

## What I Did

- Built `components/modular_sections/CtaCards.tsx`, a pure Server Component with an eyebrow, a heading, and outlined action cards. It supports an optional `surface: "default" | "alt"`.
- Action rendering follows the repo's conventions:
  - `href` → `next/link`
  - `external` → a new tab with an icon and sr-only text
  - no `href` → a non-navigating `Button`, never a dead link
- Mapped Figma colours to existing tokens **by hex**. Designed a light mode, since Figma only had dark.
- Checked contrast:
  - `#BD3547` on parchment ≈ 5.4:1
  - `#F1A7B1` on onyx ≈ 10:1
- Added one `submissionCtaContent` object plus a `SUBMIT_HREF` constant (the Zealous portal) shared by three routes.
- Took before/after screenshots at desktop and mobile in both themes.

## Open

- The K–12 Submit destination
- Approved descriptions
- Card order
- Hiding Submit while the challenge is closed

## Resume Bullets

- **Built** a reusable, accessible call-to-action section as a React Server Component, mapping Figma to existing design tokens with verified WCAG AA contrast, and reused it across three pages from one content object.

---

## Related Notes

- [[Standup_2026-09-18]]
- [[Questions_2026-09-21]]
- [[Modular_Sections]]
- [[Accessibility]]
