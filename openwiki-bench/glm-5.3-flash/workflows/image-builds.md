---
type: workflow
title: Image Builds Workflow
description: Prebuilt-image lifecycle for repositories and environments — build scopes and fingerprints, the 30-minute scheduler cron, dormant provider build sessions, single-use callback authentication, queue-based finalization with leases, failure handling (reaper, stale builds, timeouts), and image selection at spawn.
tags: [image-builds, prebuilds, fingerprints, scheduler, modal, vercel, opencomputer, callbacks, queues, finalization]
verified:
  - by: openwiki/0.4.3
    at: 2026-08-29T06:58:43.189Z
sources:
  - id: openwiki-source-72f328299068ced4bc03ba25
    resource: repo://docs/IMAGE_PREBUILD.md
  - id: openwiki-source-ae36133dde52a5b02b299597
    resource: repo://packages/control-plane/src/image-builds/callback-auth.ts
  - id: openwiki-source-52d2bdb7a0155b3c5702be9b
    resource: repo://packages/control-plane/src/image-builds/concurrency.ts
  - id: openwiki-source-bb4971132a91d2f43607be8b
    resource: repo://packages/control-plane/src/image-builds/finalization-consumer.ts
  - id: openwiki-source-673ec691c62d90c8d2f67ec2
    resource: repo://packages/control-plane/src/image-builds/finalizer.ts
  - id: openwiki-source-17cca56c03251aa00ffdfb64
    resource: repo://packages/control-plane/src/image-builds/fingerprint.ts
  - id: openwiki-source-2791bf357719e763cd0c9de2
    resource: repo://packages/control-plane/src/image-builds/lookup.ts
  - id: openwiki-source-57015c95a31da54d996279ed
    resource: repo://packages/control-plane/src/image-builds/maintenance.ts
  - id: openwiki-source-94054330f8ea4049a11657d1
    resource: repo://packages/control-plane/src/image-builds/model.ts
  - id: openwiki-source-f2b4f69e7d61b7b238f26d81
    resource: repo://packages/control-plane/src/image-builds/planner.ts
  - id: openwiki-source-0ae1cb16940e7c4e59a955ea
    resource: repo://packages/control-plane/src/image-builds/reaper.ts
  - id: openwiki-source-da40f2cb04518f6ceffd5c8c
    resource: repo://packages/control-plane/src/image-builds/rebuild-policy.ts
  - id: openwiki-source-c17bbe00abbcf37cbd7991f3
    resource: repo://packages/control-plane/src/image-builds/scheduler.ts
  - id: openwiki-source-e4551796343e091f25083955
    resource: repo://packages/control-plane/src/image-builds/scope.ts
  - id: openwiki-source-78d79c9c9f6ae2582d131b7d
    resource: repo://packages/control-plane/src/image-builds/timeouts.ts
  - id: openwiki-source-bcc80e7ca506a1801f270b8d
    resource: repo://packages/control-plane/src/image-builds/vercel-adapter.ts
  - id: openwiki-source-d67978cdb180862cca3ffb24
    resource: repo://packages/control-plane/src/image-builds/workflow.ts
  - id: openwiki-source-66587463738be0ef3850ffd6
    resource: repo://packages/control-plane/src/routes/image-builds.ts
  - id: openwiki-source-12eddf76bf761158a1fb4559
    resource: repo://packages/control-plane/src/sandbox/lifecycle/image-selection.ts
  - id: openwiki-source-416a2efbd05cc2aaf16d47c6
    resource: repo://packages/control-plane/src/sandbox/lifecycle/manager.ts
  - id: openwiki-source-ca15e4453ec332452279c0d4
    resource: repo://packages/modal-infra/src/sandbox/build_session.py
  - id: openwiki-source-fb68899d3462859b54764231
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/repo_image_callback.py
  - id: openwiki-source-b185b5840284429fac0e62d7
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/runtime_config.py
  - id: openwiki-source-1c00ceaa09c78a88ce71092a
    resource: repo://packages/shared/src/types/image-builds.ts
