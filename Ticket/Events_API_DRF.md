---
title: "Events API (DRF)"
date_started: 2026-07-06
date_finished: 2026-07-07
repo: vip-web-backend
branch: feature/events-api
pr: "#6"
status: merged
standups: []
skills: [Django REST Framework, REST API Design, PostgreSQL, pytest, Postman]
tags: [ticket, backend, api]
---

# Events API (DRF)

Introduced Django REST Framework and built the first resource, the staff events API, with a project-wide error envelope.

---

## Ticket

Phase 1.5: add DRF and an `Event` resource under a versioned route, with consistent error responses matching the frontend's API contract (`CLAUDE.md` §9).

## What I Did

- Added DRF and registered it (`956ec27`, `28dd960`).
- Scaffolded the `events` app (`ebf5971`) and mounted it under `/api/v1/staff/` (`b3b94f5`).
- Built a **standardized DRF error envelope** `{code, message, details}` (`f206917`). It maps validation errors, 404s, and other API exceptions to a single shape.
- Created the `Event` model with a UUID key, unique slug, status/type/category choices, and start/end datetimes (`0c53ed6`).
- Built the serializer with cross-field validation (`end >= start`) (`62e3ae6`) and list/create/retrieve endpoints (`0c853a2`).
- Wrote the validation and retrieval tests (`1223df4`) and a Postman collection for the endpoints (`bc6561a`).

## Technical Details

| File | Change |
|------|--------|
| `events/models.py` | `Event` model |
| `events/serializers.py` | `EventSerializer` |
| `events/views.py`, `events/urls.py` | Generic views |
| `events/exceptions.py` | `drf_exception_handler` |
| `backend/settings.py` | `REST_FRAMEWORK` config |
| `postman/vip-events.postman_collection.json` | Requests |

## Resume Bullets

- **Designed and built** a versioned REST API (`/api/v1`) in Django REST Framework, with UUID keys, slug generation, and cross-field validation.
- **Standardized** API error responses into one `{code, message, details}` envelope with a custom DRF exception handler, giving frontend and future mobile clients a predictable contract.
- **Delivered** a Postman collection and automated pytest coverage alongside the endpoints.

---

## Related Notes

- [[Events_API]] (backend doc)
- [[Error_Envelope]]
- [[Events_CRUD_Swagger]]
