---
type: architecture
title: Data Model & Storage
description: Where Open-Inspect state lives — the shared D1 schema and its migrations, the per-session Durable Object SQLite schema, the per-aggregate D1 stores, R2 media storage and KV caches, and the encryption keys that protect tokens and secrets at rest.
tags: [architecture, data-model, d1, sqlite, durable-objects, migrations, r2, kv, encryption, secrets]
verified:
  - by: openwiki/0.4.3
    at: 2026-08-29T06:58:43.189Z
sources:
  - id: openwiki-source-43801d43be5d7e8f8c4f1696
    resource: repo://docs/MULTI_REPO_AUTOMATIONS.md
  - id: openwiki-source-333fb60a4bff5e1935e75098
    resource: repo://packages/control-plane/README.md
  - id: openwiki-source-a968e65fa8624856e8611f4c
    resource: repo://packages/control-plane/src/auth/crypto.ts
  - id: openwiki-source-e1eb7b9ad7610b4c3c628cec
    resource: repo://packages/control-plane/src/auth/github-app.ts
  - id: openwiki-source-bfc97e667e626c5696dc3b3f
    resource: repo://packages/control-plane/src/auth/provider-account-crypto.ts
  - id: openwiki-source-2b411ed73fdeabff9e415c70
    resource: repo://packages/control-plane/src/auth/user/better-auth.ts
  - id: openwiki-source-02d3d70cb0b58626d6823905
    resource: repo://packages/control-plane/src/auth/webhook-key.ts
  - id: openwiki-source-d6db273a7260c26ff3ad7e5c
    resource: repo://packages/control-plane/src/autofix/handler.ts
  - id: openwiki-source-3a7ac9f1def780bcacf7f603
    resource: repo://packages/control-plane/src/db/automation-store.ts
  - id: openwiki-source-e40a80e7f2b63a9be44780ed
    resource: repo://packages/control-plane/src/db/bulk-insert.ts
  - id: openwiki-source-027bcdba0353ca5952cfc15b
    resource: repo://packages/control-plane/src/db/commit-signing.ts
  - id: openwiki-source-cf01e95fca84ae5a94b53e65
    resource: repo://packages/control-plane/src/db/environments.ts
  - id: openwiki-source-4cfc3d1c11ffb7fab939e818
    resource: repo://packages/control-plane/src/db/image-build-finalization.ts
  - id: openwiki-source-ab907f4c9536964859be35aa
    resource: repo://packages/control-plane/src/db/image-builds.test.ts
  - id: openwiki-source-c1104e7eb4cef3024711d45d
    resource: repo://packages/control-plane/src/db/image-builds.ts
  - id: openwiki-source-6742148b866ee5c7b9533092
    resource: repo://packages/control-plane/src/db/instrumented-d1.ts
  - id: openwiki-source-9b63d8f807d8e8c6a5ffc97c
    resource: repo://packages/control-plane/src/db/integration-settings.ts
  - id: openwiki-source-5a5bbf2077a0d187d4ffd946
    resource: repo://packages/control-plane/src/db/json-columns.test.ts
  - id: openwiki-source-0fb53c4dbbcd6cf979d9dea6
    resource: repo://packages/control-plane/src/db/json-columns.ts
  - id: openwiki-source-2e1968ff56e36a54c1f81991
    resource: repo://packages/control-plane/src/db/mcp-servers.ts
  - id: openwiki-source-55c504b46a09a0cb57eb171a
    resource: repo://packages/control-plane/src/db/model-provider-account-atomic-writer.ts
  - id: openwiki-source-d45c1f6838a48d0e1538b92b
    resource: repo://packages/control-plane/src/db/query-limits.ts
  - id: openwiki-source-b3029b07c424d498935e315e
    resource: repo://packages/control-plane/src/db/repo-metadata.ts
  - id: openwiki-source-475734dc9ffe03d69d908b54
    resource: repo://packages/control-plane/src/db/scoped-secrets.ts
  - id: openwiki-source-4b3e25c315b66b7ef4a6d7d8
    resource: repo://packages/control-plane/src/db/secrets-validation.ts
  - id: openwiki-source-ad15a302aac7be1dd07e9481
    resource: repo://packages/control-plane/src/db/session-index.ts
  - id: openwiki-source-4ff058c6c626ae5805c358fa
    resource: repo://packages/control-plane/src/db/session-pull-request-store.ts
  - id: openwiki-source-36f3630666778fc90d24fba0
    resource: repo://packages/control-plane/src/db/skill-profiles.ts
  - id: openwiki-source-3f6e224b2e23c9dabfe00dba
    resource: repo://packages/control-plane/src/db/skills.ts
  - id: openwiki-source-d2de27bfae64ae9ec188830b
    resource: repo://packages/control-plane/src/db/sql-database.ts
  - id: openwiki-source-4818e9ba1908baa273ca5d8e
    resource: repo://packages/control-plane/src/db/user-scm-tokens.ts
  - id: openwiki-source-e220ecfccdae416450a1c008
    resource: repo://packages/control-plane/src/db/user-store.ts
  - id: openwiki-source-557254ea34d55b02eef467a0
    resource: repo://packages/control-plane/src/env-validation.ts
  - id: openwiki-source-6273f1041fe67acec10358e4
    resource: repo://packages/control-plane/src/image-builds/provenance.ts
  - id: openwiki-source-78da2b6e3769fd428b85fe5a
    resource: repo://packages/control-plane/src/index.ts
  - id: openwiki-source-a47cd3511e1859b65c7c2130
    resource: repo://packages/control-plane/src/media.ts
  - id: openwiki-source-9634c81ea81a2f17d2906353
    resource: repo://packages/control-plane/src/routes/automations.ts
  - id: openwiki-source-2f03a3c121b1d902de28dc9a
    resource: repo://packages/control-plane/src/routes/commit-signing.ts
  - id: openwiki-source-f906fd8a6ae2896d6518f3e5
    resource: repo://packages/control-plane/src/routes/mcp-servers.ts
  - id: openwiki-source-4541aa742c69d8bf1c2769c4
    resource: repo://packages/control-plane/src/routes/repos.ts
  - id: openwiki-source-b0c033755ca51371aea86c72
    resource: repo://packages/control-plane/src/routes/session-attachments.ts
  - id: openwiki-source-6adabc4fd5f7f5fb33685c4d
    resource: repo://packages/control-plane/src/routes/session-media-artifacts.ts
  - id: openwiki-source-26017490716b93e3bacd6b3f
    resource: repo://packages/control-plane/src/routes/session-media-upload.ts
  - id: openwiki-source-12eddf76bf761158a1fb4559
    resource: repo://packages/control-plane/src/sandbox/lifecycle/image-selection.ts
  - id: openwiki-source-f69048d2562235a60f688786
    resource: repo://packages/control-plane/src/session/components.ts
  - id: openwiki-source-0f7dc7a19c00389ea0e86e0f
    resource: repo://packages/control-plane/src/session/durable-object.ts
  - id: openwiki-source-c5086611b0178ca7071daa2c
    resource: repo://packages/control-plane/src/session/http/handlers/attachments.handler.ts
  - id: openwiki-source-5c3aae3f8b776193c21c4216
    resource: repo://packages/control-plane/src/session/initialize.ts
  - id: openwiki-source-893ac0a294bbda5cd7dd3bc7
    resource: repo://packages/control-plane/src/session/message-repository.ts
  - id: openwiki-source-3a37470381153c4f7562bd60
    resource: repo://packages/control-plane/src/session/schema.test.ts
  - id: openwiki-source-b5e52d398550648420138d80
    resource: repo://packages/control-plane/src/session/schema.ts
  - id: openwiki-source-111326c6986085d1945bd815
    resource: repo://packages/control-plane/src/session/services/session-attachment-storage.ts
  - id: openwiki-source-af82e75c562cc0c1118650cf
    resource: repo://packages/control-plane/src/session/session-attachment-repository.ts
  - id: openwiki-source-467c6369bf6d18179b01cf60
    resource: repo://packages/control-plane/src/session/session-status-service.ts
  - id: openwiki-source-b91d1e3d9acc6b2a85763842
    resource: repo://packages/control-plane/src/session/sql-storage.ts
  - id: openwiki-source-c59860430ed84589a717b55a
    resource: repo://packages/control-plane/src/session/types.ts
  - id: openwiki-source-c6bd7c608dc8472f57a64deb
    resource: repo://packages/control-plane/src/source-control/provider-from-env.ts
  - id: openwiki-source-62f0c3ec6051610d33bdebe9
    resource: repo://packages/control-plane/src/storage/object-storage.ts
  - id: openwiki-source-968c4bcc9fbda1c7281a4e02
    resource: repo://packages/control-plane/src/types.ts
  - id: openwiki-source-2dd23e2f739aa003e42b413e
    resource: repo://packages/control-plane/test/integration/apply-migrations.ts
  - id: openwiki-source-a1aafae6c2c67594dd82b5c1
    resource: repo://packages/control-plane/vitest.integration.config.ts
  - id: openwiki-source-f1227299a054c9ff35745daa
    resource: repo://scripts/d1-migrate.sh
  - id: openwiki-source-136fa7eac6cf0379e2943038
    resource: repo://terraform/d1/migrations/0001_create_repo_secrets.sql
  - id: openwiki-source-e9fa301d4d038e3a194c2f37
    resource: repo://terraform/d1/migrations/0002_create_session_index.sql
  - id: openwiki-source-47faa7109d4e4c4bca451bd7
    resource: repo://terraform/d1/migrations/0004_create_global_secrets.sql
  - id: openwiki-source-b05965c92465fec483269e3d
    resource: repo://terraform/d1/migrations/0007_create_integration_settings.sql
  - id: openwiki-source-f65a2931d79af20cf5e8da05
    resource: repo://terraform/d1/migrations/0010_add_image_build_enabled.sql
  - id: openwiki-source-a756ba0a52e5175ee91be8cc
    resource: repo://terraform/d1/migrations/0013_create_automations.sql
  - id: openwiki-source-f695adb35451f63c35ccfbca
    resource: repo://terraform/d1/migrations/0019_create_users.sql
  - id: openwiki-source-24773cf735d54eecf2a2737b
    resource: repo://terraform/d1/migrations/0026_slack_triggers.sql
  - id: openwiki-source-f8dd01467907e9b2c90dc26a
    resource: repo://terraform/d1/migrations/0030_automation_repositories_and_invocations.sql
  - id: openwiki-source-6c0baf7913663ef2048830a1
    resource: repo://terraform/d1/migrations/0031_drop_deprecated_automation_columns.sql
  - id: openwiki-source-33e9fece98e946e56013b61e
    resource: repo://terraform/d1/migrations/0032_session_repositories.sql
  - id: openwiki-source-795117f7067e2fd3a97e0a0d
    resource: repo://terraform/d1/migrations/0033_environments.sql
  - id: openwiki-source-a4e22338b4633eb516a8855a
    resource: repo://terraform/d1/migrations/0036_repo_metadata_default_environment.sql
  - id: openwiki-source-c7deb7b2e7ca16c57762769d
    resource: repo://terraform/d1/migrations/0037_automation_environments.sql
  - id: openwiki-source-5ae6709e20489f9d508fafca
    resource: repo://terraform/d1/migrations/0038_integration_environment_settings.sql
  - id: openwiki-source-25d1f6182e3c51a7058f7d8d
    resource: repo://terraform/d1/migrations/0039_image_builds.sql
  - id: openwiki-source-7e836e7ab8178718b90d8b91
    resource: repo://terraform/d1/migrations/0040_drop_repo_images.sql
  - id: openwiki-source-55652c1f57459c9cd4f316cb
    resource: repo://terraform/d1/migrations/0041_session_pull_requests.sql
  - id: openwiki-source-724817d6cb72d63f7779fc5e
    resource: repo://terraform/d1/migrations/0044_api_tokens.sql
  - id: openwiki-source-91b091ea76913ca3a33ac573
    resource: repo://terraform/d1/migrations/0050_purge_retired_api_tokens.sql
  - id: openwiki-source-8b0374b8b991000da0bcdbe7
    resource: repo://terraform/d1/migrations/0051_drop_retired_custom_auth.sql
  - id: openwiki-source-c2f7cde8bffc2a82a89da21d
    resource: repo://terraform/d1/migrations/0052_image_build_finalization.sql
  - id: openwiki-source-6691180e0788381769c82b6d
    resource: repo://terraform/d1/migrations/0053_image_build_scheduler.sql
  - id: openwiki-source-63ec24a22b566c511ff53522
    resource: repo://terraform/d1/migrations/0055_session_read_states.sql
  - id: openwiki-source-c58c7e5adcc95223237e1b55
    resource: repo://terraform/d1/migrations/0057_reconcile_canonical_auth_identities.sql
  - id: openwiki-source-237dd145dd401b91e10520ab
    resource: repo://terraform/d1/migrations/0058_child_admission_leases.sql
  - id: openwiki-source-eb6023d53414123c21b19978
    resource: repo://terraform/d1/migrations/0059_require_automation_run_invocation.sql
  - id: openwiki-source-cd1a3e41b410fe02bf76abab
    resource: repo://terraform/d1/migrations/0061_managed_skills.sql
  - id: openwiki-source-7ed57c3ea61d0362377d3e7d
    resource: repo://terraform/d1/migrations/0063_session_root.sql
  - id: openwiki-source-eed9efded4c4e19279a84fcd
    resource: repo://terraform/d1/migrations/0064_provider_accounts.sql
  - id: openwiki-source-a834ce98b103886b7f1d4bde
    resource: repo://terraform/d1/migrations/0069_skill_import_sources.sql
  - id: openwiki-source-fa87429012f5a8428ace3e8f
    resource: repo://terraform/d1/migrations/0070_pr_autofix_feedback.sql
  - id: openwiki-source-ca32cc2da6748302c6ab7063
    resource: repo://terraform/environments/production/d1.tf
  - id: openwiki-source-42225b11439fd58ca2e91457
    resource: repo://terraform/environments/production/kv.tf
  - id: openwiki-source-add79d7310433a0f313a6fbc
    resource: repo://terraform/environments/production/r2.tf
  - id: openwiki-source-4167b211967d9a75eed01b74
    resource: repo://terraform/environments/production/workers-control-plane.tf
