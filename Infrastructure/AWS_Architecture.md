# AWS Architecture *(planned)*

The target AWS hosting for VIP. **None of this is built yet.** It comes from the frontend `CLAUDE.md` §3 and §5.4, and my supervisor owns the infrastructure side.

---

## Overview

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    visitor["<b>Site Visitor</b>"]

    subgraph aws ["AWS (planned)"]
        amplify["<b>AWS Amplify</b><br/>Next.js hosting"]
        ecs["<b>ECS service</b><br/>Django container"]
        ecr["<b>ECR</b><br/>image registry"]
        rds[("<b>RDS PostgreSQL</b>")]
        s3[("<b>S3</b><br/>files")]
        cf["<b>CloudFront</b><br/>CDN"]
    end

    visitor -.-> amplify
    amplify -.->|"REST /api/v1"| ecs
    ecr -.->|"image"| ecs
    ecs -.->|"ORM"| rds
    ecs -.->|"presigned URLs"| s3
    visitor -.->|"download"| cf -.-> s3

    style visitor fill:#08427B,color:#fff,stroke:#073B6F
    style amplify fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style ecs fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style ecr fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style rds fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style s3 fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style cf fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style aws fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = users
- **Gray, dashed** = planned, not built

---

## Planned Pieces

| Piece | Role | Known requirements |
|-------|------|--------------------|
| AWS Amplify | Frontend hosting | Phase 1 launch target |
| ECR + ECS | Backend container registry and service | The `Dockerfile` exists but still runs `runserver`. It needs a production WSGI server (e.g. gunicorn) first |
| RDS PostgreSQL | Production DB | The same RDS is used by the `payload` schema on [[Payload_CMS_Branch]] |
| S3 + CloudFront | Files and downloads | The frontend asks the API for a download URL. It never builds S3 URLs or hard-codes bucket names |
| Health checks | ECS/ALB target health | `/healthz/` is ready. See [[Health_Endpoints]] |

## Open Items

- A test environment, so a deployed frontend can read a hosted backend (flagged in [[OneOnOne_2026-09-25_Backend_Infra]]).
- Bearer-token auth on staff write routes before anything is public.
- Secrets management for `DJANGO_SECRET_KEY` and the DB credentials.

---

## Related Notes

- [[Tech_Stack]]
- [[03-Container]]
- [[Docker_Local_Dev]]
- [[Project_Phases]]