generated: { by: "opencode", at: "2026-08-29T06:58:43.189Z" }
---

# Image Builds Workflow

Pre-built images front-load the expensive part of a session boot — cloning every repository and
running its `.openinspect/setup.sh` — into a background artifact that new sessions restore from,
pulling only recent commits. The user-facing model is documented in `docs/IMAGE_PREBUILD.md`; this
page traces the implementation. One provider-neutral subsystem serves both **build scopes**:

- **repo** — a single repository built on its default branch (`repoImageBuildScope`), enabled per-repo
- **environment** — an ordered set of up to 10 repositories with per-repo base branches, enabled per-environment

Everything below is scope-generic; where the scopes differ it is called out. Prebuilds require an
artifact-capable sandbox provider: `modal`, `vercel`, or `opencomputer` (the artifact is a Modal
image, Vercel snapshot, or OpenComputer checkpoint respectively; E2B/Daytona use persistent
sandboxes or pause/resume and have no image settings).

## Identity: fingerprints and provenance

Every image row records:

- a **repositories fingerprint** — a hash of the ordered repository set and their base branches
  (`packages/control-plane/src/image-builds/fingerprint.ts`). A repo scope is the one-element set on
  the default branch, so the fingerprint check reproduces the old base-branch filter: a
  non-default-branch session computes a different fingerprint and misses.
- **provenance** — the commit SHA each repository was built at (`repository_shas`, position-ordered,
  primary first) and the **sandbox runtime version** the image was built on.

Spawn-time selection and the rebuild scheduler both key on this identity: editing a scope
(repositories, order, base branches) changes the fingerprint and automatically retires the old image.

## The build lifecycle, end to end

