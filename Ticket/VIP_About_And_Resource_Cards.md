---
title: "VIP About Illustrations + Resource Card Resize"
date_started: 2026-08-19
date_finished: 2026-08-19
repo: vip-web-frontend
branch: feature/ui-updates
pr: "#52 (→ staging), shipped to main in #54"
status: merged
standups: ["[[Standup_2026-08-19]]"]
skills: [React, Tailwind CSS, Responsive Design, Accessibility]
tags: [ticket, frontend]
---

# VIP About Illustrations + Resource Card Resize

Two UI tasks from the Aug 19 standup: real illustrations and responsive layout for the VIP About section, and resource-library cards that fit within the viewport.

---

## What I Did

**VIP About (`/vip`)**
- Replaced the placeholders with the five v1 layer illustrations, rendered `object-contain` on a transparent media area. Added optional `image`/`imageAlt` to the card type (backward-compatible).
- Replaced repeated red group labels with **colour-coded tab dots** driven by a new optional `accent` per group. Kept the group name as sr-only text.
- Made the 3-across and 2-across rows responsive from **≥1024px** using fluid `flex-grow`. They were previously 1440px only.

**Resource library cards (shared by get-started and resources)**
- Changed the cover from `aspect-square` to `aspect-[16/10]` and tightened padding, gaps, and type, so the header and card row fit in the viewport.

## Testing

- `tsc --noEmit` and lint clean.
- Checked manually at mobile, 834px, 1024px, and 1440px, in light and dark themes.

## Resume Bullets

- **Improved** responsive layouts so card rows work from 1024px instead of only 1440px, with backward-compatible prop extensions and screen-reader-preserving group labels.

---

## Related Notes

- [[Standup_2026-08-19]]
- [[VIP_Landing_Page]]
- [[Accessibility]]
