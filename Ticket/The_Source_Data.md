---
title: "The Source — Partner Program Data"
date_started: 2026-09-08
date_finished: 2026-09-09
repo: vip-web-frontend
branch: feature/sourceData
pr: "#98 (→ staging), shipped to main in #101"
status: merged
standups: ["[[Standup_2026-09-08]]"]
skills: [Data Extraction, Content Modeling, TypeScript, Documentation, Stakeholder Communication]
tags: [ticket, frontend, content]
---

# The Source — Partner Program Data

Replaced the lorem placeholder cards on `/the-source` with real partner-program data extracted from "One Big Idea" intake forms. Every value can be traced back to a form question.

---

## What I Did

- Extracted **6 of 9** assigned projects:
  - CSUN Firme Coding
  - CSULB Data Science
  - CSUF Biomanufacturing
  - CSUF Family Engagement
  - CSUCI First-Gen Professionals
  - CSUF Passages

  The remaining three were on hold because no source folder existed.
- Followed an **extract-only** rule. Missing fields are omitted, never guessed. Every value in `data/theSourceData.tsx` traces to a numbered question, recorded in `the-source-data.md`.
- Added **4 real downloadable PDFs**, replacing `Placeholder.pdf`.
- Kept a **blockers log (12 blockers)** and **36 flagged decisions** for the supervisor.
- Deliberately left three links unwired to avoid publishing unreviewed or private partner material.
- Flagged the 41 MB PDF as a performance concern.

## Resume Bullets

- **Extracted and modeled** partner-program content from intake forms into typed TypeScript data, with every field traceable to its source question and no guessed values.
- **Maintained** a blockers log and a decision log (12 blockers, 36 flagged questions) that let stakeholders unblock content without re-reading the source material.

---

## Related Notes

- [[Standup_2026-09-08]]
- [[Content_Data_Contract]]
- [[Pages_And_Routing]]
