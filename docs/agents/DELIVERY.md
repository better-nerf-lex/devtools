# Production Delivery Standards

## Decision Order

1. Protect confidentiality, authorization, billing correctness, and data integrity.
2. Preserve existing public and internal contracts unless the task requires changing them.
3. Match the owning project's architecture and naming.
4. Keep the smallest implementation that is testable and operationally clear.

## Security And Privacy

- Assume legal documents, generated text, case data, team membership, and billing records contain sensitive customer information.
- Validate all input at trust boundaries: browser requests, uploads, webhook bodies, third-party responses, and configuration.
- Authenticate and authorize reads and mutations server-side; check tenant/team ownership before storage or AI work.
- Avoid logging request bodies, document text, file bytes/keys, authorization headers, cookies, secrets, or unnecessary third-party payloads.
- Keep secrets in environment/Secrets Manager wiring. Never place credentials in source, Terraform values committed to version control, fixtures, or agent documentation.
- For uploads and generated content, control file size/type, key construction, response headers, and content exposure.

## Data And Integrations

- Use migrations for persisted schema changes and consider rollback, existing rows, indexes, and uniqueness constraints.
- For retries, webhooks, checkout completion, token grants, and file moves, explicitly address idempotency and partial failure.
- Keep external calls bounded and observable with useful sanitized errors; do not silently convert failed authorization or billing calls into success.
- Do not upgrade frameworks, SDKs, cloud resources, or API versions incidentally. When an upgrade is part of a task, verify migration guidance and compatibility.

## Verification

Run relevant checks from the changed project:

| Project | Baseline checks |
| --- | --- |
| `client-front` | `bun run lint`, `bun run build` |
| `gateway` | `./mvnw test` |
| `file-service` | `npm test -- --runInBand`, `npm run build` |
| `subscription-service` | `npm test -- --runInBand`, `npm run build` |
| `infra` | `terraform fmt -check -recursive`; run `terraform validate` in affected initialized environments |

For high-impact flows, add targeted verification for authentication and access control, subscription/token transitions, Stripe webhook repeats and ordering, S3 failure behavior, Terraform plan output, or the visible UI state as applicable.

## Completion

- State affected projects and meaningful contracts changed.
- State checks run and any check that could not be run.
- Surface deployment steps, migration requirements, new environment variables, or intentional follow-up risk.
