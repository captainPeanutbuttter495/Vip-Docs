# Standup

Meeting transcripts, stand-up prep notes, supervisor question lists, and 1:1 notes. Read these before starting a ticket, because they hold the decisions and context behind the work.

---

## How This Folder Works

- **One file per meeting**, named by the meeting date so files sort chronologically:
  - `Standup_YYYY-MM-DD.md`: weekly stand-up (transcript or prep notes)
  - `Questions_YYYY-MM-DD[_Topic].md`: question lists for my supervisor
  - `OneOnOne_YYYY-MM-DD_Topic.md`: 1:1 meetings
- Every file starts with frontmatter: `date`, `type`, `repo`, `ticket`, `source`. The `source` field points to the original file when one was imported.
- New meetings start from `_Templates/Standup_Template.md`. Paste the raw transcript under **Transcript / Notes**.
- **Claude's workflow:** when a ticket starts, search this folder for the ticket name and read the most recent matching notes. Answers here override assumptions. Pre-meeting question drafts may still be written to a repo's `docs/` first and copied here afterward.

---

## Index

| Date | File | Type | Ticket |
|------|------|------|--------|
| 2026-08-19 | [[Standup_2026-08-19]] | Transcript | UI updates: VIP About illustrations, resource card resize |
| 2026-08-28 | [[Standup_2026-08-28]] | Transcript | Hero artwork cropping on challenge pages |
| 2026-09-08 | [[Standup_2026-09-08]] | Transcript | The Source data extraction |
| 2026-09-18 | [[Standup_2026-09-18]] | Prep notes | Reusable "Ready to Submit?" CTA |
| 2026-09-21 | [[Questions_2026-09-21]] | Supervisor questions | Reusable CTA + Event page review |
| 2026-09-21 | [[Questions_2026-09-21_Events_Page]] | Supervisor questions | Events page |
| 2026-09-24 | [[Standup_2026-09-24]] | Prep notes | Website & Backend Integration |
| 2026-09-25 | [[OneOnOne_2026-09-25_Backend_Infra]] | 1:1 | Events API integration (model v2 + real data) |
| 2026-10-01 | [[Standup_2026-10-01]] | Transcript | Events API presentation; next steps (compatibility, rate limiter, ECS research) |

Imported files were renamed to the date *inside* the file: `standup_Aug_27.md` is dated Aug 28, and `standup_Sept_9.md` is dated Sep 8. The Sept 24 notes were identical in both repos and are stored once. The originals are still in each repo's `docs/`.

---

## Related Notes

- [[Ticket/README|Ticket index]]
- [[README|Vault home]]
