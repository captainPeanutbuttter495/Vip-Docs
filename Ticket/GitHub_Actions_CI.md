---
title: "GitHub Actions CI"
date_started: 2026-07-07
date_finished: 2026-07-08
repo: vip-web-backend
branch: feature/github-actions
pr: "#7"
status: merged
standups: []
skills: [GitHub Actions, CI/CD, Ruff, pytest, PostgreSQL]
tags: [ticket, backend, devops]
---

# GitHub Actions CI

Set up the backend's CI pipeline: lint, format check, missing-migration check, and the pytest suite against a real PostgreSQL 16 service container.

---

## What I Did

- Applied Ruff formatting across the repo (`dbb3b86`) so the new gate starts green.
- Added `.github/workflows/ci.yml` (`b19614e`) with two parallel jobs:
  - `lint`: `ruff check`, `ruff format --check`
  - `test`: a `postgres:16` service with a `pg_isready` health check, then `makemigrations --check --dry-run`, then `pytest`
- Added per-ref `concurrency` with `cancel-in-progress` so superseded runs stop, plus pip caching.
- Used CI-only throwaway env values, so no repo secrets were needed. Hot fixes after first runs: `2705534`.

## Resume Bullets

- **Built** a GitHub Actions CI pipeline for a Django API: parallel lint and test jobs, a Postgres service container, and a missing-migration gate on every pull request.
- **Enforced** code quality with Ruff linting and formatting checks as a required PR gate.

---

## Related Notes

- [[CI_GitHub_Actions]]
- [[Testing]]
