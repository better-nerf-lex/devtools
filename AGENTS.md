# Devtools Agent Guide

## Scope

`devtools` is local-development infrastructure, not a production application. It contains a Java/Spring Cloud Gateway WebFlux proxy used to expose the frontend and backend through `http://localhost:8080`, plus local Docker Compose support.

This repository also stores the versioned workspace-wide agent documents under `docs/agents`. Those documents describe production repositories but do not make `devtools` part of the production runtime.

## Local Proxy Responsibilities

| Path | Responsibility |
| --- | --- |
| `src/main/resources/application.yml` | Local proxy routes, CORS, management exposure, logging |
| `src/main/java` | Spring application bootstrap/configuration |
| `docker-compose.yml` | Legacy/fallback local PostgreSQL definition; prefer `../gateway/compose.yml` for the full local dependency stack |
| `docs/agents` | Shared Lexai AI-agent workspace guidance |

## Rules

- Keep this project local-development focused. Do not encode production routing, IAM, deployed secrets, or runtime assumptions here as if they were infrastructure.
- Changes to proxy paths or ports must be checked against frontend/backend local URLs and documented for developer usage.
- Keep broad local CORS or debug logging explicitly local; do not copy such settings into `gateway` or `infra` production configuration.
- Do not start or rebuild containers for documentation-only work. The canonical local dependency stack is `../gateway/compose.yml`; start it before `gateway`, then run other services and the devtools proxy. When a proxy behavior change is made, validate routing against the already-running local services when available.
- Update `docs/agents` whenever shared architecture, production project ownership, or cross-project quality rules change.

## Commands

Run from `devtools` for code/configuration changes:

```bash
./mvnw test
```

The local stack is normally already running when agents work in this workspace.
