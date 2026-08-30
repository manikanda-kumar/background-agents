---
type: integration
title: Source Control Providers
description: The SourceControlProvider abstraction and its GitHub/GitLab implementations — user vs app authentication tiers, the SCM_PROVIDER factory and deployment-level gate, push-spec and credential-helper auth generation, nested owner namespace handling, and the ADR-0001 boundaries for adding a provider.
tags: [source-control, github, gitlab, scm-provider, factory, scm-credentials, credential-helper, adr, extension-points, control-plane]
verified:
  - by: openwiki/0.4.3
    at: 2026-08-29T06:58:43.189Z
sources:
  - id: openwiki-source-c96bf7d49c00c4349dd4d97a
    resource: repo://docs/adr/0001-single-provider-scm-boundaries.md
  - id: openwiki-source-98a83eb7d07ff75d2880660c
    resource: repo://docs/provider-contribution-checklist.md
  - id: openwiki-source-333fb60a4bff5e1935e75098
    resource: repo://packages/control-plane/README.md
  - id: openwiki-source-e1eb7b9ad7610b4c3c628cec
    resource: repo://packages/control-plane/src/auth/github-app.ts
  - id: openwiki-source-e4551796343e091f25083955
    resource: repo://packages/control-plane/src/image-builds/scope.ts
  - id: openwiki-source-57571062f6cb1fdb094e8d3c
    resource: repo://packages/control-plane/src/router.policy.test.ts
  - id: openwiki-source-9175ccc37c339f0a3dfd984e
    resource: repo://packages/control-plane/src/router.ts
  - id: openwiki-source-2f03a3c121b1d902de28dc9a
    resource: repo://packages/control-plane/src/routes/commit-signing.ts
  - id: openwiki-source-4541aa742c69d8bf1c2769c4
    resource: repo://packages/control-plane/src/routes/repos.ts
  - id: openwiki-source-9246d18e19b8882678924a05
    resource: repo://packages/control-plane/src/routes/session-create.ts
  - id: openwiki-source-5fd49082b7e464556f638e18
    resource: repo://packages/control-plane/src/routes/session-prompt.ts
  - id: openwiki-source-d663b98c0e2ac16c96c1234f
    resource: repo://packages/control-plane/src/routes/shared.test.ts
  - id: openwiki-source-f49c3ab49317a2ad215805d3
    resource: repo://packages/control-plane/src/routes/shared.ts
  - id: openwiki-source-d6e98a6245025eabc1bd155f
    resource: repo://packages/control-plane/src/sandbox/sandbox-env.test.ts
  - id: openwiki-source-b4fcd225b1d428a7ab560a2d
    resource: repo://packages/control-plane/src/sandbox/sandbox-env.ts
  - id: openwiki-source-f69048d2562235a60f688786
    resource: repo://packages/control-plane/src/session/components.ts
  - id: openwiki-source-c4154a235be14d15e37bb81f
    resource: repo://packages/control-plane/src/session/identity.ts
  - id: openwiki-source-19be9138d087c147d650a925
    resource: repo://packages/control-plane/src/session/participant-service.ts
  - id: openwiki-source-9d15c964265e29af445b4964
    resource: repo://packages/control-plane/src/session/pull-request-service.test.ts
  - id: openwiki-source-95708266900b62a9460b893c
    resource: repo://packages/control-plane/src/session/pull-request-service.ts
  - id: openwiki-source-9d26cd0db559f86b8ba619ea
    resource: repo://packages/control-plane/src/session/repo-id-resolution.ts
  - id: openwiki-source-467e7dc8d2e7142d515a44b8
    resource: repo://packages/control-plane/src/session/sandbox-push-service.ts
  - id: openwiki-source-cc418ae85071e90eb8d81e29
    resource: repo://packages/control-plane/src/session/scm-credentials-service.ts
  - id: openwiki-source-f2c98b1a45d6d9b9b91afc24
    resource: repo://packages/control-plane/src/source-control/config.test.ts
  - id: openwiki-source-c1cc0e9304c0e13ec9a4cccc
    resource: repo://packages/control-plane/src/source-control/config.ts
  - id: openwiki-source-7a978e290674a5c91306a063
    resource: repo://packages/control-plane/src/source-control/errors.ts
  - id: openwiki-source-34b4b149a59a5ef78234ec91
    resource: repo://packages/control-plane/src/source-control/github-credential-authority.test.ts
  - id: openwiki-source-3f4f485b32cd4a751f5b34f7
    resource: repo://packages/control-plane/src/source-control/github-credential-authority.ts
  - id: openwiki-source-ab4580568e347fd89ffba5a1
    resource: repo://packages/control-plane/src/source-control/provider-from-env.test.ts
  - id: openwiki-source-c6bd7c608dc8472f57a64deb
    resource: repo://packages/control-plane/src/source-control/provider-from-env.ts
  - id: openwiki-source-d0098aaa38e98b9ca2614856
    resource: repo://packages/control-plane/src/source-control/providers/git-tree.ts
  - id: openwiki-source-a52db84aacaf86aeea891972
    resource: repo://packages/control-plane/src/source-control/providers/github-provider.test.ts
  - id: openwiki-source-98be2b2c31c1e4ddbe0f097d
    resource: repo://packages/control-plane/src/source-control/providers/github-provider.ts
  - id: openwiki-source-0b3fbc604653de11ed57e62e
    resource: repo://packages/control-plane/src/source-control/providers/gitlab-provider.test.ts
  - id: openwiki-source-49a4590261f3c9b1a968a52b
    resource: repo://packages/control-plane/src/source-control/providers/gitlab-provider.ts
  - id: openwiki-source-e25d0707f2db213ea1279259
    resource: repo://packages/control-plane/src/source-control/providers/index.test.ts
  - id: openwiki-source-23c9c11e565ab89c515bb2a6
    resource: repo://packages/control-plane/src/source-control/providers/index.ts
  - id: openwiki-source-ffa9b176035b718dbcec366c
    resource: repo://packages/control-plane/src/source-control/providers/types.ts
  - id: openwiki-source-9e1fc673b739990b38eb4626
    resource: repo://packages/control-plane/src/source-control/types.ts
  - id: openwiki-source-24a04db2be6ca4d99bad18ac
    resource: repo://packages/control-plane/test/integration/scm-credentials.test.ts
  - id: openwiki-source-f6f47e23b1ed14decd181a3b
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/plugins/inspect-plugin.js
  - id: openwiki-source-33fb93d621029662cbb71c65
    resource: repo://packages/shared/src/types/artifacts.ts
  - id: openwiki-source-f000fa0dd3efa6df8cfb9a25
    resource: repo://packages/shared/src/types/repositories.ts
  - id: openwiki-source-21defa3cc0d7d4e68eb006f7
    resource: repo://packages/shared/src/types/repository-contracts.test.ts
  - id: openwiki-source-6934413ff393652baa2cb090
    resource: repo://packages/slack-bot/src/completion/blocks.ts
