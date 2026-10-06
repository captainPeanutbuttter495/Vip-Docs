# C4 Model - Level 2: Container Diagram

The deployable building blocks of VIP and how they communicate. Solid lines exist today; dashed lines are planned.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    visitor["<b>Site Visitor</b><br/>Browser"]
    staff["<b>Staff / Developer</b><br/>Swagger, Postman"]

    subgraph vip ["VIP System"]
        web["<b>Next.js Website</b><br/><i>Next.js 16, React 19, TS</i><br/>vip-web-frontend"]
        api["<b>Django REST API</b><br/><i>Django 6, DRF, spectacular</i><br/>vip-web-backend"]
        db[("<b>PostgreSQL 16</b><br/><i>Docker locally · RDS planned</i>")]
        files[("<b>Static files</b><br/><i>public/files, public/images</i>")]
        s3[("<b>S3 + CloudFront</b><br/><i>(planned)</i>")]
    end

    ext["<b>External sites</b><br/>Zealous, partners, YouTube"]

    visitor -->|"HTTPS"| web
    web -->|"serves"| files
    web -.->|"server-side fetch<br/>GET /api/v1/... (planned)"| api
    staff -->|"JSON / HTTP"| api
    api -->|"Django ORM<br/>psycopg 3"| db
    api -.->|"presigned URLs"| s3
    web -->|"links / embeds"| ext

    style visitor fill:#08427B,color:#fff,stroke:#073B6F
    style staff fill:#08427B,color:#fff,stroke:#073B6F
    style web fill:#438DD5,color:#fff,stroke:#3579B8
    style api fill:#438DD5,color:#fff,stroke:#3579B8
    style db fill:#438DD5,color:#fff,stroke:#3579B8
    style files fill:#438DD5,color:#fff,stroke:#3579B8
    style s3 fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style ext fill:#999,color:#fff,stroke:#888
    style vip fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = Users
- **Blue** = VIP containers (what we build and deploy)
- **Gray** = External systems
- **Gray, dashed** = Planned
- **Light border** = System boundary

## Container Details

| Container | Technology | Purpose | Port (dev) | Status |
|-----------|-----------|---------|------------|--------|
| **Next.js Website** | Next.js 16 App Router, Tailwind 4, shadcn/ui, Motion | Public site. Content from `data/` | 3000 | Current. Amplify planned |
| **Django REST API** | Django 6, DRF 3.17, drf-spectacular | Events API, health, OpenAPI | 8000 | Current. ECS planned |
| **PostgreSQL** | postgres:16, psycopg 3 | Persistent data | 5433 on host / 5432 in compose | Current. RDS planned |
| **Static files** | Next.js `public/` | Images, downloadable PDFs | — | Current |
| **S3 + CloudFront** | AWS | Tracked or protected file access | — | Planned |

## Data Flow Summary

| Flow | Direction | Data | Format |
|------|-----------|------|--------|
| Browser → Next.js | Request | Page views | HTML/RSC |
| Next.js → Django | Request (planned) | `GET /api/v1/events` | JSON |
| Staff → Django | CRUD | Events | JSON. Errors use the `{code, message, details}` envelope |
| Django → Postgres | Query | ORM SQL | TCP / PostgreSQL protocol |
| Django → S3 | Planned | Presigned download URLs | AWS SDK |

## Key Relationships

| Relationship | Protocol | Purpose |
|-------------|----------|---------|
| Website ↔ API | REST, versioned `/api/v1` | The only coupling between the repos |
| API ↔ DB | Django ORM | All persistence |
| Website ↔ DB | **none** | The frontend never talks to the DB directly (except the unmerged [[Payload_CMS_Branch]]) |