generated: { by: "openwiki/0.4.3", at: "2026-08-29T06:58:43.189Z" }
---

# Data Model & Storage

Open-Inspect splits durable state across two SQLite databases with deliberately different shapes:

1. **Shared D1** (`env.DB`) — the cross-session system of record: the sessions index, repository metadata, environments, image builds, automations, encrypted secrets, managed skills, users and identities, and model-provider accounts. Anything more than one session needs to query, list, or fan out over lives here.
2. **Per-session Durable Object SQLite** (`ctx.storage.sql`) — the hot session state: participants, the prompt queue, the agent event log, artifacts, sandbox credentials, and per-repository git state. Each `SessionDO` owns exactly one embedded database, giving high performance without cross-session contention.

The two are kept intentionally different in kind: D1 is accessed through the async `SqlDatabase` port (`src/db/sql-database.ts`), while the DO's `SqlStorage` is a synchronous, load-bearing engine that the port deliberately does not cover (`src/session/sql-storage.ts`). Two non-SQL bindings complete the picture: R2 (`MEDIA_BUCKET`) holds every session binary, and KV (`REPOS_CACHE`) caches repository listings and GitHub App installation tokens (see [Object storage and caches](#object-storage-and-caches)). Readers wanting the runtime behavior built on top of these tables should see [Control Plane Worker](/openwiki/architecture/control-plane-worker.md) and [Session Durable Object](/openwiki/architecture/session-durable-object.md).

