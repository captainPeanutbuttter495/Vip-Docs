---
title: "Hero Artwork Cropping Fix"
date_started: 2026-08-28
date_finished: 2026-08-31
repo: vip-web-frontend
branch: feature/cropping-fix
pr: "#66 (→ staging)"
status: merged
standups: ["[[Standup_2026-08-28]]"]
skills: [CSS, Tailwind CSS, Debugging, Responsive Design]
tags: [ticket, frontend, bugfix]
---

# Hero Artwork Cropping Fix

Found the root cause of hero artwork being cut off on four challenge pages, and replaced two fragile per-breakpoint crops with one crop window that works at every viewport size.

---

## What I Did

- **Root cause:** the crop used `top-[-17.5%]` inside a column whose height tracked the viewport (`min-h-[clamp(…100svh…)]`). The vertical framing drifted with window **height** and sliced the art. Below 1440px the crop was dropped entirely, so the emblem shrank inside its empty artboard margins.
- **Fix:** one crop window that is always narrower than the 16:9 source, holding a centred, height-driven fill. Only the empty left and right margins are ever cropped.
  - Verified the geometry across the whole 560–828px `min-h` range.
- Added an optional `artworkScale` prop (default 1). Backward-compatible, and no data-file changes.
- Regression-checked the other three `Hero` layouts.
- Proposed follow-ups:
  - The real root fix is a tight-`viewBox` re-export of the 1.3 MB SVG.
  - The two-column breakpoint could be lowered.

## Resume Bullets

- **Diagnosed and fixed** a viewport-height-dependent CSS cropping bug in a shared hero component, replacing breakpoint-specific hacks with one geometry-verified layout used on four pages.

---

## Related Notes

- [[Standup_2026-08-28]]
- [[Modular_Sections]]
