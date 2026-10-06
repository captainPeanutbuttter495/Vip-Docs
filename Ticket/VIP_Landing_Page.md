---
title: "VIP Landing Page"
date_started: 2026-07-30
date_finished: 2026-08-03
repo: vip-web-frontend
branch: feature/vip-page
pr: "#31 (→ staging), shipped to main in #41"
status: merged
standups: []
skills: [Next.js, React, TypeScript, Tailwind CSS, Figma-to-Code, Design Systems]
tags: [ticket, frontend]
---

# VIP Landing Page

Built the `/vip` (Virtual Immersive Platform) page from Figma: the hero, Culture Is Currency, and the VIP About card groups, using reusable, data-driven sections.

---

## What I Did

- Added the VIP hero (`d311572`) and extended `Hero` with a heading, accent tone, pull quote, and shape artwork (`ed790ad`).
- Built the Culture Is Currency section from existing UI components (`3fab129`, `eaa2773`, `82f8c70`).
- Added fixed-light and framed split variants (`46359ce`).
- Fixed the page palette to match Figma (`aadc66a`):
  - parchment/onyx page surface and a carbon accordion band
  - pinned focus and outline styles for interactive controls
- Created the reusable **`LabeledCardGroups`** section and exported `VIPCard` for reuse (`da9a67d`).
- Replaced the VIP About subsections to match Figma, with stacked and split card variants and group labels moved into each card (`c13ab79`).
- Wired all page content through `data/vipData.tsx` (`51a08e3`).

## Resume Bullets

- **Translated** Figma designs into a responsive, theme-aware Next.js/TypeScript page, extending shared components rather than one-off markup.
- **Created** a reusable card-group section component adopted in later pages, keeping content in typed data files ready for a future CMS.

---

## Related Notes

- [[Modular_Sections]]
- [[Content_Data_Contract]]
- [[VIP_About_And_Resource_Cards]]
- [[VIP_Site_Audit]]