## The two storage layers

```mermaid
erDiagram
    Session ||--o{ SessionRepository : "ordered members position 0 is primary"
    Session }o..o| Environment : "launch provenance environment_id may dangle"
    Environment ||--o{ ImageBuild : "environment-scope prebuilds"
    Automation ||--o{ AutomationInvocation : "one row per firing"
    AutomationInvocation ||--o{ AutomationRun : "one run per target"
    AutomationRun }o..|| Session : "session linkage automation_run_id"
    Session }o..o{ Skill : "pins manifest revisions"
    Environment }o..o{ Automation : "live target selection"

    Session {
        string id PK
        string status
        string repo_owner
        string repo_name
        string environment_id
        string root_session_id
        number total_cost
    }
    SessionRepository {
        int position
        string repo_owner
        string repo_name
        string base_branch
    }
    Environment {
        string id PK
        string name
        int prebuild_enabled
    }
    Automation {
        string id PK
        string trigger_type
        int enabled
        number next_run_at
    }
    AutomationInvocation {
        string id PK
        string trigger_key
        string concurrency_key
        string skip_reason
    }
    AutomationRun {
        string id PK
        string invocation_id FK
        string session_id FK
        string repo_owner
        string environment_id
    }
    ImageBuild {
        string id PK
        string scope_kind
        string scope_id
        string status
        string repositories_fingerprint
        string runtime_version
    }
    Skill {
        string id PK
        string name
        string current_revision_id
        int enabled
    }
```

Core shared-state entities in D1 and their relationships. Per-session hot state (participants, messages, events, artifacts, sandbox) lives inside each Session Durable Object's embedded SQLite and is not shown.

All shared-state SQL flows through one engine-neutral port, `SqlDatabase` (`src/db/sql-database.ts`):

- `D1Database` satisfies the port structurally (compile-time-asserted in the same file); there is no runtime wrapper for the raw handle.
- `batch()` must execute its statements in order as a **single atomic transaction** — all effects or none, one consistent snapshot, positional results. Delete-then-insert replacement writes and multi-query analytics reads rely on this.
- `meta.changes` is a **required** result field: roughly 38 store call sites gate correctness on affected-row counts (CAS conflict detection, guarded lifecycle transitions, insert-vs-update detection). An engine that omits it would make successful writes report failure.

On top of the port sit per-aggregate stores in `src/db/` — `SessionIndexStore`, `EnvironmentStore`, `AutomationStore`, `ImageBuildStore`, `SkillStore`, `UserStore`, the secrets stores, and so on. Each takes a `SqlDatabase`, keeps snake_case rows in the database and camelCase types at the API boundary, and owns its table-specific SQL. The router injects `instrumentD1(env.DB, metrics)` (`src/db/instrumented-d1.ts`), a transparent wrapper that records per-query timing and rows read/written into the request's wide event; stores never know the difference.

Request metrics are part of the data path's observability contract: the wrapper records one `D1QueryRecord` per terminal call (wall-clock `query_ms` plus the engine-reported `d1_server_ms`, `rows_read`, `rows_written`) and one aggregated record per `batch()`. `RequestMetrics.summarize()` folds these into the wide event as `d1_query_count`, `d1_total_ms`, `d1_server_total_ms`, `d1_rows_read`, and `d1_rows_written`, alongside named spans captured via `metrics.time()`. Instrumented statements keep the original statement under an `ORIGINAL_STMT` symbol so `batch()` can unwrap them before submission — the raw engine can only execute statements it prepared itself (the same-origin contract the port documents). The WebSocket upgrade path builds its own instrumented handle in `src/index.ts`, so upgrade-time index reads are timed too.

### JSON-in-TEXT columns

Several columns deliberately embed JSON documents in TEXT, and each owning store supplies a reader for its document. The one shared helper, `parseJsonStringArray` (`src/db/json-columns.ts`), covers the string-array columns — `repo_metadata.aliases` / `channel_associations` / `keywords` and `environments.channel_associations`: `NULL`, empty, malformed JSON, non-array payloads, and arrays with non-string elements all read as `undefined`, so a corrupt value degrades to "unset" instead of failing the row or leaking junk through the `string[]` contract (writers `JSON.stringify` the arrays). Documents with richer shapes have store-specific codecs with differing strictness: `image_builds.repository_shas` (`parseRepositoryShasJson` returns null for malformed or schema-invalid payloads) and integration settings (zod-validated on both write and read, throwing `IntegrationSettingsValidationError` on malformed JSON) fail closed; `automations.trigger_config` is read with plain `JSON.parse` (a corrupt value surfaces as an ordinary row-parse error); MCP server `command`/`repo_scope`/env parsers degrade leniently (a malformed command falls back to `[raw]`, malformed env to `{}`); in the DO schema, `messages.attachments` is a JSON array column and `session.sandbox_settings` holds the resolved sandbox settings document.

