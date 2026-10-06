
How `vip-web-backend` is configured: everything comes from environment variables, and it fails fast when something is missing.

---

## Overview

`backend/settings.py` calls `load_dotenv(BASE_DIR / ".env")` (python-dotenv, `override=False`). **Real process env vars win over `.env`**, which is how Docker Compose and CI inject their own values. `.env` is gitignored. `.env.example` is committed and documents every variable.

---

## Environment Variables

| Variable | Required | Default | Purpose |
|----------|----------|---------|---------|
| `DJANGO_SECRET_KEY` | **Yes** | — (startup `KeyError`) | Signing key |
| `DJANGO_DEBUG` | No | `False` | Only the literal string `True` enables debug |
| `DJANGO_ALLOWED_HOSTS` | No | empty | Comma-separated |
| `POSTGRES_DB` | **Yes** | — | Database name |
| `POSTGRES_USER` | **Yes** | — | Database user |
| `POSTGRES_PASSWORD` | **Yes** | — | Database password |
| `POSTGRES_HOST` | No | `localhost` | Compose injects `db` |
| `POSTGRES_PORT` | No | `5432` | `.env.example` uses **5433**, the compose db's published host port. Compose injects `5432` for `web` |

The required variables fail hard, on purpose, so the app never silently falls back to SQLite or an insecure key.

---

## Notable Settings

| Setting | Value | Why it matters |
|---------|-------|----------------|
| `INSTALLED_APPS` | + `rest_framework`, `drf_spectacular`, `events` | |
| `REST_FRAMEWORK.EXCEPTION_HANDLER` | `events.exceptions.drf_exception_handler` | [[Error_Envelope]] |
| `TIME_ZONE` / `USE_TZ` | `UTC` / `True` | The frontend must format dates for display. Which timezone to show is an open question in [[Events_API_Integration]] |
| `DATABASES.default` | PostgreSQL via env | No SQLite anywhere |
| Static/media | `STATIC_URL = "static/"`. No `MEDIA_ROOT`, no S3 | File storage is *(planned)*. See [[AWS_Architecture]] |

---

## Key Files

| File | Purpose |
|------|---------|
| `backend/settings.py` | All settings |
| `.env.example` | Variable template |
| `docker-compose.yml` | Overrides `POSTGRES_HOST` / `POSTGRES_PORT` for `web` |
| `.github/workflows/ci.yml` | Throwaway CI values |

---

## Related Notes

- [[Docker_Local_Dev]]
- [[CI_GitHub_Actions]]
- [[PostgreSQL_Setup]]
