---
title: "National Challenge & Site UI Updates"
date_started: 2026-08-17
date_finished: 2026-08-19
repo: vip-web-frontend
branch: feature/national-challenge-up-updates
pr: "#49 (→ staging), shipped to main in #54"
status: merged
standups: []
skills: [React, Next.js, Tailwind CSS, UI Refactoring]
tags: [ticket, frontend]
---

# National Challenge & Site UI Updates

A batch of UI changes across the challenge, team, gallery, and home pages, including a new reusable FAQ section.

---

## What I Did

- Removed the Deliverables section from the national challenge page (`0353c2f`).
- Built a reusable **`Faq`** section and added it to the challenge page (`2f46639`).
- Simplified `ImageGallery` to a single featured image by removing the thumbnail strip (`9cb8f77`, −86 lines).
- Added a hover lift to the team `PersonGrid` cards (`e5ab141`).
- Swapped the "By Us" icons for new illustrations and refactored `IconFeatureList` (`9c36d10`).
- Added rounded corners and rolled the updated "By Us" component out to every page (`b5e0ccd`).

## Resume Bullets

- **Built** a reusable, accessible FAQ accordion section and **simplified** existing components, removing unused code while shipping design updates across four pages.

---

## Related Notes

- [[Modular_Sections]]
- [[VIP_About_And_Resource_Cards]]
