# Payload CMS Branch *(unmerged)*

An experiment on `vip-web-frontend` `origin/feature/payload-cms` (unmerged since 2026-07-22) that embeds Payload CMS in the Next.js app, talking **directly** to the shared Postgres.

---

## Overview

- Payload stores its data in a `payload` schema in the same RDS PostgreSQL that Django uses.
- That branch's `CLAUDE.md` §5.5 splits ownership: **Payload owns editorial page copy, Django owns application data.**
- It never says which side owns **events**.
- `staging`'s `CLAUDE.md` §6.8 still says "No CMS has been chosen yet." The two branches contradict each other.

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart LR
    next["<b>Next.js + Payload</b><br/>feature/payload-cms"]
    django["<b>Django API</b>"]
    subgraph pg ["PostgreSQL (RDS)"]
        ps[("<b>payload</b> schema<br/>editorial copy")]
        ds[("<b>public</b> schema<br/>application data")]
    end
    next -.->|"direct DB access"| ps
    next -->|"REST /api/v1"| django
    django --> ds

    style next fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style django fill:#438DD5,color:#fff,stroke:#3579B8
    style ps fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style ds fill:#438DD5,color:#fff,stroke:#3579B8
    style pg fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Blue** = current
- **Gray, dashed** = unmerged experiment

---

## Why It Matters

- **Don't assume either answer** on events ownership. Events are currently being built in Django ([[Events_API_Integration]]).
- A CMS writing directly to the DB bypasses the API boundary ([[Content_Data_Contract]]). That needs a decision before Phase 2.

---

## Related Notes

- [[Content_Data_Contract]]
- [[AWS_Architecture]]
- [[01-SystemLandscape]]
