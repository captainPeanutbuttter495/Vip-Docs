# C4 Model - Level 4: Code

Code-level detail for the backend: the Event schema, the soft-delete lifecycle, the request pipeline for a PATCH, and the proposed v2 schema.

## 1. Database Schema — `Event` (current)

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
erDiagram
    EVENTS_EVENT {
        uuid id PK "uuid4, not editable"
        varchar title "max 200, required"
        varchar slug UK "max 220, auto from title"
        text description "default empty"
        varchar status "draft | published | archived, read-only via API"
        varchar event_type "workshop | webinar | conference | other"
        varchar category "general | challenge | community"
        timestamptz start_datetime "required, UTC"
        timestamptz end_datetime "nullable, >= start"
        timestamptz created_at "auto_now_add"
        timestamptz updated_at "auto_now"
        timestamptz deleted_at "nullable, soft-delete marker, not serialized"
    }
```

Migrations: `0001_initial`, then `0002_event_deleted_at`.

## 2. Soft-Delete Lifecycle

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
stateDiagram-v2
    [*] --> Live: POST /events (201)
    Live --> Live: PATCH (200)
    Live --> SoftDeleted: DELETE (204), deleted_at = now()
    Live --> [*]: DELETE ?permanent=true (204), row removed
    SoftDeleted --> SoftDeleted: GET / PATCH / DELETE → 404 not_found
    note right of SoftDeleted
        Row stays in the DB, recoverable by hand.
        The API can't reach it: querysets filter deleted_at IS NULL.
    end note
```

## 3. Request Pipeline — PATCH `/api/v1/staff/events/<uuid>`

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
classDiagram
    class EventDetailView {
        queryset: Event.objects.filter(deleted_at__isnull=True)
        serializer_class: EventSerializer
        http_method_names: get, patch, delete, head, options
        delete(request) extend_schema(permanent)
        perform_destroy(instance)
    }
    class EventSerializer {
        fields: id, title, slug, description, status, event_type, category, start/end, created/updated
        read_only_fields: id, status, created_at, updated_at
        validate(attrs) end >= start on merged state
        create(validated_data) slug autogen
    }
    class Event {
        soft_delete()
    }
    class generate_unique_slug {
        <<function>>
        slugify(title)[:200] then -2, -3 ...
    }
    class drf_exception_handler {
        <<function>>
        returns code, message, details
    }
    EventDetailView --> EventSerializer : validates with
    EventSerializer --> Event : saves
    EventSerializer ..> generate_unique_slug : on create
    EventDetailView ..> drf_exception_handler : on error
```

## 4. Proposed v2 Schema *(planned — not built)*

From [[OneOnOne_2026-09-25_Backend_Infra]]. It is pending supervisor answers, so treat field names as proposals.

```mermaid
%%{init: {'theme': 'base', 'flowchart': {'useMaxWidth': true}, 'themeVariables': {'primaryColor': '#438DD5', 'primaryTextColor': '#fff', 'lineColor': '#666', 'fontSize': '14px'}}}%%
erDiagram
    EVENTS_EVENT_V2 {
        uuid id PK
        varchar title
        varchar slug UK "future detail pages"
        text description
        varchar category "event | program | workshop | signature_event"
        varchar registration_status "open_registration | accepting_applications | limited_seats"
        varchar event_type "free text, optional"
        timestamptz start_datetime
        timestamptz end_datetime
        varchar format "in_person | online | hybrid"
        varchar location "optional"
        varchar online_url "optional"
        varchar host "optional"
        varchar audience "students | educators | community | families"
        varchar registration_url "missing means Coming soon"
        varchar publish_status "renamed from status"
        timestamptz deleted_at
    }
```

## Related Notes

- [[Events_API]]
- [[Error_Envelope]]
- [[Events_API_Integration]]
