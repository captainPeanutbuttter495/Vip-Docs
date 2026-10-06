# C4 Model - Dynamic Diagrams

Runtime behavior through key interaction flows. Flows 1–4 exist today. Flow 5 is planned.

## 1. Create Event

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
sequenceDiagram
    actor Staff as Staff (Postman / Swagger)
    participant URL as backend/urls.py
    participant View as EventListCreateView
    participant Ser as EventSerializer
    participant DB as PostgreSQL
    participant Exc as drf_exception_handler

    Staff->>URL: POST /api/v1/staff/events {title, event_type, start_datetime, ...}
    URL->>View: dispatch
    View->>Ser: is_valid()
    alt end_datetime < start_datetime or missing title
        Ser-->>View: ValidationError
        View->>Exc: handle
        Exc-->>Staff: 400 {code: validation_error, message, details: {field: [...]}}
    else valid
        rect rgb(200, 230, 200)
            Note over Ser,DB: create(): slug autogen if blank
            Ser->>DB: SELECT slug exists? (loop -2, -3, ...)
            Ser->>DB: INSERT events_event (status = draft)
        end
        DB-->>Ser: row
        Ser-->>Staff: 201 event JSON (no deleted_at)
    end
```

## 2. Partial Update (PATCH) with Merged-State Validation

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
sequenceDiagram
    actor Staff
    participant View as EventDetailView
    participant Ser as EventSerializer
    participant DB as PostgreSQL

    Staff->>View: PATCH /events/<uuid> {end_datetime}
    View->>DB: SELECT ... WHERE id = uuid AND deleted_at IS NULL
    alt not found or soft-deleted
        View-->>Staff: 404 {code: not_found}
    else found
        View->>Ser: partial=True
        rect rgb(200, 230, 200)
            Note over Ser: start = attrs.start or instance.start<br/>end = attrs.end or instance.end
            Ser->>Ser: end >= start ?
        end
        alt invalid
            Ser-->>Staff: 400 validation_error
        else valid
            Ser->>DB: UPDATE (status ignored if sent)
            Ser-->>Staff: 200 event JSON
        end
    end
    Note over Staff,View: PUT → 405 method_not_allowed (PATCH-only)
```

## 3. Delete — Soft by Default, Hard on Request

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
sequenceDiagram
    actor Staff
    participant View as EventDetailView
    participant DB as PostgreSQL

    Staff->>View: DELETE /events/<uuid>[?permanent=true]
    View->>DB: SELECT live row
    alt ?permanent=true (case-insensitive)
        View->>DB: DELETE FROM events_event
    else anything else
        View->>DB: UPDATE deleted_at = now()
    end
    View-->>Staff: 204 No Content
    Staff->>View: GET /events/<uuid>
    View-->>Staff: 404 not_found (either way)
```

## 4. Health Check

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
sequenceDiagram
    participant Probe as Probe (curl / compose / ECS planned)
    participant H as healthz()
    participant DB as PostgreSQL

    Probe->>H: GET /healthz/
    H->>DB: SELECT 1
    alt DB reachable
        DB-->>H: 1
        H-->>Probe: 200 {status: ok, database: ok}
    else exception
        H->>H: logger.exception (server-side only)
        H-->>Probe: 503 {status: error, database: unavailable}
    end
```

## 5. Events Page Reads the API *(planned — proof of concept)*

From [[OneOnOne_2026-09-25_Backend_Infra]]. The fetch runs on the Next.js server, so there's no browser-to-Django call and no CORS.

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
sequenceDiagram
    actor Visitor
    participant Page as app/events/page.tsx (Server Component)
    participant Map as mapper (API → EventItem)
    participant API as Django /api/v1/events
    participant DB as PostgreSQL
    participant Grid as EventCardGrid / EventList (client)

    Visitor->>Page: GET /events
    Page->>API: fetch GET events (server-side)
    API->>DB: SELECT live [published?] events
    DB-->>API: rows
    API-->>Page: 200 JSON array
    rect rgb(200, 230, 200)
        Note over Page,Map: Format dates (timezone TBD), derive accent from category,<br/>registration_url → cta or "Coming soon"
        Page->>Map: map(events)
        Map-->>Page: EventItem[]
    end
    Page->>Grid: events={...}
    Grid-->>Visitor: Cards + category/audience filters
    alt API error / empty
        API-->>Page: {code, message, details} or []
        Page->>Grid: events=[] → emptyMessage
    end
```

## Related Notes

- [[Events_API]]
- [[Events_Page]]
- [[Health_Endpoints]]
- [[Events_API_Integration]]