generated: { by: "openwiki/0.4.3", at: "2026-08-29T06:58:43.189Z" }
---

# Source Control Providers

Every Git-host interaction in Open-Inspect — repository lookup, PR creation, branch pushes,
sandbox git credentials, repository and tree reads — goes through one pluggable abstraction: the
`SourceControlProvider` interface in
`packages/control-plane/src/source-control/types.ts`. Two implementations ship today: a
**GitHub App–backed** provider (the production default) and a **PAT-backed GitLab** provider.
Bitbucket is a recognized name that fails fast with an explicit "not implemented" error.

The module is deliberately the only place in the control plane where provider-specific URLs, API
shapes, and token constructions live. ADR-0001 pins this: deployments are **single-provider**
(`SCM_PROVIDER` selects one), and provider-specific logic must never leak into router, session, or
bot layers (see [ADR-0001 boundaries](#adr-0001-boundaries-and-adding-a-provider)).

Related pages: [Control Plane Worker](/openwiki/architecture/control-plane-worker.md) (the router
SCM gate and route policies), [Security & Tokens](/openwiki/concepts/security-and-tokens.md)
(the token architecture and GitHub App brokering), [Sandbox Runtime]
(/openwiki/architecture/sandbox-runtime.md) (the sandbox-side git credential helper that consumes
`scm-credentials`), [Environments & Repositories](/openwiki/concepts/environments-and-repositories.md)
(repository identity and metadata), [Sessions](/openwiki/concepts/sessions.md) (the PR flow inside
the Session DO).

## The `SourceControlProvider` surface

The interface splits into three tiers:

| Tier | Methods | Credentials |
| --- | --- | --- |
| **User-authenticated** | `getRepository`, `createPullRequest` | A plain (already-decrypted) user token in `SourceControlAuthContext`; the session layer decrypts before calling |
| **App-authenticated** | `checkRepositoryAccess`, `listRepositories`, `listBranches`, `getBranchHead`, `resolveCommit`, `listTree`, `readBlob`, `getPullRequest`, `generatePushAuth`, `generateCredentialHelperAuth` | Provider-level credentials configured at construction (GitHub App installation / GitLab PAT), never a caller token |
| **Pure builders** | `buildManualPullRequestUrl`, `buildGitPushSpec` | No I/O — deterministic URL/spec construction from config |

The app-authenticated tier exists because the callers have no user in the loop: webhooks, the
read-through PR refresh, scheduler and automation fan-out, image builds, and the sandbox credential
path all run with deployment credentials only. `getPullRequest` is deliberately app-authenticated
"matching `listRepositories`" so webhook freshness checks cannot depend on a user token.

**Error contract.** Methods throw `SourceControlProviderError`, which carries an `errorType` of
`transient` (retryable: HTTP 429, 5xx, network-shaped failures like fetch failures, timeouts,
connection resets, aborts) or `permanent` (configuration, auth, validation — do not retry), plus
the HTTP status when one is known. `SourceControlProviderError.fromFetchError` is the shared
classifier both providers use, so retry policy is uniform across providers.

**Blob budgets.** `readBlob`'s `maxBytes` is a refusal threshold, not a truncation point: a shared
`readResponseBytesWithinLimit` helper cancels the body up front when `content-length` exceeds the
budget and cancels the stream mid-flight otherwise, so an oversized blob is never fully buffered.
Callers with a size budget must still re-check what they receive when a provider cannot know the
size up front (GitLab cannot — see below).

## Provider resolution: `SCM_PROVIDER`, factory, and wiring

```mermaid
flowchart TD
    Env["SCM_PROVIDER env value"] --> Res["resolveScmProviderFromEnv"]
    Res -->|"unset or empty"| Def["github default, DEFAULT_SCM_PROVIDER"]
    Res -->|"github, gitlab, or bitbucket"| Name["validated SourceControlProviderName"]
    Res -->|"anything else"| Err["permanent SourceControlProviderError"]
    Name --> Factory["createSourceControlProvider"]
    Factory -->|"github"| GHP["createGitHubProvider"]
    Factory -->|"gitlab with gitlab config"| GLP["createGitLabProvider"]
    Factory -->|"gitlab without config, or bitbucket"| Fail["permanent error at construction"]
    GHP --> Consumers["route handlers, Session DO, scheduler, automations, image builds"]
    GLP --> Consumers
```

*Provider resolution: the env resolver normalizes and validates, the factory constructs, and every
consumer ends up with one `SourceControlProvider` instance.*

Three layers make up resolution:

1. **Env resolver** (`source-control/config.ts`). `resolveScmProviderFromEnv` trims and lower-cases
   the value, returns `DEFAULT_SCM_PROVIDER` (`"github"`) when unset or empty, accepts only
   `github` / `bitbucket` / `gitlab`, and throws a permanent `SourceControlProviderError` listing
   the supported values otherwise. There is no provider auto-detection — configuration is the only
   input.

2. **Factory** (`source-control/providers/index.ts`). `createSourceControlProvider` switches on the
   name: `github` always constructs; `gitlab` requires GitLab configuration (a blank or missing
   token is a permanent error); `bitbucket` throws the explicit
   `"SCM provider 'bitbucket' is configured but not implemented."`; an unknown runtime value falls
   into an exhaustive `never` guard. A TypeScript-level exhaustiveness check means adding a
   provider name to the union forces a decision here.

3. **Env wiring** (`source-control/provider-from-env.ts`). `createSourceControlProviderFromEnv`
   assembles the factory config from `Env`: the GitHub App config plus a KV cache store over
   `env.REPOS_CACHE` (installation-token caching) and an app-name `User-Agent` (from
   `resolveAppName`, defaulting to `DEFAULT_APP_NAME`) for GitHub; and — only when
   `GITLAB_ACCESS_TOKEN` is set — the GitLab access token with the optional `GITLAB_NAMESPACE`
   group scope.

### Deployment-level gating in the router

The worker router memoizes the deployment-level SCM resolution in a module-level `cachedScmProvider`
keyed by the raw `SCM_PROVIDER` value: the validated name (or the permanent error) is computed once
per worker and re-thrown on later requests. `enforceImplementedScmProvider` then answers **501 Not
Implemented** for any route whose `supportedScmProviders` policy excludes the deployment provider,
and **500** when the configuration itself is invalid (surfacing the resolver's message).

Two ordering properties are pinned by tests:

- **Authentication runs before the SCM gate** — an unauthenticated request to a restricted route
  gets 401 even on a GitLab deployment, never a 501.
- **Public routes still see invalid configuration** — `/health` with an invalid `SCM_PROVIDER`
  returns the resolver's error as 500 rather than silently serving.

Route policies live as named constants in `routes/shared.ts`
(`GITHUB_USER_OR_SERVICE_ROUTE`, the `SCM_AGNOSTIC_*` variants, `SCM_CREDENTIALS_ROUTE`, and the
sandbox-fallback variants). Notably, `SCM_CREDENTIALS_ROUTE` — sandbox-authenticated, supporting
`github` + `gitlab` — is the **only** GitLab-supporting route in the API; everything
GitHub-specific (commit signing, ws-token) is pinned to `["github"]`.

### Instance lifetimes

Provider construction is cheap (no I/O), so instances are created close to their use:

- **Worker routes** build one per request via `createRouteSourceControlProvider(env)` — repository
  listing (`/repos`, which enriches `listRepositories()` output with D1 metadata and a KV cache),
  repo access checks, image-build triggers, and so on.
- **The Session DO** constructs its instance eagerly during runtime initialization, so a
  misconfigured deployment (invalid `SCM_PROVIDER`, GitLab without a token, Bitbucket) fails every
  session request at initialization — before any session state is written — instead of running
  degraded until the first spawn or PR operation.
- **Background consumers** (scheduler, automations, image builds) construct per firing, or take a
  provider as an injected dependency; `resolveSessionRepoId` deliberately takes the provider as a
  *thunk* so legacy session rows that already carry a `repo_id` never construct it at all.

## Shared provider semantics

A few contracts hold identically across implementations and are pinned in the interface docs:

- **404 is absence, not error, for existence lookups.** `getBranchHead` and `resolveCommit` return
  `null` on a confirmed 404 and throw only for auth, throttling, and transport failures.
- **`checkRepositoryAccess` returns `null` for "not available to this deployment"** — not installed
  (GitHub), not accessible (GitLab), archived, or without a readable default branch. Accessible
  results carry the stable numeric `repoId`, **lower-cased** `repoOwner`/`repoName`, and the
  `defaultBranch`.
- **Rename/transfer tolerance in `getPullRequest`.** On a 404 with a known stable
  `repositoryExternalId`, both providers resolve the repository's current owner/name by id
  (GitHub's undocumented `GET /repositories/{id}` alias; GitLab's `GET /projects/{id}`) and retry
  exactly once. Resolution is a best-effort repair path: any failure degrades to the original 404.
  The successful read's location is treated as canonical for the returned snapshot.
- **Providers return status facts, not display state.** `CreatePullRequestResult` and
  `PullRequestSnapshot` carry `lifecycleState` + `isDraft`; consumers derive display state with
  `toDisplayStatus` at their own boundary, so a provider result can never carry an inconsistent
  pair (the invariant: `isDraft` is true only while `lifecycleState === "open"`).
- **Tree/blob reads are pinned to a commit.** `listTree` lists entries reachable from one commit;
  `readBlob` reads by the provider blob id from that listing, so content cannot drift with the ref.
  Entries are classified by the shared `classifyGitTreeEntry` (tree/blob modes; symlinks,
  submodules, and unknown modes become `"other"` for the caller to decide about), and
  `truncated` reports a provider cap being hit so callers can refuse partial listings.

## GitHub provider

`GitHubSourceControlProvider` wraps the GitHub REST API (`GITHUB_API_BASE = https://api.github.com`,
the only place that base URL is defined outside the auth module) and is backed by the deployment's
GitHub App installation. Wire responses are parsed through strict zod schemas — for instance, the
pull-request `state` is a closed enum, so upstream schema drift fails the parse instead of coercing
an apparently-valid status.

**User-authenticated operations.** `getRepository` reads repo metadata (default branch, visibility,
stable numeric id) with the user's `Bearer` token; `createPullRequest` posts to `/pulls` and maps
the response through `deriveGitHubPullRequestStatus`: GitHub models merged as `state: "closed"` +
`merged: true`, so **merged wins over a stale draft flag**, then closed, then open with
`isDraft: draft === true`. The result captures `headSha`, `repositoryExternalId` (from
`base.repo.id` — the canonical PR identity used for rename tolerance later), and
`providerUpdatedAt` (seeds the monotonic webhook write guard). Labels and reviewers are
best-effort: labels are ensured per-label with a 422 race re-check (a concurrent creator winning
between GET and POST is confirmed by re-GET, not treated as failure), and any failure is logged
without failing PR creation.

**App-authenticated plumbing.** All app-level calls go through one `appFetch` helper that mints an
installation token via `getCachedInstallationToken` (in-memory + KV cache over the provider's
`cacheStore`) and sends it as `Bearer`. A missing `appConfig` is a permanent error at call time.
`generateCredentialHelperAuth` uses the with-expiry variant of the token getter so the returned
`expiresAtEpochMs` is the installation token's true lifetime — the sandbox credential helper caches
its copy until shortly before expiry.

**Credential-helper auth.** `generateCredentialHelperAuth` returns:

- `username: "x-access-token"` (GitHub's HTTPS basic-auth username for installation tokens)
- `password`: a freshly minted installation token
- `expiresAtEpochMs`: the token's absolute expiry

**URL and push-spec builders.**

- `buildManualPullRequestUrl` → `https://github.com/{owner}/{name}/pull/new/{base}...{head}`, with
  every component run through `encodeURIComponent` wholesale (a nested owner or a slash in a name
  becomes one percent-encoded segment).
- `buildGitPushSpec` → remote URL `https://x-access-token:{token}@github.com/{owner}/{name}.git`,
  a `redactedRemoteUrl` twin with `<redacted>` for safe logging, refspec
  `{sourceRef}:refs/heads/{targetBranch}`, and `repoOwner`/`repoName`/`force` fields.

**GitHub-only capabilities.** The concrete class also exposes methods outside the portable
interface — `getPullRequestFeedback` (PR comments and reviews, with ownership checks that bind a
comment/review to the requested PR path) and `hasPullRequestWritePermission` — used by the
feedback/autofix pipelines. The interface is the portable surface, not the whole provider; callers
of GitHub-only methods type-narrow to the concrete class.

## GitLab provider

`GitLabSourceControlProvider` is PAT-only: the deployment configures one Personal Access Token
(`GITLAB_ACCESS_TOKEN`, needing `read_api` scope for reads and `api` scope to create merge requests
and push; `GITLAB_NAMESPACE` optionally scopes repository listing to a group), and every API call
uses `Bearer {PAT}` against `https://gitlab.com/api/v4`. The constructor rejects a blank token
permanently, so a misconfigured GitLab deployment fails at provider construction.

**Nested groups are the GitLab-specific subtlety.** API paths URL-encode the whole project path
(`owner/name` → `owner%2Fname`), and `getRepository` maps the project's `namespace.full_path` — not
the bare namespace — to `owner`, so nested groups (`group/subgroup`) round-trip through owner/name
lookups. A missing `default_branch` in the response is a permanent
`"token cannot read repository code"` error: the token can see the project but not its code, which
is never a usable deployment state.

**Merge request creation** adapts GitHub-shaped config: drafts become a `Draft: ` title prefix
(GitLab's draft convention), labels are joined into one field, and reviewer assignment is
unsupported — usernames would need per-reviewer username→ID resolution, so the request is warned
and ignored. `deriveGitLabMergeRequestStatus` maps GitLab's first-class `merged` state terminal-first
and treats the transient mid-merge `locked` state as open.

**Listing and tree semantics.** `listRepositories` hits `/groups/{ns}/projects` (with
`include_subgroups=true`) when a namespace is configured, else `/projects?membership=true`, always
`archived=false`; it uses the project `path` (URL slug), never the display name, and drops projects
without a default branch. `listTree` paginates at GitLab's per-page maximum (100) and caps at
`MAX_TREE_PAGES = 20` pages before reporting `truncated: true`; GitLab's tree endpoint reports **no
blob sizes**, so `sizeBytes` is always `null` and callers must enforce size budgets at `readBlob`
time. A 404 on a scoped subtree path (GitLab 17.7+) is an empty listing, not an error.

**Credential-helper auth.** `generatePushAuth` returns the PAT itself (`authType: "pat"`) — there
is no app installation to broker with — and `generateCredentialHelperAuth` returns username
`oauth2`, password = the PAT, and a fixed **one-hour** expiry, because GitLab PATs expose no expiry
the control plane could read; the hourly refresh keeps the sandbox cache conservative.

**URL and push-spec builders.**

- `buildManualPullRequestUrl` → `https://gitlab.com/{owner}/{name}/-/merge_requests/new?merge_request[source_branch]=…&merge_request[target_branch]=…`,
  with the project web path built per-segment (`encodeProjectWebPath`) so nested group separators
  survive as literal `/` while each segment is encoded.
- `buildGitPushSpec` → `https://oauth2:{token}@gitlab.com/{owner}/{name}.git` with **deliberately
  literal (unencoded) path segments** — GitLab project paths are always URL-safe, and git clients
  expect literal segments in remote URLs — plus the same redacted twin and refspec shape as GitHub.

## Nested owner namespaces

Repository **owners may contain `/`** (GitLab groups/subgroups), and the whole stack is built
around that assumption rather than around flat `owner/name` pairs:

- `parseRepositoryFullName` (in `packages/shared/src/types/repositories.ts`) splits an
  `owner/name` string **on the last slash** and rejects empty owner segments — so
  `group/subgroup/web` parses as owner `group/subgroup`, name `web`.
- Repository APIs encode the owner as **one route segment**:
  `encodeRepositoryPathSegments({ repoOwner: "group/subgroup", repoName: "web app" })` →
  `group%2Fsubgroup/web%20app`. Route patterns bind `:owner` to a single `[^/]+` segment, so the
  encoded owner arrives intact and `extractRepoParams` decodes it via
  `decodeRepositoryPathSegments` — which also rejects an encoded slash in the *name* and
  non-canonical encodings with a 400.
- Per provider, the GitLab provider re-encodes as needed: the whole `owner/name` path for API calls
  (`owner%2Fname`), per-segment for web URLs. The web client's URL builder
  (`packages/web/src/lib/scm.ts`) does the same split, driven by the build-time
  `NEXT_PUBLIC_SCM_PROVIDER`.

## The GitHub credential authority: App vs user tokens

`source-control/github-credential-authority.ts` answers one question for GitHub deployments: *which
credential store backs this user's GitHub identity and token for SCM enrichment?*

`resolveGitHubCredentialAuthority` returns one of two shapes:

- `{ kind: "browser_session", accountClient, githubAccount }` — the user authenticated through the
  browser; their GitHub identity/token flow through Better Auth's provider-account client. The
  linked-account list is fetched per request (only when an SCM workflow asks for it) and validated:
  a malformed list, an account belonging to a different user (`"GitHub account authority is
  corrupt"`), or **more than one linked GitHub account** (`"User resolves to multiple GitHub
  provider accounts"`) throws. A user with no linked GitHub account resolves to
  `githubAccount: null` — they fall back to the shared App bot identity for PRs, and account
  linking is intentionally deferred.
- `{ kind: "legacy" }` — the transitional D1 token-store lookup. **Service actors are the only
  callers that retain the legacy authority**, and a user principal without browser-session
  provenance (missing `authentication` or auth runtime) throws rather than silently downgrading:
  a browser user must never fall back to the legacy token store.

The authority is consumed by `resolveGitHubEnrichmentForRequest` (`session/identity.ts`): the
legacy path reads the D1 `user_scm_tokens` store; the browser-session path fetches the access token
via the account client and encrypts it with the deployment's token-encryption key before it touches
session state. Both `session-create` and `session-prompt` resolve the authority — and only when
`resolveScmProviderFromEnv(env.SCM_PROVIDER) === "github"`; on GitLab deployments the GitHub
enrichment path is skipped entirely.

## Git push and sandbox credential flows

```mermaid
sequenceDiagram
    participant Tool as create-pull-request tool
    participant DO as Session DO
    participant Prov as SourceControlProvider
    participant SB as Sandbox bridge

    Tool->>DO: POST /internal/create-pr
    DO->>DO: resolve prompting participant OAuth token
    DO->>Prov: generatePushAuth()
    Prov-->>DO: app or PAT token
    DO->>Prov: buildGitPushSpec(owner, name, sourceRef, targetBranch, auth)
    Prov-->>DO: GitPushSpec with credential-bearing remoteUrl and redactedRemoteUrl
    DO->>SB: push command carrying pushSpec
    SB-->>DO: push_complete or push_error
    DO->>Prov: createPullRequest(promptingAuth or app auth, config)
    Prov-->>DO: PR id, webUrl, lifecycle facts, headSha, repositoryExternalId
```

*PR creation: the provider builds the push transport and the PR; the prompting user's OAuth token
is preferred for attribution, the app/PAT token is the fallback.*

`SessionPullRequestService` (inside the Session DO) orchestrates the flow. It calls
`generatePushAuth()` for the push credential, builds the `GitPushSpec` for the target repository and
head branch, and hands the spec to `SandboxPushService`, which sends the protocol's only
request/response exchange — a `push` command over the sandbox WebSocket, settled by
`push_complete`/`push_error` events (360-second timeout; no connected sandbox counts as success,
assuming the branch was pushed manually). PR creation then runs with the prompting user's OAuth
token when one resolves (attribution), falling back to the app/PAT token — the path taken by
Linear-spawned sessions and users without a linked GitHub account. The resulting
`PullRequestSnapshot`-shaped facts flow into the DO artifact and the D1 authority record, and
`GitPushSpec.repoOwner`/`repoName` select the checkout in multi-repo sandboxes.

```mermaid
sequenceDiagram
    participant Git as git inside sandbox
    participant Helper as git credential helper
    participant CP as control plane
    participant Prov as SourceControlProvider

    Git->>Helper: credential get for VCS host
    Helper->>CP: POST /sessions/:id/scm-credentials with sandbox token
    CP->>Prov: generateCredentialHelperAuth()
    Prov-->>CP: username, password, expiresAtEpochMs
    CP-->>Helper: credentials
    Helper-->>Git: username and password
```

*Per-request sandbox credentials: the helper mints a fresh token on every git operation instead of
holding a long-lived one.*

On the sandbox side, the git credential helper calls `POST /sessions/:id/scm-credentials` — a
sandbox-authenticated route supporting github + gitlab only — which wraps
`generateCredentialHelperAuth` in `ScmCredentialsService`. The service validates the response
(non-empty username/password, finite `expiresAtEpochMs` in the future — otherwise 500), maps
permanent provider errors to **500** and transient ones to **502** so the helper exits non-zero and
the next git operation retries, and never logs the returned password. Complementing this,
`scmCloneIdentity` maps each provider to a sandbox clone identity — host, clone username, and
secret hosts (`github.com`/`x-access-token`, `gitlab.com`/`oauth2`, `bitbucket.org`/
`x-token-auth`) — applied to sandbox env as `VCS_HOST`/`VCS_CLONE_USERNAME`, matching the
credential-helper usernames the providers return.

The manual fallback builder stays part of the contract for the no-OAuth path: manual-PR branch
artifacts (`mode: "manual_pr"` with a `createPrUrl`) are rendered as Create-PR buttons by the Slack
bot, and the sandbox's create-pull-request tool can surface a manual envelope (`kind: "manual"`
with a `createPrUrl`) when the create response signals the manual path.

Two provider-aware degradations complete the picture: managed-skill imports consume only the
`RepositoryReader` subset (`checkRepositoryAccess`, `resolveCommit`, `listTree`, `readBlob`), and
commit signing is GitHub-only — on non-GitHub deployments the sandbox commit-signing endpoints
return an explicit `{ enabled: false }` (GET) / 409 (POST) so unsupported providers degrade safely
instead of failing sessions.

## ADR-0001 boundaries and adding a provider

[ADR-0001](/docs/adr/0001-single-provider-scm-boundaries.md) is the accepted architecture decision
this module implements:

1. **Single provider per deployment.** `SCM_PROVIDER` selects it; no per-session provider state is
   persisted (PR artifact metadata deliberately has no `provider` field — provider is deployment
   state). Multi-provider-per-deployment would require a new ADR.
2. **Fail fast for unimplemented providers.** A provider name without an implementation
   (`bitbucket` today) gets explicit `501 Not Implemented` responses on non-public routes rather
   than silent misbehavior.
3. **Provider/auth boundary rules.** Provider-specific PR URL construction, push-transport
   construction, and sandbox credential-helper auth live in provider implementations; direct GitHub
   API base URL usage is limited to approved auth/provider modules. No provider-specific URL/token
   logic in router/session/slack layers.
4. **Guardrails via code review + focused tests** (below).

[`docs/provider-contribution-checklist.md`](/docs/provider-contribution-checklist.md) is the
extension guide for a new provider:

- **Architecture:** implementation under
  `packages/control-plane/src/source-control/providers`; an explicit factory case in
  `createSourceControlProvider`; the name recognized by `resolveScmProviderFromEnv`; no
  provider-specific URL/token logic outside provider/auth modules.
- **Auth and API:** user-authenticated repository lookup and PR creation via the interface; push
  auth via the provider path; `buildManualPullRequestUrl`, `buildGitPushSpec`, and
  `generateCredentialHelperAuth` implemented (the helper auth is a prerequisite for enabling
  helper-backed sandbox git auth for the provider).
- **Tests:** factory tests for selection and unsupported behavior; provider tests for manual PR URL
  building, push spec building, credential-helper auth generation (or explicit
  unsupported-provider behavior), and basic API mapping; existing create-PR branch-consistency
  tests and Slack manual-PR button tests still pass.
- **Documentation:** control-plane README documents new provider env vars/constraints; the ADR is
  updated when architecture assumptions change.

Tests that actually pin this module's behavior today:

| Test | Pins |
| --- | --- |
| `source-control/providers/index.test.ts` | Factory selection, gitlab-without-config, bitbucket explicit not-implemented, unknown runtime value |
| `source-control/config.test.ts` | Default to github, case/whitespace normalization, invalid value throws |
| `source-control/provider-from-env.test.ts` | Env wiring: default GitHub, GitLab with token/namespace (including helper auth shape), GitLab without config throws |
| `source-control/providers/github-provider.test.ts`, `gitlab-provider.test.ts` | API mapping, PR status derivation invariants, rename tolerance, manual PR URL building (nested-group preservation on GitLab), push spec (redaction, literal GitLab segments), credential-helper auth |
| `source-control/github-credential-authority.test.ts` | Authority selection: linked account chosen, no-account allowed, cross-user and multi-account rejected, user-without-provenance rejected, legacy for service principals |
| `routes/shared.test.ts` | Nested owner namespace decode as one segment; encoded slash in the name rejected |
| `router.policy.test.ts` | Auth-before-SCM-gate ordering, invalid-config preservation on public routes, SCM credentials as the only GitLab exception |
| `test/integration/scm-credentials.test.ts` | Full sandbox-auth → DO → service → provider credential chain |
| `test/integration/create-pr.test.ts` | App-token fallback when the prompting user's token is unusable; multi-repo PR creation |
| `test/integration/session-do-collaborator-wiring.test.ts` | `buildGitPushSpec` returns a complete spec with `repoOwner`/`repoName` for checkout selection |
