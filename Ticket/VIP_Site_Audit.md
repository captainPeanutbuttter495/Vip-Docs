---
title: "VIP / EWU Site Audit Fixes"
date_started: 2026-09-01
date_finished:
repo: vip-web-frontend
branch: feature/site-audit
pr: "unconfirmed"
status: needs-confirmation
standups: []
skills: [Design QA, Accessibility, Tailwind CSS, Stakeholder Communication]
tags: [ticket, frontend]
---

# VIP / EWU Site Audit Fixes

Worked through the open items from the August VIP Site Audit after the designer's review. Some were fixed, and the rest were triaged into blocked-on-asset, blocked-on-decision, and waiting-on-supervisor.

> **Needs confirmation:** the source is `vip-web-frontend/docs/vip-audit-status.md`. I couldn't find the commits for these changes under my author name in git history. Confirm the PR or branch and update the frontmatter.

---

## What I Did

- **VIP-7:** changed the card hover from Sky 400 to **Sky 100** (`#E0EBF8`) for better contrast in dark mode.
- **VIP-5:** kept the group colour dots and moved them to the bottom-right. The sr-only group label stays first for screen readers.
- **VIP-3:** switched the hero to `split-artwork` so the body copy sits under the title. This was a data-only change.
- Adjusted the testimonial quote size and fixed two desktop spacing issues.
- Captured before/after screenshots in light and dark themes for the designer (`docs/audit/`).
- Recorded the remaining items and known web↔Figma mismatches for later.

## Resume Bullets

- **Resolved** design-audit findings in a production Next.js site and triaged the rest into asset, decision, and approval blockers with screenshot evidence for designers.

---

## Related Notes

- [[VIP_Landing_Page]]
- [[Accessibility]]
