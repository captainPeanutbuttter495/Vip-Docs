---
title: "PostgreSQL Setup"
date_started: 2026-06-24
date_finished: 2026-06-25
repo: vip-web-backend
branch: feature/postgresql-setup
pr: "#2"
status: merged
standups: []
skills: [PostgreSQL, Django, Docker Compose, 12-Factor Config, Security]
tags: [ticket, backend, database]
---

# PostgreSQL Setup

Moved the backend onto PostgreSQL 16 in its own Compose service, with environment-driven configuration that fails fast when a secret is missing.

---

## Ticket

Replace the default database with PostgreSQL (RDS in production), and move all configuration into environment variables.

## What I Did

- Added the `psycopg` driver (`fb672b5`) and a `db` service on `postgres:16` with a `pg_isready` healthcheck. `web` waits for `service_healthy`, which prevents "connection refused" on boot.
- Stored Postgres data in the `postgres_data` named volume, so it survives `down` (but not `down -v`).
- Moved config into env vars loaded by `python-dotenv` (`ef4b184`, `1a96d17`, `51b5cc7`):
  - `DJANGO_SECRET_KEY` and `POSTGRES_DB`/`USER`/`PASSWORD` are **required**. The app refuses to start without them rather than silently falling back to SQLite.
  - `.env` is gitignored. `.env.example` documents every variable.
- Published the db on host port **5433**, so a native local Postgres on 5432 can't shadow the container. Compose injects `db:5432` for `web`.
- Rewrote the README configuration table (`6e42ecf`, `734cf0f`).

## Technical Details

| File | Change |
|------|--------|
| `backend/settings.py` | Env-driven `SECRET_KEY`, `DEBUG`, `ALLOWED_HOSTS`, `DATABASES` |
| `docker-compose.yml` | `db` service, healthcheck, volume, port 5433 |
| `.env.example` | Variable template |
| `requirements.txt` | `psycopg[binary]`, `python-dotenv` |

## Resume Bullets

- **Integrated** PostgreSQL 16 into a containerized Django service with health-gated startup and persistent volumes.
- **Hardened** configuration with required environment variables and fail-fast startup, so no secrets live in the repo and there is no silent fallback to an insecure default.

---

## Related Notes

- [[Configuration]]
- [[Docker_Local_Dev]]
- [[Backend_Docker_Setup]]
- [[Health_Check_Endpoint]]
