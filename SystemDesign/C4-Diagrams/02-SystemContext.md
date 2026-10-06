# C4 Model - Level 1: System Context

VIP as one box: who uses it and which external systems it depends on.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    visitor["<b>Site Visitor</b><br/>Reads pages, filters events,<br/>downloads resources"]
    staff["<b>Hub Staff</b><br/>Creates and edits events"]
    dev["<b>Developer</b><br/>Runs, tests, reviews"]

    vip["<b>VIP System</b><br/>Public website + REST API<br/>for the Innovation Hub"]

    zealous["<b>Zealous</b><br/>Challenge submission portal"]
    partners["<b>Partner Program Sites</b><br/>Learn More links"]
    youtube["<b>YouTube</b><br/>Embedded video"]
    aws["<b>AWS</b><br/>Hosting, DB, files (planned)"]

    visitor -->|"HTTPS"| vip
    staff -.->|"staff API (auth planned)"| vip
    dev -->|"Swagger UI / Postman"| vip
    vip -->|"external links"| zealous
    vip -->|"external links"| partners
    vip -->|"embeds"| youtube
    vip -.->|"runs on"| aws

    style visitor fill:#08427B,color:#fff,stroke:#073B6F
    style staff fill:#08427B,color:#fff,stroke:#073B6F
    style dev fill:#08427B,color:#fff,stroke:#073B6F
    style vip fill:#1168BD,color:#fff,stroke:#0E5CA4
    style zealous fill:#999,color:#fff,stroke:#888
    style partners fill:#999,color:#fff,stroke:#888
    style youtube fill:#999,color:#fff,stroke:#888
    style aws fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
```

### Legend

- **Dark blue** = Users / Actors
- **Blue** = VIP system (what we build)
- **Gray** = External systems
- **Gray, dashed** = Planned

## Key Relationships

| Relationship | Protocol | Purpose |
|-------------|----------|---------|
| Visitor → VIP | HTTPS | Browse the site. Download PDFs from `public/files/` today, S3 later |
| Staff → VIP | REST `/api/v1/staff/events` | Event CRUD. No auth yet |
| Developer → VIP | `/api/docs/`, Postman | Explore and test the API |
| VIP → Zealous | Link (new tab) | Challenge "Submit" CTA |
| VIP → Partner sites | Link (new tab) | The Source "Learn More" |
| VIP → YouTube | Embed / lightbox | Video sections (`lib/youtube.ts`) |
| VIP → AWS | — | Planned. See [[AWS_Architecture]] |