All trigger sources — the scheduler cron, save-hooks (enabling prebuilds / saving a prebuild-enabled
environment / rotating environment secrets), and manual rebuild buttons — converge on
`ImageBuildWorkflow.trigger*` (`packages/control-plane/src/image-builds/workflow.ts`), which enforces
the **per-scope concurrency-1 rule** in one place: an active build makes a trigger a no-op
(`already_building`), enforced by both a cheap `getActiveBuild` read and a `registerBuild`
NOT EXISTS insert guard (the read is not atomic with the insert, so a lost race reports the
winner's build).

1. **Register and mint the callback token.** Provider configuration is validated before any database
   work; a lazy wedge-recovery pass marks a scope build stale if its sandbox died without a callback
   (`failStaleScopeBuild`). The build row is registered **before** secrets are decrypted — the
   secret-change supersede can only see builds that have a row, and everything before registration
   stays cheap and secret-free. `ImageBuildPlanner.createCallbackAuth` generates a single-use
   64-hex-char callback token whose **HMAC hash only** is stored on the row (peppered with
   `IMAGE_CALLBACK_TOKEN_PEPPER` under the wire-frozen `repo-image-callback:` domain prefix, TTL 2
   hours — `callback-auth.ts`).
2. **Create a dormant provider session.** The planner resolves the target (repository snapshot,
   fingerprint, build-time secrets — the same set the scope's sessions get — and the scope's
   resolved build timeout) and the provider adapter starts the build. The adapter's
   `bindProviderSession` callback persists the provider session id onto the build row the moment it
   exists; a failure after binding runs `cleanupFailedBuild` and marks the row failed. Build ids are
   `imgb-<scope id with / flattened to ->-<timestamp>-<rand>` — greppable and safe as path segments.
3. **The build sandbox runs the setup.** On Modal, `ModalBuildSessionService.create`
   (`packages/modal-infra/src/sandbox/build_session.py`) launches a sandbox in build mode
   (`IMAGE_BUILD_MODE=true`) with `SESSION_CONFIG` carrying the ordered repository list, a one-shot
   `VCS_CLONE_TOKEN` (there is no control plane to broker credentials — see
   [Git Auth and Pull Requests](git-auth-and-pull-requests.md)), and the callback URLs as reserved
   env keys that are scrubbed from any user-supplied env. The sandbox clones every repository at its
   base branch (for environments, sequentially in position order), runs each `.openinspect/setup.sh`
   in order, and reports via `repo_image_callback.py` — a failing setup script fails the whole build,
   with the error naming the repository for environment scopes. Callbacks are POSTed to
   `POST /image-builds/build-complete` / `build-failed` with the bearer token and the **exact bound
   provider session id**, retried with backoff.
4. **Authenticate and durably accept before publishing.** The workflow authorizes the callback
   (token hash match, token bound to the exact provider session id, not expired — `authorizeCompletionCallback`),
   then persists the outcome through the store's `acceptSuccessfulCompletion` /
   `acceptFailedCompletion` (completion-hash fencing makes exact retries idempotent — a replayed
   acceptance re-publishes safely). Only after durable acceptance is a **secret-free** command
   published to the `IMAGE_BUILD_FINALIZATION_QUEUE`. Success publishes only while the row is still
   `building`; a rejected acceptance throws to the route's error taxonomy.
5. **Queue-based finalization.** `ImageBuildFinalizer.process`
   (`packages/control-plane/src/image-builds/finalizer.ts`) advances the accepted build:
   - the completion hash rejects stale commands; a non-`building` row just gets terminal cleanup;
   - a D1 **finalization lease** (6 minutes — longer than the 5-minute provider attempt deadline)
     serializes provider work across at-least-once deliveries; a lost lease retries after the
     active lease expires;
   - the adapter snapshots or checkpoints the provider session (hard 5-minute
     `IMAGE_BUILD_PROVIDER_ATTEMPT_MS` deadline). A timeout is **ambiguous** — the build is failed
     rather than retried, because creating the artifact twice is worse than failing. An error
     proven *not* to have created an artifact clears the lease and retries;
   - the provider artifact id is **fenced** into D1 (`recordArtifact`); a persistence failure
     re-reads D1 first (D1 is strongly consistent within the Worker) before compensating — an
     unfenced artifact is deleted, and if deletion also fails the artifact is **quarantined** on the
     row for maintenance to reap;
   - `tryMarkImageBuildReady` transitions the row to `ready` (or `superseded` when a newer image
     already exists), the reaper deletes the replaced images, and terminal cleanup (terminating the
     build session) runs idempotently.

### Statuses and the aggregate feed

`ImageBuildStatus` is `building | ready | failed | superseded`
(`packages/shared/src/types/image-builds.ts`). The `GET /image-builds/status` route
(`packages/control-plane/src/routes/image-builds.ts`) serves the public-safe aggregate feed used by
Settings and the new-session picker, optionally filtered by `scope_kind`/`scope_id`;
`GET /image-builds/enabled` and `/enabled-repos` expose enabled scopes with their current
fingerprints, and `POST /image-builds/trigger/...` plus `/toggle/repo/:owner/:name` are the
scope-generic trigger family.

## Scheduling: the 30-minute cron

`ImageBuildScheduler.run` (`packages/control-plane/src/image-builds/scheduler.ts`) fires on cron
`7,37 * * * *` and, per tick, best-effort runs four phases (each failure is logged and never
blocks the next):

1. **Republish recoverable finalizations** — accepted builds whose Queue command was lost get
   republished from D1.
2. **Stale sweep** — `markStaleBuildsAsFailed` fails rows older than
   `DEFAULT_STALE_BUILD_MAX_AGE_MS` (the maximum provider-session budget — 1-hour build execution +
   10-minute finalization grace — plus a 5-minute dispatch grace, from `timeouts.ts`/`maintenance.ts`).
3. **Provider-session cleanup** — every pending terminal build session is dispatched through
   `runMaintenanceTasks` (`concurrency.ts`), which bounds concurrent provider calls. Cleanup is
   dispatched from each row's *recorded* provider, independently of the currently active provider —
   which is why old provider credentials must outlive a provider switch until the cleanup backlog
   drains.
4. **Scope reconciliation** — for every enabled scope, `evaluateImageBuildRebuildPolicy`
   (`rebuild-policy.ts`) decides: skip while a build is in flight; rebuild when there is no
   current-fingerprint ready image, the ready image's runtime version is below
   `MIN_REBUILD_RUNTIME_VERSION` (the shared provider-neutral runtime generation floor), or its
   provenance is missing/incomplete; otherwise **check branches** — the scheduler reads each
   repository's live branch head from the SCM provider and rebuilds on any SHA drift (a missing
   branch head does not trigger).