### Which store owns which tables

Ownership is one-to-one: every shared table has exactly one store module that writes it, and stores are composed (never merged) when an operation spans aggregates.

| Store module (`src/db/`) | Tables it owns |
| --- | --- |
| `session-index.ts` — `SessionIndexStore` | `sessions`, `session_repositories`, `child_admission_leases`; also writes `session_model_provider_auth` and the pinned skill manifest inside its atomic create batch |
| `session-pull-request-store.ts` | `session_pull_requests` |
| `session-skills.ts` — `SessionSkillStore` | reads `session_skill_manifests` / `session_skill_revisions` (written only via the creation batch) |
| `session-inbox-store.ts`, `analytics-store.ts`, `pull-request-analytics-store.ts`, `session-read-state.ts` | read-only projections and shared SQL fragments over `sessions` / `session_pull_requests` / `session_read_states` |
| `environments.ts` — `EnvironmentStore`; `environment-secrets.ts` | `environments`, `environment_repositories`; `environment_secrets` |
| `repo-metadata.ts` — `RepoMetadataStore` | `repo_metadata` |
| `repo-secrets.ts` / `global-secrets.ts` / `environment-secrets.ts` (shared plumbing in `scoped-secrets.ts`) | `repo_secrets`, `global_secrets`, `environment_secrets` |
| `image-builds.ts` — `ImageBuildStore` + `image-build-finalization.ts` — `ImageBuildFinalizationStore` | `image_builds` |
| `automation-store.ts` — `AutomationStore` | `automations`, `automation_repositories`, `automation_environments`, `automation_invocations`, `automation_runs` |
| `slack-channel-store.ts` — `SlackChannelStore` | `automation_slack_channels` |
| `skills.ts` — `SkillStore`; `skill-profiles.ts` — `SkillProfileStore` | `skills`, `skill_revisions`, `skill_revision_files`, `skill_assignments`, `skill_import_sources`, `skills_catalog_state`; `skill_profiles`, `skill_profile_items` |
| `user-store.ts` — `UserStore` (+ `user-merge.ts`, `identity-claim-store.ts`) | `users`, `user_identities` |
| `user-scm-tokens.ts` — `UserScmTokenStore` | `user_scm_tokens` |
| `model-provider-accounts.ts`, `provider-account-credentials.ts`, `provider-account-defaults.ts`, `provider-account-authorizations.ts` (composed by `model-provider-account-atomic-writer.ts`) | `model_provider_accounts`, `model_provider_account_credentials`, `model_provider_account_defaults`, `model_provider_account_authorizations` |
| `integration-settings.ts` — `IntegrationSettingsStore` (`scm-settings.ts` reuses the same tables under the fixed `scm` key) | `integration_settings`, `integration_repo_settings`, `integration_environment_settings` |
| `mcp-servers.ts` / `commit-signing.ts` | `mcp_servers` / `commit_signing_configuration` |
| `model-preferences.ts`, `keyboard-shortcut-preferences.ts` | `model_preferences`, `keyboard_shortcut_preferences` |
| `pr-autofix-feedback-store.ts` | `pr_autofix_feedback` |
| `better-auth-adapter.ts` — `CanonicalSqlAdapter` | no tables of its own; maps Better Auth's models onto `users`, `user_identities`, `auth_sessions`, `auth_verifications` |

## Migrations

### D1 migrations (shared schema)

`terraform/d1/migrations/` is the schema source of truth: 69 numbered SQL files spanning `0001`–`0070` (the numbering skips 0046) define every table, index, trigger, and backfill that exists in production D1. They are named `NNNN_description.sql` with a numeric prefix that is the tracked version, and are applied by `scripts/d1-migrate.sh <database-name>`:

1. The script validates filenames first — a file without a leading numeric prefix cannot be tracked, and two files sharing a prefix would mean one is silently skipped forever, so both fail fast before anything runs.
2. It ensures a `_schema_migrations (version, name, applied_at)` ledger table, then reads the applied `(version, name)` pairs. A version already recorded under a *different* filename is an error (downstream installations may have used the same number), so renames are caught before apply.
3. Each pending migration is copied to a temp file, the ledger `INSERT` is appended, and the combined file is submitted in one `wrangler d1 execute --remote --file`. D1 executes the file atomically: a failed migration rolls back, and a lost client response is safe to retry because a committed migration always has its ledger row.

Terraform drives the script: `terraform/environments/production/d1.tf` declares a `null_resource` whose trigger is the sha256 over the sorted migration file set, so `terraform apply` re-runs `d1-migrate.sh` whenever any migration file changes (and skips it otherwise). D1 read replication is explicitly disabled for this database.

Integration tests apply the same migrations automatically. `vitest.integration.config.ts` loads the directory with `readD1Migrations()` into a `TEST_MIGRATIONS` binding, and the setup file `test/integration/apply-migrations.ts` calls `applyD1Migrations(env.DB, env.TEST_MIGRATIONS)` before any test runs. Every integration test therefore exercises the real schema, which is why **a new table needs both a migration file and store updates** — adding only the store would pass typecheck and fail at runtime against the migrated D1. Migration-authored backfills are written guarded (`NOT EXISTS` / `IS NULL`) so they are the idempotent roll-forward repair path, while the schema-changing statements in the same file are applied exactly once (SQLite has no `ADD COLUMN IF NOT EXISTS`; see the header of migration 0030).

A worked example of the migration discipline: migrations 0039/0040 replaced the per-kind `repo_images`/`environment_images` tables with the unified `image_builds` registry, copying environment rows verbatim (they already carried fingerprints) and dropping the old tables. Because the control-plane scheduler naturally rebuilds every enabled scope, no legacy backfill job was required. Another: migration 0063 added `sessions.root_session_id` with a cycle-safe recursive backfill that tolerates corrupt parent chains (UNION-bounded reachability, lexicographically-smallest cycle root) and a trigger that keeps inserts from old worker versions correct during the migration window.

### Durable Object migrations (per-session schema)

`src/session/schema.ts` owns the DO schema with a two-path design:

- `SCHEMA_SQL` gives fresh DOs the complete current schema.
- `MIGRATIONS` is an ordered list of numbered migrations tracked in a DO-local `_schema_migrations (id, applied_at)` table, applied to existing DOs on first touch.
- `initSchema(sql)` = `SCHEMA_SQL`, then `applyMigrations`, then the index set; `SessionDO.ensureInitialized()` calls it on every activation (first touch after construction or eviction), so an evicted-and-rehydrated DO is always at the current schema before its runtime graph is built.

