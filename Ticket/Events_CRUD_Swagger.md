---
title: "Events CRUD + Swagger/OpenAPI"
date_started: 2026-07-13
date_finished: 2026-07-14
repo: vip-web-backend
branch: feature/events-crud-swagger
pr: "#8"
status: merged
standups: []
skills: [Django REST Framework, OpenAPI, drf-spectacular, REST API Design, pytest]
tags: [ticket, backend, api, documentation]
---

# Events CRUD + Swagger/OpenAPI

Completed the staff events API with PATCH, soft and hard delete, merged-state validation, and auto-generated OpenAPI docs with Swagger UI.

---

## What I Did

- Added **soft delete** through a `deleted_at` marker (`bb596f8`, migration `95f8cc7`):
  - Querysets hide soft-deleted rows, so GET, PATCH, and DELETE on them return 404.
  - `?permanent=true` hard-deletes the row.
- Made updates **PATCH-only**. PUT returns 405.
- Fixed PATCH validation so the `end >= start` rule checks the **merged** state (the incoming field plus the stored value) (`71fdc0b`).
- Integrated **drf-spectacular** (`7c08365`):
  - `/api/schema/` and `/api/docs/` (Swagger UI)
  - API title/version, and the error envelope documented in the schema description
  - the `permanent` query parameter surfaced through `@extend_schema` on the `delete` handler
- Expanded the suite to **21 events API tests** (26 total) covering CRUD, validation, delete semantics, and the docs endpoints (`2e8d792`). Updated Postman.

## Technical Details

| File | Change |
|------|--------|
| `events/models.py` | `deleted_at`, `soft_delete()` |
| `events/views.py` | `EventDetailView`, `perform_destroy`, `http_method_names` |
| `events/serializers.py` | Merged-state `validate()` |
| `backend/settings.py`, `backend/urls.py` | Spectacular config and routes |
| `tests/test_events_api.py` | 21 tests |

## Resume Bullets

- **Implemented** full CRUD for a REST resource with recoverable soft delete, an opt-in hard delete, and PATCH-only updates validated against merged state.
- **Generated** interactive OpenAPI 3 documentation (Swagger UI) with drf-spectacular, giving frontend developers a self-serve API contract.
- **Grew** the backend test suite to 26 pytest tests running in CI against PostgreSQL.

---

## Related Notes

- [[Events_API]]
- [[OpenAPI_Swagger]]
- [[06-Code]]
- [[07-Dynamic-Diagrams]]
