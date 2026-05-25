# Lexai Local Development Tools

`devtools` provides the local reverse proxy for the Lexai workspace and stores the shared AI-agent workspace guidance. It is a local-development repository, not a production service.

## Workspace Layout

Place the repositories as sibling folders under one local workspace directory:

```text
lexai/
├── client-front/
├── devtools/
├── file-service/
├── gateway/
├── infra/
├── landing/
└── subscription-service/
```

The proxy is configured in `src/main/resources/application.yml`:

| Incoming request | Required running service | Target |
| --- | --- | --- |
| `/api/**` | `gateway` backend | `http://localhost:8081` with `/api` stripped |
| All other paths | `client-front` Vite application | `http://localhost:5173` |

## Local Startup

1. Start the shared local dependencies from `gateway`:

   ```bash
   cd ../gateway
   docker compose up -d
   ```

   Gateway owns the local dependency stack. Its Compose file starts PostgreSQL on `localhost:5432`, Elasticsearch on `localhost:9200`, Kibana on `localhost:5601`, and LocalStack on `localhost:4566`. Start this stack before the application services so `gateway`, `file-service`, and `subscription-service` all connect to the same local dependencies.

2. Run `gateway` before the other application services:

   ```bash
   ./mvnw spring-boot:run
   ```

   `gateway` expects the local PostgreSQL database `gateway` with user `gateway_user` and password `gateway_password`, matching `gateway/compose.yml`.

3. Run the other services required by the flow you are working on:

   ```bash
   cd ../client-front
   bun run dev
   ```

   Start additional services such as `file-service` and `subscription-service` when working on flows that call them. The proxy expects `gateway` on port `8081` and `client-front` on port `5173`.

4. Run the local proxy:

   ```bash
   cd ../devtools
   ./mvnw spring-boot:run
   ```

5. Open [http://localhost:8080](http://localhost:8080).

## AI Agent Workspace Setup

The workspace-wide agent documentation is committed here because `lexai/` is a local parent directory containing multiple independent Git repositories. The canonical shared documentation is:

```text
devtools/docs/agents/
├── WORKSPACE.md
├── ARCHITECTURE.md
└── DELIVERY.md
```

Each engineer must copy the loader files from `devtools/docs/agents/workspace-root/` into their local parent `lexai/` directory, preserving paths:

```text
devtools/docs/agents/workspace-root/AGENTS.md                  -> lexai/AGENTS.md
devtools/docs/agents/workspace-root/CLAUDE.md                  -> lexai/CLAUDE.md
devtools/docs/agents/workspace-root/README.md                  -> lexai/README.md
devtools/docs/agents/workspace-root/.junie/guidelines.md       -> lexai/.junie/guidelines.md
devtools/docs/agents/workspace-root/.agent/rules/front.md      -> lexai/.agent/rules/front.md
```

From the parent `lexai/` directory:

```bash
cp devtools/docs/agents/workspace-root/AGENTS.md ./AGENTS.md
cp devtools/docs/agents/workspace-root/CLAUDE.md ./CLAUDE.md
cp devtools/docs/agents/workspace-root/README.md ./README.md
mkdir -p .junie .agent/rules
cp devtools/docs/agents/workspace-root/.junie/guidelines.md .junie/guidelines.md
cp devtools/docs/agents/workspace-root/.agent/rules/front.md .agent/rules/front.md
```

The loader files resolve the canonical shared documents through relative `@devtools/docs/agents/...` references. Each individual repository also contains its own local `AGENTS.md` and agent entry files for work performed outside the parent workspace.
