---
title: "Health Check Endpoint"
date_started: 2026-06-24
date_finished: 2026-07-06
repo: vip-web-backend
branch: feature/health-check
pr: "merged directly (8ddb436)"
status: merged
standups: []
skills: [Django, Observability, pytest, PostgreSQL]
tags: [ticket, backend, observability]
---

# Health Check Endpoint

Added `/healthz/`, a readiness probe that checks database connectivity and returns 200 or 503 without leaking internals.

---

## Ticket

Phase 1.5: a health check to support container orchestration (planned ECS target health).

## What I Did

- Added the `healthz/` route (`0acc878`) and the view (`7aeb9d8`). It runs `SELECT 1` through Django's connection:
  - Success returns 200 `{"status":"ok","database":"ok"}`.
  - Any exception returns 503 `{"status":"error","database":"unavailable"}`.
- Logged failures server-side with `logger.exception`. The response body never includes exception text.
- Wrote 4 pytest tests (`70467a4`): happy path, JSON shape, DB down (monkeypatched), and no leaked internals.

## Resume Bullets

- **Implemented** a database-aware health-check endpoint with correct 200/503 semantics, preparing the service for load-balancer and ECS health checks.
- **Wrote** pytest coverage that simulates database outages and verifies error responses don't leak sensitive information.

---

## Related Notes

- [[Health_Endpoints]]
- [[Testing]]
- [[PostgreSQL_Setup]]
