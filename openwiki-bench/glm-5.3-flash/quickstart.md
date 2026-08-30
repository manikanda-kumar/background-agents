---
type: workflow
title: OpenWiki Quickstart
description: Orient a coding agent in the first minutes — what Open-Inspect is, the monorepo map, the bootstrap and build order, verification commands, the single-tenant security caveat, and a task-routing map into the wiki hierarchy.
tags: [quickstart, setup, bootstrap, monorepo, testing, security, task-routing]
verified:
  - by: openwiki/0.4.3
    at: 2026-08-29T06:58:43.189Z
sources:
  - id: openwiki-source-9d478d257be2df7e7d88a74e
    resource: repo://.openinspect/setup.sh
  - id: openwiki-source-8037e2358a2c4f9b2c722a11
    resource: repo://AGENTS.md
  - id: openwiki-source-9fc8a0741745a9148f4010cd
    resource: repo://docs/GETTING_STARTED.md
  - id: openwiki-source-83a5c69723e8f477e9b7dcbd
    resource: repo://docs/HOW_IT_WORKS.md
  - id: openwiki-source-5b54a58d1b51cd490b0e7162
    resource: repo://package.json
  - id: openwiki-source-8207def63e0d42ebbdef131a
    resource: repo://packages/control-plane/package.json
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
generated: { by: "opencode", at: "2026-08-29T06:58:43.189Z" }
---

# OpenWiki Quickstart

Open-Inspect is an open-source **background coding agent** system (inspired by Ramp's Inspect):
users submit tasks from a web UI, Slack, GitHub PRs, Linear, webhooks, or scheduled automations;
each task runs in a sandboxed development environment with an AI coding agent; and the work streams
back in real time, ending in commits and pull requests attributed to the prompting user. Three
tiers connected by WebSockets: a Next.js web client, a Cloudflare Workers control plane (Durable
Objects per session, D1 for indexes), and Python sandboxes on Modal (or other providers).

## Bootstrap (do this first)

```bash
bash .openinspect/setup.sh
```

The setup script (`/.openinspect/setup.sh`) checks prerequisites (Node.js ≥ 20, npm), runs
`npm install` (husky hooks via the prepare script), **builds `@open-inspect/shared` first**, verifies
git hooks, and — if Python ≥ 3.12 and `uv` are available — syncs the Python environment for
modal-infra (`uv sync --frozen --extra dev`, falling back to a pip editable install of
`packages/sandbox-runtime` + `packages/modal-infra[dev]`). It ends by running `npm run typecheck`.

**Build order matters**: whenever shared types change, build `@open-inspect/shared` first —
control-plane, web, and the bots import it at build time:

```bash
npm run build -w @open-inspect/shared
npm run build            # all packages (rebuilds shared first)
```

## The monorepo map

| Package | Lang / framework | What it is |
| --- | --- | --- |
| `packages/shared` | TypeScript | Shared types, contracts, model definitions — the build-time dependency of everything below |
| `packages/control-plane` | TypeScript / CF Workers + Durable Objects + D1 | Session lifecycle (one Durable Object per session with SQLite storage), WebSocket hub, GitHub/auth integration, scheduler, image builds |
| `packages/web` | TypeScript / Next.js 16 + React 19 | User-facing dashboard, OAuth, real-time session UI |
| `packages/sandbox-runtime` | Python | The in-sandbox runtime: bridge to the control plane, OpenCode streaming, git credential helper, commit signing, repo boot |
| `packages/modal-infra` | Python / Modal + FastAPI | Sandbox provider: sandbox lifecycle, snapshots, WebSocket bridge endpoints |
| `packages/daytona-infra`, `packages/e2b-infra`, `packages/opencomputer-infra` | TypeScript | Alternative sandbox provider template packages (persistent sandboxes / pause-resume / checkpoints) |
| `packages/slack-bot`, `packages/github-bot`, `packages/linear-bot` | TypeScript / CF Workers + Hono | Integration bots: Slack messages → sessions, PR review/@mention commands, Linear agent webhooks |

`AGENTS.md` at the repo root carries the conventions (duration units, commit style, build order,
key gotchas like PKCS#8 keys and Terraform-driven wrangler config); `docs/` carries deep dives
(`HOW_IT_WORKS.md`, `GETTING_STARTED.md`, `SETUP_GUIDE.md`, `AUTOMATIONS.md`, `IMAGE_PREBUILD.md`,
`SECRETS.md`, provider guides).

## Verify your changes

```bash
npm run typecheck                                  # builds shared, then tsc across workspaces
npm run lint:fix                                   # ESLint + Prettier fix
npm test -w @open-inspect/control-plane            # unit tests (Vitest, node env)
npm run test:integration -w @open-inspect/control-plane  # workerd/Miniflare + real D1
npm test -w @open-inspect/web                      # and the other TS workspaces
cd packages/modal-infra && pytest tests/ -v        # Python (pytest + pytest-asyncio)
cd packages/sandbox-runtime && pytest              # sandbox runtime tests
```

Control-plane integration tests run inside a real `workerd` runtime via
`@cloudflare/vitest-pool-workers` with D1 migrations applied automatically — they share one D1
instance, so clean tables in `beforeEach`/`afterEach`. Python linting is
`ruff check --fix && ruff format` from `packages/modal-infra`.

## Security caveat: single-tenant only

Open-Inspect is designed for **single-tenant deployment where all users are trusted members of the
same organization**. Everyone shares one GitHub App installation: the control plane mints
short-lived installation tokens server-side and brokers them to sandboxes on demand, all users can
reach any repo the App can access, and there is no per-user repository access validation at session
creation (user OAuth tokens are used for PR creation so attribution is real, but that is not an
isolation boundary). Details and the token table: [Security and Tokens](concepts/security-and-tokens.md).

## Where to go next: task-routing map

- **How the system is shaped** — [architecture/](architecture/overview.md): the control-plane
  worker, the session Durable Object, the data model, shared contracts, the web client, sandbox
  providers, and the sandbox runtime.
- **Domain models and invariants** — [concepts/](concepts/sessions.md): sessions, environments and
  repositories, sandbox lifecycle, security and tokens, models and provider accounts, managed skills.
- **End-to-end flows** — [workflows/](workflows/session-creation.md): session creation, prompt
  flow, git auth and pull requests, image builds, automations.
- **Bots and SCM providers** — [integrations/](integrations/github-bot.md): the Slack/GitHub/Linear
  bots and the source-control provider abstraction.
- **Deploying, configuring, observing** — [operations/](operations/deployment.md): Terraform
  deployment, configuration surface, logging and observability.
- **How it's all verified** — [testing/](testing/strategy.md): the Vitest/pytest layout, integration
  test runtime, and conventions.
