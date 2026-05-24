# Lexai Workspace Guide

## Scope

Lexai is a production legal-document SaaS. Treat reliability, tenant isolation, document confidentiality, billing accuracy, and deployability as product requirements.

The production projects are independent repositories:

| Project | Responsibility | Detailed guidance |
| --- | --- | --- |
| `client-front` | Customer web application | `client-front/AGENTS.md` |
| `gateway` | Public API, auth, domain workflows, AI orchestration | `gateway/AGENTS.md` |
| `file-service` | Private object-storage adapter | `file-service/AGENTS.md` |
| `subscription-service` | Stripe billing and token entitlements | `subscription-service/AGENTS.md` |
| `infra` | AWS infrastructure and environment configuration | `infra/AGENTS.md` |

## Read First

Before changing a production project:

1. Read this file and that project's `AGENTS.md`.
2. For cross-service or deployment work, read `devtools/docs/agents/ARCHITECTURE.md`.
3. For feature work, security, tests, or release-related work, read `devtools/docs/agents/DELIVERY.md`.
4. Inspect existing implementation and configuration before selecting a pattern or adding a dependency.

## Non-Negotiables

- Keep changes inside the owning project unless a contract, deployment, or UI change genuinely requires coordinated edits.
- Do not expose legal document contents, personal data, access tokens, refresh tokens, Stripe secrets, AI prompts containing customer content, or signed storage URLs in logs or errors.
- Enforce authorization at the backend for every tenant-owned resource. Frontend route guards are user experience only.
- Treat API payloads, webhook payloads, storage keys, environment variables, database schemas, and Terraform module interfaces as contracts. Update consumers and tests when a contract changes.
- Do not commit secrets or real customer data. Keep configuration environment-driven and update examples only with safe values.
- Add or update focused tests for changed behavior. Run the smallest meaningful validation in each affected project before finishing.
- Preserve existing architecture. Do not mix Spring patterns into NestJS services, bypass Feature-Sliced Design on the frontend, or edit deployed infrastructure manually instead of Terraform.

## Product Boundaries

- The browser talks to `gateway`; it must not directly become the authority for storage access or billing entitlements.
- `gateway` owns authenticated product workflows, access checks, AI/document operations, and integration calls to internal services.
- `file-service` owns S3 operations; it must not decide business ownership of documents.
- `subscription-service` owns billing state, Stripe event handling, and token ledger correctness; `gateway` consumes that state.
- `infra` owns AWS wiring, secrets injection, service discovery, public routing, network permissions, and runtime settings.

## Working Method

- Prefer a small coherent change over speculative abstractions.
- For material design choices, state trade-offs and choose the option that best protects correctness and maintainability.
- Keep documentation accurate when commands, environment variables, boundaries, or deployment behavior change.
- Never start or rebuild containers merely to inspect code. The local stack may already be running; run project checks when needed.