The two paths are kept from diverging by sharing SQL constants: e.g. `SESSION_REPOSITORIES_TABLE_SQL` is used both inside `SCHEMA_SQL` and as migration 31, and a unit test asserts both fresh and migrated DOs get the table. `runMigration` swallows *only* "duplicate column"/"already exists" errors and rethrows everything else; string migrations are ALTER statements, while function migrations execute directly and must be written idempotently because a crash between execution and recording can re-run them. Index creation runs after migrations so indexes can safely reference columns legacy tables lack.

## Shared D1 schema

### Sessions index

`sessions` (migration 0002, extended by many later migrations) is the queryable session index, migrated from KV. One row per session carries: `id`, `title`, the **scalar primary-repository mirror** (`repo_owner`, `repo_name`, `base_branch`), `model`/`reasoning_effort`, `status`, lineage (`parent_session_id`, `spawn_source`, `spawn_depth`, and `root_session_id` — added in 0063 with a cycle-safe recursive backfill that tolerates corrupt parent chains), automation linkage (`automation_id`, `automation_run_id`), launch provenance (`environment_id`, FK-less so a deleted environment leaves a benignly dangling id), analytics mirrors (`scm_login`, `user_id`, `total_cost`, `active_duration_ms`, `message_count`, `pr_count`), and the all-or-none `latest_terminal_message_*` triple (0055, enforced by a `CHECK`). Partial indexes serve the hot lookups: status+updated, per-repo, per-user (with a non-automation variant, 0054), and root-lineage scans.

`session_repositories` (0032) holds the full ordered member set: one row per member with a `position` column — **position 0 is the primary**, which list-created sessions also mirror into the scalar columns. The table is `ON DELETE CASCADE` from `sessions`. Pre-feature sessions have no rows here; readers synthesize a one-entry list from the scalar columns.

`SessionIndexStore` (`src/db/session-index.ts`) is the store over these tables and enforces the schema's core invariants at the boundary:

- `create()` validates that `repoOwner`/`repoName` are present together (both lowercased and trimmed, blank pairs nulled), requires that a supplied `providerAuth` snapshot is complete across every subscription provider, and then writes the session row, the `session_repositories` rows, the pinned skill manifest, and the provider-auth rows in **one atomic `db.batch`**. A skipped insert (0 changes) throws rather than silently half-creating a session, because `initialize.ts` relies on D1 failures being caught before any sandbox spawns.
- The **row-0-mirrors-scalars** invariant is enforced at creation: `initializeSession` throws when `repositories[0]` does not match the scalar repository fields.
- `updateStatus` only applies monotonic `updated_at` values (`AND updated_at <= ?`), protecting the index from out-of-order async writes; `repairStatus` and `archiveOrphanedDraft` are the deliberately unguarded repair paths used by the abandoned-draft sweep.
- `acquireChildAdmissionLease` claims parent concurrency capacity in `child_admission_leases` (0058) with a 5-minute TTL, counting live children plus unexpired leases against `maxConcurrentChildren` in a single guarded statement.

The index is also the routing authority: the WebSocket upgrade path answers 404 for a session id absent from `sessions` before any Durable Object is resolved.

Two adjacent tables extend the index:

- `session_pull_requests` (0041) — D1 is the **queryable authority record** for PRs created by sessions; the DO's `pr` artifact is a live-view mirror. One row per PR keyed by the DO artifact id, with a `lifecycle_state IN ('open','closed','merged')` CHECK, a draft-only-while-open invariant, and a monotonic `provider_updated_at` guard on every upsert. Canonical identity is `(repository_external_id, pr_number)` with a legacy `(repo_owner, repo_name, pr_number)` fallback for rows that predate external-id capture.
- `session_read_states` (0055) — per-user last-read message per session, cascading from both `users` and `sessions`.

`repo_metadata` (0002) is the repository catalog: description, aliases, Slack channel associations, plus `image_build_enabled` (0010, the per-repo prebuild toggle) and `default_environment_id` (0036, the GitHub-bot workspace redirect).

### Environments and secrets

`environments` (0033) is a named, prebuildable repository set: `environment_repositories` stores the ordered members (1–10) with per-repository `base_branch`, and the environment name is unique case-insensitively (the stable `env_<id>` is what Slack/Linear targets reference). The cascade model is deliberate:

- `environment_repositories` and `environment_secrets` are owned children with `ON DELETE CASCADE` — deleting an environment reclaims them.
- `image_builds` rows for the scope are **superseded, not cascaded** (FK-less on purpose), so the cleanup reaper can still find and delete provider-side artifacts after the entity is gone. `EnvironmentStore.delete` composes the whole cascade — repository rows, secret rows, superseding `building`/`ready` builds, then the environment row — into one `db.batch`.
- `sessions.environment_id` is FK-less; sessions created from an environment keep their snapshot members and a dangling provenance id after the environment is deleted.