The tick ends with the reaper: `ImageBuildReaper.cleanupImages` deletes aged-out failed rows
(`DEFAULT_ARTIFACT_CLEANUP_MAX_AGE_MS` = 24h) and reaps the provider artifacts of failed/superseded
rows best-effort.

### Timeouts

- The scope's resolved **build timeout** (default 30 minutes, hard max 1 hour; global defaults
  overridden by the primary repository's settings and — for environments — the environment's own
  overrides) covers clone + setup execution (`planner.ts`).
- The provider session gets the build budget **plus a 10-minute finalization grace**
  (`IMAGE_BUILD_FINALIZATION_GRACE_MS`) for callback delivery and snapshot/checkpoint
  (`resolveImageBuildProviderSessionTimeoutSeconds` in `timeouts.ts`).
- Vercel caps sandbox lifetime at 45 minutes, so Vercel image-build execution is capped at
  `VERCEL_MAX_SANDBOX_TIMEOUT_MS - IMAGE_BUILD_FINALIZATION_GRACE_MS` = 35 minutes
  (`vercel-adapter.ts`); Modal and OpenComputer honor the configured timeout up to the shared 1-hour
  limit.

## Image selection at spawn

`evaluateImageBuildForSpawn` (`packages/control-plane/src/sandbox/lifecycle/image-selection.ts`) is
pure decision logic run against the **session's own repository snapshot** — not the scope's current
repositories, so an entity edited after session creation can never hand the session a mismatched
image. Checks run cheapest-first: latest ready image on the active provider exists (enablement-gated
via `createImageBuildLookup` in `image-builds/lookup.ts`) → artifact id present → runtime version at
or above `MIN_COMPATIBLE_RUNTIME_VERSION` (fails closed on an unparseable version) → fingerprint
equals the session snapshot's fingerprint. Any miss falls back to the base image — sessions are
never blocked on builds.

The lifecycle manager (`packages/control-plane/src/sandbox/lifecycle/manager.ts`) applies the scope
rules: an environment session matches only its environment's image (it must never fall back to a
member repo's image, which bakes that repository's setup and secrets, not the environment's), a
single-repo ad-hoc session matches its repo scope, and multi-repo ad-hoc sessions never use
prebuilt images. A selected image passes `prebuiltImageId`/`prebuiltImageSha` (the primary repo's
baked SHA, informational) to the provider: the sandbox boots from the artifact, setup scripts are
**not** re-run, and a fast per-repo git sync pulls commits pushed since the build. If the provider
restore itself fails, the row is marked failed (`markRestoreFailed`) so the cron rebuilds it, the
spawn identity is rotated, and the session retries from base — a provider restore failure is "no
image", not a failed spawn.

The sandbox-side boot mode distinguishes `fresh`, `snapshot_restore`, `repo_image`, and `build`
boots from the environment (`BootMode.from_env` in `packages/sandbox-runtime/src/sandbox_runtime/runtime_config.py`);
build-mode sandboxes run the image-build entrypoint and the callback reporter, and their boot
skips the setup that the image already baked in.

## Failure handling summary

- **Reaper** (`reaper.ts`) — deletes aged failed rows and reaps failed/superseded artifacts on the
  scheduler tick.
- **Stale builds** — rows with no callback within the stale budget are marked failed (cron sweep,
  plus the lazy per-scope check at trigger time) so the concurrency-1 guard can never wedge.
- **Ambiguity discipline** — during finalization, only a *provably* uncreated artifact is retried;
  ambiguous outcomes (timeout, fenced-write limbo) fail the build and clean up, and a
  quarantine path covers artifacts that could neither be fenced nor deleted.
- **Idempotency everywhere** — completion-hash fencing, lease tokens, NOT EXISTS registration,
  and terminal cleanup that is safe to re-run.
