# C4 Model - Level 3a: Component Diagram (Frontend)

Inside the Next.js website: the root layout and providers, routes, section components, content data, and shared hooks and UI.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    visitor["<b>Site Visitor</b>"]

    subgraph next ["Next.js Website (vip-web-frontend)"]
        layout["<b>Root Layout</b><br/>app/layout.tsx<br/>fonts, skip link"]
        providers["<b>Providers</b><br/>ThemeProvider (next-themes)<br/>MotionProvider"]
        chrome["<b>Global Chrome</b><br/>NavBar + NavData, Footer,<br/>ScrollIndicator, AnnouncementBanner"]
        routes["<b>Routes</b><br/>app/**/page.tsx<br/>thin composition + metadata"]
        data["<b>Content Data</b><br/>data/[page]Data.tsx<br/>typed *Content objects"]
        sections["<b>Modular Sections</b><br/>components/modular_sections<br/>Hero, MediaFeature, ResourceLibrary,<br/>EventCardGrid, CtaCards, …"]
        pageComps["<b>Page Components</b><br/>components/pages/*"]
        ui["<b>UI Primitives</b><br/>components/ui (shadcn),<br/>Reveal, Breadcrumb, Button"]
        bgs["<b>Animated Backgrounds</b><br/>ui/backgrounds/registry.ts"]
        hooks["<b>Hooks</b><br/>use-focus-trap, use-motion-preference,<br/>use-mounted, use-now"]
        axe["<b>AxeReporter</b><br/>dev only"]
        fetcher["<b>API client / mapper</b><br/>(planned)"]
    end

    api["<b>Django API</b><br/>(planned wiring)"]

    visitor --> layout
    layout --> providers --> chrome
    layout --> routes
    layout --> axe
    routes -->|"imports"| data
    routes -->|"{...content}"| sections
    routes --> pageComps
    sections --> ui
    sections --> bgs
    sections --> hooks
    chrome --> hooks
    bgs -->|"reads pause state"| providers
    routes -.-> fetcher -.->|"GET /api/v1"| api

    style visitor fill:#08427B,color:#fff,stroke:#073B6F
    style layout fill:#438DD5,color:#fff,stroke:#3579B8
    style providers fill:#438DD5,color:#fff,stroke:#3579B8
    style chrome fill:#438DD5,color:#fff,stroke:#3579B8
    style routes fill:#438DD5,color:#fff,stroke:#3579B8
    style data fill:#438DD5,color:#fff,stroke:#3579B8
    style sections fill:#438DD5,color:#fff,stroke:#3579B8
    style pageComps fill:#438DD5,color:#fff,stroke:#3579B8
    style ui fill:#438DD5,color:#fff,stroke:#3579B8
    style bgs fill:#438DD5,color:#fff,stroke:#3579B8
    style hooks fill:#438DD5,color:#fff,stroke:#3579B8
    style axe fill:#438DD5,color:#fff,stroke:#3579B8
    style fetcher fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style api fill:#999,color:#fff,stroke:#888,stroke-dasharray: 5 5
    style next fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = Users
- **Blue** = Frontend components (what we build)
- **Gray, dashed** = Planned

## Component Details

| Component | Location | Responsibility |
|-----------|----------|----------------|
| Root Layout | `app/layout.tsx` | Fonts, `<main id="main-content">`, skip link, providers |
| Providers | `components/theme-provider.tsx`, `components/MotionProvider.tsx` | Theme class; reduced-motion + background pause state |
| Global Chrome | `components/global/*` | Nav tree from `NavData.ts`, footer, scroll indicator |
| Routes | `app/**/page.tsx` | Compose sections from content |
| Content Data | `data/*` | All copy, links, and image paths. JSON-serializable |
| Modular Sections | `components/modular_sections/*` | Pure `XProps` sections. See [[Modular_Sections]] |
| Page Components | `components/pages/*` | Page-specific pieces (About, Home, Resources) |
| UI Primitives | `components/ui/*` | shadcn + local primitives |
| Backgrounds | `components/ui/backgrounds/*` | Registry of canvas/WebGL effects |
| API client | — | **Planned**: a server-side fetch + mapper for `/events` |

## Key Relationships

| Relationship | Protocol | Purpose |
|-------------|----------|---------|
| Routes → Data | ES import | Today's content source |
| Routes → Sections | Props spread | Content/presentation split ([[Content_Data_Contract]]) |
| Sections → Providers | React context | Motion preference, theme |
| Routes → API | `fetch` in a Server Component (planned) | Replaces `data/eventsData.tsx` for events |
