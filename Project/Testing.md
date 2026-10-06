# Testing

Test strategy, tooling, and suites for both VIP repos.

---

## Overview

The backend has an automated pytest suite that runs in CI against a real PostgreSQL 16. The frontend relies on type checks, lint (including `jsx-a11y`), axe-core in development, and manual checks at the standard breakpoints in both themes.

---

## Backend — pytest + pytest-django

Config is in `pyproject.toml`: `DJANGO_SETTINGS_MODULE = "backend.settings"`, and test files match `test_*.py`. Tests use the pytest-django `client` and `db` fixtures and hit a real Postgres. There is no SQLite fallback, because `settings.py` requires the `POSTGRES_*` variables.

```bash
pytest                      # full suite
ruff check . && ruff format --check .
python manage.py makemigrations --check --dry-run
```

| File | Tests | Covers |
|------|-------|--------|
| `tests/test_events_api.py` | 21 | Create (happy, ignores client `status`, missing title, end < start, end == start, duplicate slug, slug autogen), list, detail + unknown id, PATCH (happy, end before stored start, start after stored end, status ignored, unknown id), PUT → 405, soft delete, `?permanent=true`, delete/patch of a soft-deleted event, delete unknown, OpenAPI schema + Swagger UI |
| `tests/test_healthz.py` | 4 | Happy path, JSON shape, DB down → 503, DB down leaks no internals |
| `tests/test_hello.py` | 1 | `/` returns "Hello, World!" |
| **Total** | **26** | |

These counts are for `origin/main @ 6b62343` (PR #8).

### On `feature/events` (unmerged) — 60 tests

The card-fields branch adds `tests/test_events_fields.py` and a shared helper module. All 60 passed locally on 2026-10-05 (`pytest -q`, about 3.3 s).

| File | Tests | Covers |
|------|-------|--------|
| `tests/test_events_fields.py` | 34 (10 functions, parametrized) | One happy path per `format` (in_person / online / hybrid); `location` / `online_url` missing per format; PATCH that flips `format` to `online` with no stored `online_url` → 400; 4 required fields (`category`, `format`, `host`, `audience`); invalid choices, including the retired `general` category; every valid category / tag / audience accepted; optional `url` / `tag` / `slug` omitted; malformed `url` / `online_url` |
| `tests/test_events_api.py` | 21 | Same as on `main`, moved onto the shared helpers |
| `tests/test_healthz.py` | 4 | Unchanged |
| `tests/test_hello.py` | 1 | Unchanged |
| **Total** | **60** | |

Commit `7b8b24e` says "38 individual tests", but pytest collects 34 in `test_events_fields.py`.

### Shared helpers — `tests/events_helpers.py`

| Helper | Purpose |
|--------|---------|
| `EVENT_KEYS` | The exact 17-key set every event response must have. Adding or removing a serializer field breaks the tests on purpose |
| `ERROR_KEYS` | `{code, message, details}` |
| `CATEGORIES`, `TAGS`, `AUDIENCES` | Choice values the frontend maps to labels and colors; they drive the parametrized "accepts every choice" test |
| `make_payload(**overrides)` | A valid in-person event; each test overrides only the field under test |
| `post_event`, `patch_event` | JSON request wrappers for the staff routes |
| `assert_validation_error(response, field)` | Asserts 400 + envelope keys + `validation_error` + the field in `details` |
| `parse_dt` | Parses ISO strings so datetimes are compared as instants, not strings |

### Techniques

- **Contract-first.** Tests are written from the brief before the implementation and are never edited to make them pass.
- **Parametrization.** `@pytest.mark.parametrize` for format variants, missing fields, invalid and valid choices.
- **Boundaries.** `end == start` is valid; end before start is a 400.
- **Fault injection.** `monkeypatch` replaces `connection.cursor` with one that raises `OperationalError` to simulate a database outage.
- **Leak tests.** A fake DB error containing a host, port and password must not appear in the 503 body.
- **Exact-shape assertions.** `set(body.keys()) == EVENT_KEYS` catches accidentally exposed fields like `deleted_at`.

## Frontend

| Check | Command / Tool | Notes |
|-------|----------------|-------|
| Types | `npx tsc --noEmit` | Run on every PR |
| Lint | `npm run lint` (ESLint 9, `eslint-config-next`, `jsx-a11y`) | |
| a11y (runtime) | `components/AxeReporter.tsx` | Logs axe-core violations in dev only (`NODE_ENV !== "production"`) |
| Visual / manual | Breakpoints: base, `834px`, `1024px`, `1440px`; light + dark; reduced motion | PR write-ups record this checklist |
| Screenshots | Playwright (installed dev dependency) | Used ad hoc to capture PR screenshots |

There is no automated frontend test suite or frontend CI workflow yet.

---

## CI

See [[CI_GitHub_Actions]]. The backend runs `lint` (ruff check + format check) and `test` (migrations check + pytest against a `postgres:16` service) on every PR and on every push to `main`.

---

## Related Notes

- [[Events_API]]
- [[Health_Endpoints]]
- [[CI_GitHub_Actions]]
- [[Accessibility]]