Secrets have three scopes with one shared store plumbing: `global_secrets` (0004), `repo_secrets` (0001, keyed by `(repo_id, key)`), and `environment_secrets` (0033, mirroring `repo_secrets` with the same caps). All three encrypt with the same `REPO_SECRETS_ENCRYPTION_KEY` and go through `src/db/scoped-secrets.ts` (see [Encryption at rest](#encryption-at-rest)); only the encryption bookkeeping and row codecs are shared — each store keeps its own SQL and API.

### Image builds

`image_builds` (0039, extended by 0052) is the **unified prebuilt-image registry** for both scope kinds: `scope_kind` is `repo` (`scope_id` = lowercase `owner/name`) or `environment` (`scope_id` = environment id). Rows carry the provider artifact id, the per-repository SHAs (`repository_shas`, a JSON `[{repoOwner, repoName, baseSha}]` document), the `repositories_fingerprint` used for spawn matching, the `runtime_version` (the `SANDBOX_VERSION` at build time, checked against the compatibility floor), the status machine `building | ready | failed | superseded`, callback-token state (hash only, expiry, `used_at`), Queue-finalization lease state (`completion_hash`, `finalization_lease_token`, `finalization_lease_expires_at`), and `provider_session_cleanup_pending` for provider-session reclamation. The table is FK-less so deletes supersede rather than cascade.

`ImageBuildStore` + `ImageBuildFinalizationStore` implement the state machine with conditional SQL:

- `registerBuild` is the authoritative concurrency-1 gate: a single `INSERT … SELECT … WHERE NOT EXISTS (a building row for the same scope+provider)` — atomic within one statement, so concurrent triggers cannot both insert. The provider session is then bound with `bindProviderSession`, which also sets `provider_session_cleanup_pending = 1`.
- Callback acceptance is a conditional `UPDATE` requiring the exact bound `provider_session_id`, a matching (hash-compared, unexpired, single-use) callback token, and `status = 'building'`. An exact duplicate is reported as a replay (safe republish after a lost HTTP response); any conflicting or stale callback is rejected without touching the row.
- Finalization happens off the request path: the accepted row is finalized by a Queue consumer that claims an exclusive lease (expired leases may be replaced), then transitions the row to `ready`/`superseded`/`failed` and performs idempotent provider-session cleanup. Sweep queries (constants like `MARK_STALE_IMAGE_BUILDS_SQL`, `PROVIDER_SESSION_CLEANUP_SQL`, `SUPERSEDED_IMAGES_SQL`) reap stale builds, timed-out `building` rows, superseded artifacts, and failed artifacts; migration 0053 adds the matching partial indexes.

At spawn time a session boots from its scope's ready image only if the image's fingerprint equals the fingerprint of **the session's own repository snapshot** (not the scope's current repositories) and its `runtime_version` passes `MIN_COMPATIBLE_RUNTIME_VERSION` — the floor fails closed on an unparseable version. Any miss falls back to the base image; sessions are never blocked on builds.

### Automations

The automation model separates configuration, firing, and per-target work (see [Automations](/openwiki/workflows/automations.md) for the workflow behavior):

- `automations` (0013, reshaped by 0029/0031) holds the trigger configuration — `trigger_type`, `schedule_cron`/`schedule_tz`, `event_type`, JSON `trigger_config` and encrypted `trigger_auth_data` — plus model/instructions, `enabled`, `next_run_at`, `consecutive_failures`, and soft delete (`deleted_at`). The scalar repository columns were dropped in 0031; the live selection lives only in the normalized tables.
- `automation_repositories` (0030) and `automation_environments` (0037) are the live target selection — 0–10 combined rows, unique per `(automation_id, repo_owner, repo_name)` and per `(automation_id, environment_id)`. Zero rows is a repo-less automation; behavior derives from the data.
- `automation_invocations` (0030) is one **thin row per firing**: `source` (`schedule` | `manual` | `event`, distinct from the configured trigger type), `scheduled_at` (the cron slot, required for schedule sources by CHECK), the firing-scoped `trigger_key` (event dedup — `UNIQUE (automation_id, trigger_key) WHERE trigger_key IS NOT NULL`), `concurrency_key` (the per-key overlap scope), `trigger_metadata`, `skip_reason` (present ⇔ a skipped, childless firing), and `failure_counted_at` — the compare-and-set anchor that makes auto-pause accounting exactly-once no matter how many callbacks race. A partial unique index on `(automation_id, scheduled_at)` for schedule sources dedups cron double-fires. **Status is never stored** on the invocation.
- `automation_runs` is the per-target, session-linked unit: one row per repository or environment per invocation, required by 0059 to have a `NOT NULL invocation_id` (table-rebuild migration that aborts on malformed rows rather than inventing data). Each run snapshots its target at firing time (`repo_owner/repo_name/repo_id/base_branch`, or `environment_id`), so history is self-contained: editing the selection can never rewrite the past, which is why there is no edit-while-active guard. Unique indexes enforce at most one run per repository and per environment per invocation.

Two mechanisms make firings safe:

- **Derived status.** `DERIVED_INVOCATION_STATUS_SQL` in `automation-store.ts` is the single SQL definition of invocation status, aggregated over child runs: no children → `skipped`; any active child → `starting` until one leaves `starting`, then `running`; all terminal → `completed` (no failures), `failed` (no completions), `partial_failed` (a mix), or `skipped` (all skipped). A TypeScript twin (`deriveInvocationStatus`) mirrors it and integration tests assert the two agree. Deriving rather than storing removes the whole "crash between last child completing and parent updating" wedge class.
- **Guarded batch insertion.** `insertInvocationGuarded` creates the invocation, its children, and the schedule advance in **one D1 batch** where every statement is self-guarded: the invocation insert is suppressed when an overlap predicate matches (automation-wide for schedule/manual, per `concurrency_key` for events); child inserts are no-ops when the invocation was suppressed; the schedule advance is a compare-and-set on the claimed cron slot (`WHERE next_run_at = ?` — deliberately not "any later value wins", which would let a loser skip a slot). A `UNIQUE` violation (cron double-fire, event dedup) rolls back the whole batch including the advance, and callers classify it via `isDuplicateKeyError`.

`automation_slack_channels` (0026) is the watched-channel join table that lets Slack event selection find candidate automations by channel instead of scanning and JSON-parsing every trigger config.

### Managed skills

Migration 0061 introduced the skills subsystem (operations in `src/db/skills.ts`):

- `skills` is the mutable catalog row (`enabled`, soft delete, `current_revision_id`); content is **immutable** in `skill_revisions` (`UNIQUE (skill_id, revision_number)`, content sha256, body, metadata) with files in `skill_revision_files`. Triggers abort any `current_revision_id` that does not belong to the skill.
- `skill_assignments` grants applicability with exactly one of `global` / `repository` / `environment` scope shapes (CHECK-enforced). Assignment writes — and environment renames that affect provenance — advance the `skills_catalog_state` singleton `generation`, which session skill resolution uses to retry consistent snapshots.
- `skill_import_sources` (0069) records per-revision import provenance (provider, repo, resolved ref, commit sha, source digest).
- `skill_profiles` / `skill_profile_items` are per-user named filters over the catalog. Profiles do **not** grant applicability — resolution intersects their ids with enabled skills assigned to the session target — and profile writes also bump the shared generation.
- `session_skill_manifests` + `session_skill_revisions` pin the immutable resolved set per session (selection mode, resolver version, manifest sha256, per-revision provenance). The manifest is written **atomically inside the session-creation batch** (`SessionIndexStore.create`) or copied verbatim from a parent session for agent-spawned children, so a session can never exist without a consistent pinned manifest.

### Integration settings

Integration settings resolve through three layers, lowest precedence first: `integration_settings` (0007, global defaults keyed by `integration_id`), `integration_repo_settings` (0007, per-repo overrides keyed by `(integration_id, repo)`), and `integration_environment_settings` (0038, the top layer keyed by `(integration_id, environment_id)`, `ON DELETE CASCADE` from environments). Only the session-scoped integrations (`sandbox`, `code-server`) accept the environment level; the store's `supportsEnvironmentSettings` enforces that allowlist. Settings are JSON, validated against per-integration zod schemas on both write and read.

### Users, identities, and auth tokens

- `users` + `user_identities` (0019, `src/db/user-store.ts`) is the canonical user model: one canonical record per person, with free-form `provider` identity links unique per `(provider, provider_user_id)` — no CHECK, so new auth/SCM providers need no migration. `sessions`, `automations`, and `user_scm_tokens` all gained a nullable `user_id` for attribution.
- `auth_sessions` and `auth_verifications` (0048, recreated on epoch-ms columns by 0057) hold Better Auth browser sessions and OAuth handshake state. Since the #1290 consolidation (0057) Better Auth's user model **is** `users` and its account model **is** `user_identities` — the OAuth credential columns (`access_token`, `refresh_token`, `id_token`) live there, written as ciphertext because the runtime enables `encryptOAuthTokens` (Better Auth encrypts with its own secret, `BROWSER_AUTH_SECRET`); the parallel `auth_users`/`auth_accounts` registry from 0048 was merged and dropped by 0057.
- The pre-Better-Auth browser-auth design is retired: `api_tokens` (0044, CP-issued opaque credentials with rotation families) had its rows purged by 0050, and 0051 dropped `api_tokens` together with 0047's `verified_email_claims` and `browser_auth_sessions` tables. They remain in migration history only; the live browser-session authority is Better Auth's `auth_sessions`.

### Model provider accounts

Migration 0064/0065 define subscription-provider account storage:

- `model_provider_accounts` — one row per linked account, `status IN ('active','disabled','reconnect_required')`, a unique external identity per provider among non-archived rows, and a `lifecycle_version` (0065) for optimistic concurrency.
- `model_provider_account_credentials` — the `encrypted_payload` plus a versioned credential schema and a strict `exchange_state` machine (`idle` ⇔ `in_flight` with owner and started-at, CHECK-enforced) so token exchanges are fenced.
- `model_provider_account_defaults` — one default account per provider, with a trigger that refuses to disable/archive an account while it is the default.
- `session_model_provider_auth` — the per-session immutable auth snapshot (`provider_account` | `api_key` | `legacy_scoped_oauth` modes, CHECK-constrained on whether an account id is present). Sessions remain pinned to their stored mode.
- `model_provider_account_authorizations` — the device-flow authorization state machine, heavily CHECK-constrained (operation shape, polling interval, state-to-column dependencies, terminal-state cleanup).

`src/db/model-provider-account-atomic-writer.ts` composes these stores so account + credential + default are written in **one `db.batch`**, and reconnects verify both the expected credential version and account state in the same batch — a failed exchange can never leave a half-connected account.

### Other shared tables

- `mcp_servers` (0018, `revision` added in 0062) — managed MCP server configs; env/header values are encrypted with `REPO_SECRETS_ENCRYPTION_KEY`.
- `commit_signing_configuration` (0043) — singleton commit-signing config with the encrypted private key.
- `model_preferences` (0006), `keyboard_shortcut_preferences` (0067) — deployment-wide and per-user preferences.
- `pr_autofix_feedback` (0070) — durable receipt/decision ledger for PR feedback Autofix (execution admission stays authoritative in the owning DO).
- `repo_secrets`/`global_secrets` covered above.

## Object storage and caches

### MEDIA_BUCKET: session binaries in R2

The R2 bucket bound as `MEDIA_BUCKET` (`Env.MEDIA_BUCKET`, provisioned by `terraform/environments/production/r2.tf` with the default name `open-inspect-media-<deployment_suffix>`) holds every session binary; no binary data is stored in either SQLite database. All access flows through the small engine-neutral `ObjectStorage` port in `src/storage/object-storage.ts` — `createMediaObjectStorage(env)` wraps the R2 binding, and GET routes stream responses with Range support. Two key conventions:

- **Agent media artifacts** — screenshots and videos uploaded by the sandbox via `POST /sessions/:id/media`, keyed `sessions/<sessionId>/media/<artifactId>.<extension>` (`buildMediaObjectKey` in `src/media.ts`), with per-session upload caps enforced before the put (100 screenshots / 10 MB each, 20 videos / 100 MB each). If the DO then refuses to persist the artifact record, `persistMediaArtifact` deletes the uploaded R2 object rather than leaking an orphan.
- **Chat-composer attachments** — user-uploaded images via `POST /sessions/:id/attachments`, keyed `sessions/<sessionId>/attachments/<attachmentId>` (`buildSessionAttachmentObjectKey`), referenced by prompts as `{ attachmentId, name }` so the message row and `user_message` event stay small — DO SQLite rows cap at 2 MB, so base64 payloads must never ride through the message queue.

Attachment upload and garbage collection are a three-party protocol between the route, the DO's `attachments` table, and R2:

```mermaid
flowchart TD
    A["POST /sessions/:id/attachments"] --> B["Session DO: register attachment record"]
    B -- "stale unreferenced rows claimed" --> C["Route deletes their R2 objects"]
    C --> D["Route reports acks and failures to the DO"]
    D --> B
    B -- "ok" --> E["R2 put sessions/:id/attachments/:attachmentId"]
    E --> F["Prompt references the attachment by id and name"]
    F --> G["Message row and attachment claims commit atomically in the DO"]
    G -- "never claimed within 24 h TTL" --> H["Next registration sweep claims the row"]
    H --> C
```

Attachment upload and garbage-collection protocol between the router, the session Durable Object's `attachments` table, and R2.

The route **registers the record in the DO before the R2 put**, so every failed or ambiguous storage outcome remains discoverable through the normal stale-attachment cleanup protocol (retries up to three times when the DO asks for a cleanup pass first). The DO enforces per-session quotas (100 files, 500 MB total) and each registration sweeps attachment records never referenced by a message within the 24-hour TTL (cleanup claims expire after 5 minutes), returning their object keys for the route to delete from R2. A prompt claims the attachment rows **atomically with message creation** — a partial claim (an attachment already claimed or missing) aborts the whole message with `AttachmentClaimConflictError`.

### REPOS_CACHE: one KV namespace, two tenants

The single KV namespace bound as `REPOS_CACHE` (Terraform binds it to the session-index namespace, `open-inspect-session-index-<suffix>`) is cache-only — the worst case is a cold cache — and serves two independent consumers:

- **`/repos` listing cache** (`src/routes/repos.ts`): one key, `repos:list:v2`, holding the SCM-enriched repository list. Fresh entries (under 5 minutes old) are served directly; stale entries (5 minutes–1 hour, the KV TTL) are served immediately while a background refresh revalidates; a cold cache fetches synchronously and registers the refresh with `waitUntil` so an aborting caller cannot leave the cache permanently empty. Any repository-metadata write deletes the key.
- **GitHub App installation-token cache, persistent tier** (`src/auth/github-app.ts`): keys `github:installation-token:v1:<appId>:<installationId>` hold cached installation tokens behind an in-isolate memory map and a single-flight refresh promise. Cached tokens are usable for at most 50 minutes and only with ≥5 minutes of remaining lifetime; the KV TTL is capped at 1 hour. The same provider (and hence the same cache tier) is constructed both in the route composition root (`provider-from-env.ts`) and by the Autofix queue consumer.

## Per-session Durable Object SQLite schema

`SCHEMA_SQL` (`src/session/schema.ts`) defines the hot-state tables created inside every session DO:

| Table | Contents |
| --- | --- |
| `session` | Core state: external name, title, scalar primary repo mirror, per-session model/reasoning default, `status` (`created`, `active`, `completed`, `failed`, `archived`, `cancelled`), lineage (`parent_session_id`, `spawn_source`, `spawn_depth`), `code_server_enabled`/`vnc_enabled`, `total_cost`, resolved `sandbox_settings` JSON, `environment_id` provenance, `opencode_session_id`. A CHECK requires `repo_owner`/`repo_name` to be null together and no-repo sessions to lack branch context. |
| `participants` | Session members with SCM identity for git attribution, **AES-GCM-encrypted** `scm_access_token_encrypted` / `scm_refresh_token_encrypted`, and `ws_auth_token` stored as a SHA-256 hash. |
| `messages` | Prompt queue and history: `status` (`pending`/`processing`/`completed`/`failed`), per-message model/reasoning overrides, Slack callback context, web idempotency (`client_request_id` unique partial index), Autofix dedup keys, `stop_confirmation_deadline`. A unique partial index enforces **at most one `processing` message per session**. |
| `events` | Append-only agent event log (tool calls, tokens, errors) with a `UNIQUE timeline_sequence` for stable ordering and cursoring. |
| `artifacts` | PRs, screenshots, videos, preview URLs; `updated_at` tracks PR lifecycle content changes. |
| `attachments` | Chat composer attachments in the media bucket; `message_id` links once referenced, unreferenced rows are TTL-pruned together with their R2 objects. |
| `sandbox` | The selected backend sandbox: provider ids, snapshot ids, `runtime_version` and `snapshot_runtime_version` (the restore compatibility floor), sandbox auth token (hash preferred), the status machine (`pending` → `spawning` → `connecting` → `warming` → `ready`, plus `stale`/`snapshotting`/`stopped`/`failed`), `git_sync_status`, heartbeats, and a spawn-failure circuit breaker (`spawn_failure_count`). Code-server/VNC tunnel URLs and passwords rotate on wake/restore. |
| `session_repositories` | Multi-repo members **including per-repo git state** (`branch_name`, `base_sha`, `current_sha`) — the identity subset is mirrored to D1; git state is DO-only. The same SQL constant feeds both this table and migration 31. |
| `session_diff` | Singleton holding the latest durable checkout diff bundle (the only home of source patches). |
| `session_alarm_state` | Singleton persisting alarm scheduling state so a DO that can be adopted by another host can recover its alarms. |
| `ws_client_mapping` | WebSocket id → participant mapping, letting hibernation-recovered connections be re-authenticated. |

The DO schema is a superset of what D1 stores for a session: D1's `session_repositories` deliberately carries no git state ("D1 doesn't store it"), and the event log, prompt queue, and sandbox credentials never leave the DO.

### Keeping D1 and the DO consistent

The DO is the live authority; D1 is a projection of selected facts. `SessionStatusService` is the single place every status transition fans out — connected clients, the D1 index (status plus terminal metrics), and the parent DO (child rollup) — and the D1 writes are monotonicity-guarded as described above. The abandoned-draft sweep repairs drafts whose index row drifted (D1 says `created`, the DO disagrees or does not exist). Session creation writes D1 **first** so a failed index write is caught before any sandbox is spawned, then initializes the DO.

## Encryption at rest

Stored credentials are protected by independent key domains; rotation guidance differs per key and they must never be treated as interchangeable during an incident.

- **`TOKEN_ENCRYPTION_KEY`** — AES-256-GCM via `encryptToken`/`decryptToken` in `src/auth/crypto.ts` (256-bit key, 96-bit random IV, base64 `IV || ciphertext`). It protects the SCM enrichment tokens: participant tokens in the DO `participants` table and the `user_scm_tokens` refresh-token store in D1. Rotating it invalidates stored SCM tokens; affected users re-link.
- **`REPO_SECRETS_ENCRYPTION_KEY`** — the single key for all three secret scopes (`global_secrets`, `repo_secrets`, `environment_secrets`), and also for other stored control-plane secrets: automation `trigger_auth_data` (e.g. encrypted Sentry client secrets), MCP server env/header values, and the commit-signing private key. `src/db/scoped-secrets.ts` owns the scope-independent plumbing: `prepareSecretsForWrite` (normalize, validate, enforce the per-scope total-value cap, reject two raw keys that normalize to one), `assertScopeKeyCapacity` (the 50-keys-per-scope cap), `encryptSecretEntries`, and `decryptSecretRows` (parallel decrypt; `SecretDecryptionError` carries the failing key, never plaintext or ciphertext).
- **`BROWSER_AUTH_SECRET`** — Better Auth's secret; signs browser session cookies **and** encrypts the OAuth credential columns on `user_identities` (via `encryptOAuthTokens: true` in the Better Auth configuration). Rotating it signs every browser session out and orphans those stored credentials (they re-populate at next sign-in); it does not affect `user_scm_tokens`.
- **`PROVIDER_ACCOUNTS_ENCRYPTION_KEY`** — dedicated AES-256-GCM key for subscription-provider credentials, implemented in `src/auth/provider-account-crypto.ts` with a versioned envelope (`v1.<iv>.<ciphertext>`) and **additional authenticated data binding** the provider account id, provider, and credential schema version — ciphertexts cannot be replayed under a different account context. Rotation requires an explicit migration that re-encrypts every credential while both keys are available; reconnecting accounts does not migrate already-encrypted rows.

`src/db/secrets-validation.ts` enforces the key rules that gate every secret write: keys must match `[A-Za-z_][A-Za-z0-9_]*`, max 256 characters, from a reserved-key blocklist (system env vars like `SANDBOX_AUTH_TOKEN`, `PATH`, `GITHUB_APP_TOKEN`); values max 16 KB; max 50 keys and 64 KB total per scope. `mergeSecretSources` folds the ordered per-session sources (global, then repositories/environments) lowest-precedence-first — the primary repository is passed last so it wins collisions — with per-source byte attribution, and rejects a merged payload over the 128 KiB session cap.

Key validation is fail-closed: `src/env-validation.ts` requires each encryption key to be present, strict base64, and exactly 32 bytes (so a malformed key fails at startup rather than mid-spawn, and a short key cannot silently downgrade to AES-128/192).

## Operating the schema

- **New D1 table/column:** add a `NNNN_description.sql` file to `terraform/d1/migrations/`, add or extend the store in `src/db/`, and rely on the integration tests to exercise the real migrated schema. Backfills must be guarded to stay idempotent; the deploy path re-runs automatically via Terraform's content-hash trigger.
- **New DO column/table:** add it to `SCHEMA_SQL` (fresh DOs) *and* append a numbered entry to `MIGRATIONS` (existing DOs), sharing a SQL constant when the shape must match exactly.
- **Object storage and caches:** R2 and KV carry no migrations. R2 object keys are code conventions (`buildMediaObjectKey`, `buildSessionAttachmentObjectKey`), and `REPOS_CACHE` is cache-only — a shape change at worst costs a cold cache.
- **Manual apply / inspection:** `scripts/d1-migrate.sh <database-name>` applies pending migrations and prints per-file skip/apply status; the `_schema_migrations` ledger in each database is the source of truth for what has run.
