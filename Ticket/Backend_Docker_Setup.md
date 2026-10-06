---
title: "Backend Docker Setup"
date_started: 2026-06-18
date_finished: 2026-06-23
repo: vip-web-backend
branch: feature/backend-docker-setup
pr: "#1"
status: merged
standups: []
skills: [Django, Docker, Docker Compose, Python, Technical Writing]
tags: [ticket, backend, infrastructure]
---

# Backend Docker Setup

Scaffolded the VIP Django backend and containerized it with Docker Compose, so the service runs the same way on any machine. This was the first PR in `vip-web-backend`.

---

## Ticket

Phase 1.5 Backend Foundation: stand up a minimal Django service ("Hello, World!") that is containerized and ready for later ECS/ECR deployment.

## What I Did

- Scaffolded the Django project (`38f1ac6`) with a `hello` view at `/`.
- Wrote the `Dockerfile` on `python:3.12-slim`: unbuffered logs, no `.pyc` files, and a dependency layer that caches separately from the source.
- Added `docker-compose.yml` with a bind mount for hot reload (`bee20d3`).
- Documented both Docker engines in the README, **Docker Desktop** and **Colima** (`41c3d92`, `2d1cb50`), including the `docker compose` vs `docker-compose` difference and the "Cannot connect to the Docker daemon" fix.

## Technical Details

| File | Change |
|------|--------|
| `Dockerfile` | Python 3.12 slim image, `runserver 0.0.0.0:8000` |
| `docker-compose.yml` | `web` service, `.:/app` bind mount, port 8000 |
| `README.md` | Local and Docker run instructions |

## Resume Bullets

- **Containerized** a Django 6 REST backend with Docker and Docker Compose, giving the team a one-command local environment across macOS and Windows.
- **Wrote** onboarding documentation covering both Docker Desktop and Colima workflows, cutting setup friction for new contributors.

---

## Related Notes

- [[Docker_Local_Dev]]
- [[PostgreSQL_Setup]]
- [[Tech_Stack]]
