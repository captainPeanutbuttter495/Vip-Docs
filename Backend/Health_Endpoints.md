# Health Endpoints

The liveness/readiness probe (`/healthz/`) and the original hello-world route (`/`).

---

## Overview

`/healthz/` checks that Django can actually reach PostgreSQL, so a load balancer or ECS health check (planned) can tell "the process is up" apart from "the service works."

---

## Endpoints

### GET /healthz/

`backend/health.py → healthz()` opens a DB cursor and runs `SELECT 1`.

| Outcome | Status | Body |
|---------|--------|------|
| DB reachable | 200 | `{"status": "ok", "database": "ok"}` |
| Any exception | 503 | `{"status": "error", "database": "unavailable"}` |

On failure the exception is logged with `logger.exception(...)` server-side. The response body **never includes exception details**, and a test asserts this.

### GET /

`backend/views.py → hello()` returns plain text `Hello, World!`. It's left over from the Phase 1.5 scaffold and used as a smoke test.

---

## Key Files

| File | Purpose |
|------|---------|
| `backend/health.py` | `healthz` view |
| `backend/views.py` | `hello` view |
| `backend/urls.py` | Routes `""` and `"healthz/"` |

---

## Tests

| File | Tests | Description |
|------|-------|-------------|
| `tests/test_healthz.py` | 4 | 200 happy path, JSON shape, 503 when the DB is down (monkeypatched), no internals leaked |
| `tests/test_hello.py` | 1 | Hello body |

---

## Related Notes

- [[Docker_Local_Dev]]
- [[AWS_Architecture]]
- [[Health_Check_Endpoint]]
