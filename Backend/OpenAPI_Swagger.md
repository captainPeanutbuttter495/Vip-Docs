# OpenAPI & Swagger

Auto-generated OpenAPI 3 schema and Swagger UI via drf-spectacular, plus the Postman collection.

---

## Overview

`drf-spectacular` builds the schema from the DRF views and serializers. It is the machine-readable API contract the frontend (and future clients) can read.

---

## Endpoints

| Path | View | Purpose |
|------|------|---------|
| `/api/schema/` | `SpectacularAPIView` | Raw OpenAPI 3 schema (YAML by default) |
| `/api/docs/` | `SpectacularSwaggerView(url_name="schema")` | Interactive Swagger UI |

## Settings

```python
REST_FRAMEWORK = {"DEFAULT_SCHEMA_CLASS": "drf_spectacular.openapi.AutoSchema", ...}
SPECTACULAR_SETTINGS = {
    "TITLE": "VIP Events API",
    "VERSION": "1.0.0",
    "SERVE_INCLUDE_SCHEMA": False,
    "DESCRIPTION": "...error envelope documented here...",
}
```

- The `?permanent` query parameter on DELETE is added with `@extend_schema(parameters=[OpenApiParameter(...)])` on the view's `delete()` handler.
- `test_openapi_schema_and_swagger_docs` asserts that both routes render.

---

## Postman

`postman/vip-events.postman_collection.json` has 7 requests:

- Create event (happy)
- Create event (validation error example)
- List events
- Get event by id
- Update event (PATCH)
- Delete event
- Delete event (permanent)

---

## Key Files

| File | Purpose |
|------|---------|
| `backend/urls.py` | Schema + docs routes |
| `backend/settings.py` | `SPECTACULAR_SETTINGS` |
| `events/views.py` | `@extend_schema` on `delete` |
| `postman/vip-events.postman_collection.json` | Manual request collection |

---

## Related Notes

- [[Events_API]]
- [[Error_Envelope]]
- [[Events_CRUD_Swagger]]
