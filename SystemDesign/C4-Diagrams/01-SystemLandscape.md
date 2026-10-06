# C4 Model - Level 0: System Landscape

The broadest view: the Global HSI Equity Innovation Hub's digital ecosystem, where VIP sits in it, and which systems are current versus planned.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    public["<b>Public Visitors</b><br/>students, educators,<br/>families, partners"]
    staff["<b>Hub Staff</b><br/>content + events"]
    team["<b>Tech Team</b><br/>developers, designers"]

    subgraph hub ["Global HSI Equity Innovation Hub — digital ecosystem"]
        vip["<b>VIP Website + API</b><br/>vip-web-frontend + vip-web-backend"]
        ios["<b>iOS App</b><br/>(future)"]
        rn["<b>React Native App</b><br/>(future)"]
        xr["<b>Vision Pro / AR / VR</b><br/>(future)"]
        tools["<b>Internal Tools</b><br/>(future)"]
    end

    github["<b>GitHub</b><br/>innovation-hub-tech-team"]
    figma["<b>Figma</b><br/>wireframes + design system"]
    aws["<b>AWS</b><br/>Amplify · ECS · RDS · S3<br/>(planned)"]
    payload["<b>Payload CMS</b><br/>(unmerged experiment)"]
    ext["<b>Partner Sites</b><br/>Zealous portal, partner<br/>program pages, YouTube"]

    public -->|"browses"| vip
    staff -.->|"manages events (planned UI)"| vip
    team -->|"builds from"| figma
    team -->|"pushes / PRs"| github
    github -.->|"deploys"| aws
    vip -.->|"hosted on"| aws
    vip -->|"links out to"| ext
    vip -.->|"editorial copy?"| payload
    ios -.->|"shared API"| vip
    rn -.->|"shared API"| vip
    xr -.->|"shared API"| vip
    tools -.-> vip

    style public fill:#08427B,color:#fff,stroke:#073B6F
    style staff fill:#08427B,color:#fff,stroke:#073B6F
    style team fill:#08427B,color:#fff,stroke:#073B6F
    style vip fill:#1168BD,color:#fff,stroke:#0E5CA4
    style ios fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style rn fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style xr fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style tools fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style github fill:#999,color:#fff,stroke:#888
    style figma fill:#999,color:#fff,stroke:#888
    style aws fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style payload fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style ext fill:#999,color:#fff,stroke:#888
    style hub fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = Users / Actors
- **Blue** = VIP (what we build today)
- **Gray** = External systems
- **Gray, dashed** = Future or planned

## Key Relationships

| Relationship | Protocol | Purpose |
|-------------|----------|---------|
| Visitors → VIP | HTTPS | Public website (Phase 1) |
| Staff → VIP | HTTPS (planned admin/staff UI) | Manage events. Today this is done by hand through Swagger/Postman |
| Future clients → VIP API | REST `/api/v1` | Django becomes the shared business-logic layer |
| VIP → AWS | — | Planned hosting. See [[AWS_Architecture]] |
| VIP → Payload CMS | Direct DB (unmerged) | Ownership unresolved. See [[Payload_CMS_Branch]] |
| Tech team → Figma | Figma MCP (read-only) | Design source of truth |
