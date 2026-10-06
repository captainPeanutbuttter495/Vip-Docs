# C4 Model - Level 3b: Component Diagram (Backend)

Inside the Django REST API: the URLconf, views, serializers, model, exception handler, schema generator, and health probe.

## Diagram

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
flowchart TD
    client["<b>API Client</b><br/>Next.js server, Postman, Swagger"]

    subgraph django ["Django REST API (vip-web-backend)"]
        settings["<b>Settings</b><br/>backend/settings.py<br/>env vars, REST_FRAMEWORK"]
        urls["<b>Root URLconf</b><br/>backend/urls.py"]
        hello["<b>hello</b><br/>backend/views.py"]
        health["<b>healthz</b><br/>backend/health.py"]
        eurls["<b>Events URLconf</b><br/>events/urls.py"]
        listv["<b>EventListCreateView</b><br/>ListCreateAPIView"]
        detv["<b>EventDetailView</b><br/>RetrieveUpdateDestroyAPIView<br/>PATCH-only, soft/hard delete"]
        ser["<b>EventSerializer</b><br/>date-range validate, slug autogen"]
        model["<b>Event model</b><br/>events/models.py"]
        exc["<b>drf_exception_handler</b><br/>events/exceptions.py"]
        spec["<b>drf-spectacular</b><br/>/api/schema, /api/docs"]
        admin["<b>Django Admin</b><br/>/admin/"]
    end

    db[("<b>PostgreSQL</b>")]

    client --> urls
    urls --> hello
    urls --> health
    urls -->|"api/v1/staff/"| eurls
    urls --> spec
    urls --> admin
    eurls --> listv
    eurls --> detv
    listv --> ser
    detv --> ser
    ser --> model
    model -->|"ORM"| db
    health -->|"SELECT 1"| db
    listv -.->|"errors"| exc
    detv -.->|"errors"| exc
    settings -.->|"registers"| exc
    spec -->|"introspects"| listv
    spec -->|"introspects"| detv

    style client fill:#08427B,color:#fff,stroke:#073B6F
    style settings fill:#438DD5,color:#fff,stroke:#3579B8
    style urls fill:#438DD5,color:#fff,stroke:#3579B8
    style hello fill:#438DD5,color:#fff,stroke:#3579B8
    style health fill:#438DD5,color:#fff,stroke:#3579B8
    style eurls fill:#438DD5,color:#fff,stroke:#3579B8
    style listv fill:#438DD5,color:#fff,stroke:#3579B8
    style detv fill:#438DD5,color:#fff,stroke:#3579B8
    style ser fill:#438DD5,color:#fff,stroke:#3579B8
    style model fill:#438DD5,color:#fff,stroke:#3579B8
    style exc fill:#438DD5,color:#fff,stroke:#3579B8
    style spec fill:#438DD5,color:#fff,stroke:#3579B8
    style admin fill:#438DD5,color:#fff,stroke:#3579B8
    style db fill:#999,color:#fff,stroke:#888
    style django fill:#E8E8E8,color:#333,stroke:#BBB
```

### Legend

- **Dark blue** = Clients
- **Blue** = Backend components (what we build)
- **Gray** = Database container
- **Dashed arrow** = error path / configuration

In this diagram the dashed arrows mean error paths or configuration, not "planned."

## Component Details

| Component | File | Responsibility |
|-----------|------|----------------|
| Root URLconf | `backend/urls.py` | `/`, `/healthz/`, `/api/v1/staff/`, `/api/schema/`, `/api/docs/`, `/admin/` |
| Events views | `events/views.py` | List/create, and retrieve/PATCH/delete over live rows (`deleted_at IS NULL`) |
| EventSerializer | `events/serializers.py` | Field exposure, read-only fields, `end >= start` on merged state, slug on create |
| Event model | `events/models.py` | Schema, `soft_delete()`, `generate_unique_slug()` |
| Exception handler | `events/exceptions.py` | [[Error_Envelope]] |
| Schema | drf-spectacular | [[OpenAPI_Swagger]] |
| Health | `backend/health.py` | [[Health_Endpoints]] |

Not present yet: authentication or permissions, pagination, CORS, a public read-only events route, file storage.

## Key Relationships

| Relationship | Protocol | Purpose |
|-------------|----------|---------|
| Views → Serializer | DRF | Validation and representation |
| Serializer → Model | ORM | Persistence |
| Views → Handler | DRF `EXCEPTION_HANDLER` | Uniform errors |
| spectacular → Views | Introspection | OpenAPI 3 schema |
