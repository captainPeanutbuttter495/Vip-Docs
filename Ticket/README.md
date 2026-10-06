# Ticket

A running history of the work I've done on VIP: one note per ticket or PR, each ending with resume-ready bullets. [[Resume_Highlights]] collects the best of them, grouped by skill, for job applications.

---

## How This Folder Works

- New tickets start from `_Templates/Ticket_Template.md`. Name them `Ticket_Name.md`, PascalCase with underscores. A name must not match a note in another folder, because wiki-links resolve by file name.
- Frontmatter fields: `status` (`in-progress` · `in-review` · `merged` · `blocked` · `needs-confirmation`), `repo`, `branch`, `pr`, dates, `standups`, and `skills`.
- **Resume bullets:** start with an action verb. Only use numbers that are actually recorded (tests, files, pages). Never invent metrics.
- When a ticket merges, update its status and bullets, then refresh [[Resume_Highlights]].

---

## Index

| # | Ticket | Repo | PR | Dates | Status |
|---|--------|------|----|-------|--------|
| 1 | [[Backend_Docker_Setup]] | backend | #1 | 2026-06-18 → 06-23 | merged |
| 2 | [[PostgreSQL_Setup]] | backend | #2 | 06-24 → 06-25 | merged |
| 3 | [[Health_Check_Endpoint]] | backend | direct merge | 06-24 → 07-06 | merged |
| 4 | [[Events_API_DRF]] | backend | #6 | 07-06 → 07-07 | merged |
| 5 | [[GitHub_Actions_CI]] | backend | #7 | 07-07 → 07-08 | merged |
| 6 | [[Events_CRUD_Swagger]] | backend | #8 | 07-13 → 07-14 | merged |
| 7 | [[VIP_Landing_Page]] | frontend | #31 → #41 | 07-30 → 08-03 | merged |
| 8 | [[National_Challenge_UI_Updates]] | frontend | #49 → #54 | 08-17 → 08-19 | merged |
| 9 | [[VIP_About_And_Resource_Cards]] | frontend | #52 → #54 | 08-19 | merged |
| 10 | [[Hero_Cropping_Fix]] | frontend | #66 | 08-28 → 08-31 | merged (staging) |
| 11 | [[VIP_Site_Audit]] | frontend | ? | ~09-01 | needs confirmation |
| 12 | [[The_Source_Data]] | frontend | #98 → #101 | 09-08 → 09-09 | merged |
| 13 | [[Reusable_CTA]] | frontend | open → staging | 09-18 → | in review |
| 14 | [[Event_Page]] | frontend | draft | 09-18 → 09-21 | in review |
| 15 | [[Events_API_Integration]] | both | — | 09-24 → | in progress (core flow built; CTA, docs, E2E left) |

Sources: `git log` of both repos (commits by `captainPeanutbuttter495`), the frontend's `docs/PR_*.md` write-ups, and standup notes.

---

## Related Notes

- [[Resume_Highlights]]
- [[Standup/README|Standup index]]
