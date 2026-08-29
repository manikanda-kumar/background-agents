# OpenWiki model comparison — DeepSeek V4 Flash vs Grok 4.6 vs GLM 5.3 Flash vs Qwen 3.8 Flash

Oracle reviews of `openwiki --init` on **background-agents** (Open-Inspect) at
git SHA `32470cc2`. Flash models used stock `openwiki@0.4.3` via OpenCode Go.
Grok 4.6 used the fork's **host-agent** path (`host-agent/grok`): Grok
researches/writes; OpenWiki MCP owns page jobs. Runtime is not comparable;
**wiki quality** is.

This is about **reasoning and code-understanding quality**, not page counts.
Qwen (9/38, dead) and GLM (still generating) remain incomplete; their written
samples still count for style and grounding.

## Verdict

**Pick DeepSeek V4 Flash as the wiki author for this repo.** Best combination of
usable taxonomy, broad grounding, and support for risky coding changes. It
already formed a coherent 26-page wiki.

**Grok 4.6 is narrowly second.** Best human-facing pages: compact, task-oriented,
consistently structured, generally well grounded, and it finished 24 pages. It
avoids DeepSeek's `BROWSER_AUTH_SECRET` encryption-at-rest slip. Compression is
not free: the session lifecycle flattens the status model, and the dedicated
realtime page still misses Qwen's Worker WebSocket-bypass consequences.

Supplement later (do not mix voices on one page):

- **Grok** as default page writer (replaces GLM now that a finished wiki exists).
- **GLM** as workflow-page fallback when Grok's draft is too compressed.
- **Qwen** as forensic rewrite for boundary pages (WebSocket/reconnect,
  service-auth, SCM providers, adding a sandbox provider, persistence).

Recommended fork split: `OPENWIKI_PLANNER_MODEL_ID=deepseek-v4-flash`,
`OPENWIKI_PAGE_MODEL_ID` → Grok when host-agent is available else GLM,
`OPENWIKI_SPECIALIST_MODEL_ID=qwen3.8-flash`.

Do **not** let Qwen design the map (38 pages, over-split). Do **not**
ensemble-merge pages.

## Rankings

| Dimension | 1st | 2nd | 3rd | 4th |
| --- | --- | --- | --- | --- |
| Taxonomy / information architecture | **DeepSeek** | Grok | GLM | Qwen |
| Code-grounding | **Qwen** | Grok | DeepSeek | GLM |
| Reasoning depth per unit of prose | **Qwen** | DeepSeek | Grok | GLM |
| Usefulness for a real coding change | **DeepSeek** | Grok | Qwen | GLM |
| Page style / clarity | **Grok** | GLM | DeepSeek | Qwen |
| Overall wiki author | **DeepSeek** | Grok | Qwen | GLM |

Grok's voice is closest to **GLM's operator explanation**, more aggressively
compressed: short ownership sections, job tables, invariants, failure behavior,
extension seams, focused tests. Neither DeepSeek's long briefing nor Qwen's
forensic dump.

## Runtime

| Model | Started | Finished | Phase | Plan | Complete | Wiki size |
| --- | --- | --- | --- | --- | --- | --- |
| qwen3.8-flash | 05:37Z | died 15:08Z | generating | 38 | 9 | ~284K (9 md) |
| glm-5.3-flash | 06:58Z (3rd attempt) | — | generating | 28 | 4 | still crawling |
| deepseek-v4-flash | 05:37Z | 09:38Z | complete | 26 | 26 | 33 md, [quickstart](deepseek-v4-flash/quickstart.md) |
| grok-4.6 | ~14:40Z | 15:39Z | complete | 24 | 24 | 32 md, [quickstart](grok-4.6/quickstart.md) |

GLM attempt 1 died on undici `headersTimeout` after ~63 min of planning;
attempt 2 with HTTP streaming died in 17s (`wrapModelCall` / GLM
`reasoning_content`). Attempt 3: no streaming, patched 1h headersTimeout, then
a restore-missing-file death; manual restart is the current generating run.
Grok's first host-agent pass hit `Max turns reached` at 12/24; resume with
`--max-turns 200` finished.

Flash models scheduled `quickstart.md` last — a shared planning-order mistake.
Grok's quickstart is last in generation order too, but it is a **routing table**,
not a long document.

## Grok 4.6 — best human-facing finished wiki

**Taxonomy:** 24 pages with real ownership boundaries. Unique folders:
`data-plane/`, `features/`, dedicated `architecture/realtime-protocol.md`.
Quickstart is a job-routing table. Missing vs DeepSeek: standalone persistence,
`session-diffs`, media/attachments, testing as its own section (Grok put testing
under `operations/`). All checked wiki links resolve; no invented destinations.

**Strengths**

1. Best task-oriented IA. Quickstart routes by job (understand a session, follow
   a prompt, debug a sandbox, deploy, extend bots) instead of narrating the repo.
2. Strong compression of the control-plane contract. 178 lines still has
   Worker/DO split, D1-before-DO, exact crons, queue-prefix routing, D1 vs DO
   SQLite, failure behavior, extension seams, tests.
3. Correct secret-domain treatment: `requireTokenEncryptionKey` /
   `requireRepoSecretsEncryptionKey`. Does **not** repeat DeepSeek's
   `BROWSER_AUTH_SECRET` encryption-at-rest slip.
4. Sessions/workspaces page extracts four exclusive targets, primary-repo
   mirroring, nested GitLab owners, environment snapshot semantics.
5. GitHub page catches a real bug: session create does not pass PR head, but the
   review prompt pretends the sandbox is already on that branch. Cleanly
   separates direct sessions, Autofix, and automation forwarding.

**Failure modes**

1. **Wrong session status model.** Writes `Created → Active → Archived` and says
   unarchive restores `active`. Source settles from existing messages and warns
   that forcing `active` falsely claims work exists. Also omits `completed` /
   `failed` / `cancelled`. Clearest under-research from compression.
2. Sees the WebSocket upgrade split before `handleRequest`, never derives Qwen's
   operational consequence: no route policy, preflight, correlation headers, or
   `http.request` log on that path.
3. Lifecycle page is orientation, not enough to change the subsystem (no D1
   compensation on failed DO init, CAS dispatch, two-phase sandbox identity,
   stop-confirmation).
4. Compact map leaves persistence, session-diffs, and media implicit.
5. "Quickstart" is intentionally only navigation — setup stays in `docs/`.
   Correct for a wiki, weak if a new operator expected a runnable onboarding.

**Style:** Best writer for humans. Headings, tables, invariants, then stop.
Quickstart 84 lines vs DeepSeek 242; overview 115 vs DeepSeek 292.

Samples: [quickstart.md](grok-4.6/quickstart.md),
[architecture/overview.md](grok-4.6/architecture/overview.md),
[architecture/control-plane.md](grok-4.6/architecture/control-plane.md),
[integrations/github.md](grok-4.6/integrations/github.md).

## DeepSeek V4 Flash — best overall

**Taxonomy:** Compact noun-based map. Architecture (`control-plane`, `overview`,
`persistence`, `sandbox-providers`, `sandbox-runtime`, `session-do`,
`web-client`). Concepts are nouns (sessions, secrets, environments, automations,
managed-skills, models-providers, prebuilt-images). Unique:
`workflows/session-diffs.md`.

**Strengths**

1. Best top-level mental model. `architecture/overview.md` covers single-tenant
   trust, package graph, canonical prompt path, DO/D1 split, change-routing.
2. Compression without usually going shallow. `architecture/control-plane.md` is
   221 lines and still has exact crons, queue dispatch, route policies,
   D1-before-DO.
3. Excellent invariant extraction. `concepts/sessions.md`: four exclusive
   targets, primary-repo semantics, nested `repo_owner`, D1-before-DO.
4. Broad written coverage — quality is less sample-sensitive.
5. Cross-language hazards: stale credential cache, argv git,
   `SESSION_DIFF_VERSION` sync.

**Failure modes**

1. Compression can invent. `architecture/overview.md` named `BROWSER_AUTH_SECRET`
   as an encryption-at-rest domain; scoped secrets use
   `REPO_SECRETS_ENCRYPTION_KEY`. Fix before publishing.
2. No standalone Qwen-style protocol pages (service-auth, SCM providers,
   sandbox-runtime-protocol, websocket-reconnect).
3. Missed Qwen's WebSocket bypass warning (upgrade path skips `handleRequest` →
   no CORS / correlation headers / HTTP log).
4. Weaker related-page graph than Grok's routing tables.

**Style:** Best briefing. Walls of bullets, few tables. Invariant dump at the
end is gold for an agent.

Samples: [architecture/control-plane.md](deepseek-v4-flash/architecture/control-plane.md),
[architecture/overview.md](deepseek-v4-flash/architecture/overview.md),
[quickstart.md](deepseek-v4-flash/quickstart.md).

## Qwen 3.8 Flash — deepest reader, over-expanded editor

**Taxonomy:** 38 pages, finest grain. Extra unique pages:
`sandbox-runtime-protocol.md`, `service-auth-and-boundaries.md`,
`source-control-providers.md`, `websocket-streaming-and-reconnect.md`,
`adding-a-sandbox-provider.md`, `control-plane-integration-tests.md`.
Instructions are procedural (file names, regexes, ESLint rules, Mermaid types).

**Strengths**

1. Best hidden-boundary reasoning. Control-plane worker page derives that the
   WebSocket branch bypasses `handleRequest` (no route policy, preflight,
   `x-request-id`/`x-trace-id`, HTTP log); admission splits Worker D1 check vs
   DO socket auth.
2. Best persistence ownership. DO SQLite vs D1 projections vs R2 payloads vs
   disposable KV; D1 `session_repositories` has identity, not git state.
3. Concurrency/durability, not just schemas (derived automation status, queue
   leases, compensating R2 cleanup).
4. Strongest provider/runtime boundary: `/app/sandbox_runtime` as a hard
   contract (`OpenCodeServer` absolute paths).
5. Best specialist destinations for real maintenance tasks.

**Failure modes**

1. Over-splitting. Sandbox concerns spread across six pages.
2. Bad generation order: `system-overview.md` sixth, `quickstart.md` last.
   Unfinished init is not useful. Died 15:08Z restoring a missing file.
3. Pages mirror implementation too closely (357–558 line samples). High drift
   cost.
4. Throughput: ~1 page / 45–50 min. 38-page plan is impractical for a first wiki.

**Style:** Forensic dump. Highest insight per sentence, lowest scannability.
Giant table cells, `defineRoute` snippets. Fine for a specialist page, terrible
as default house style.

Samples: [control-plane-worker.md](qwen3.8-flash/architecture/control-plane-worker.md),
[data-model-and-persistence.md](qwen3.8-flash/architecture/data-model-and-persistence.md),
[sandbox-data-plane.md](qwen3.8-flash/architecture/sandbox-data-plane.md).

## GLM 5.3 Flash — best workflow planner, thin written evidence

**Taxonomy:** ~28 pages, workflow-heavy. Unique: `media-and-attachments.md`,
automations as workflow not concept, model-provider-accounts as integration.

**Strengths**

1. Best workflow decomposition: `session-creation`, `prompt-lifecycle`,
   `sandbox-lifecycle`, `image-builds`, `git-and-pull-requests`,
   `media-and-attachments`.
2. Generated control-plane page is excellent: numbered `handleRequest` pipeline,
   exact crons, queue-prefix dispatch (`open-inspect-github-autofix-` so a
   similarly named image-build queue is not stolen).
3. Plans trace convergence (web/Slack/GitHub/Linear/automations/child spawn →
   one init path).
4. Only dedicated media/attachments workflow.
5. Operational constants (`MAX_SPAWN_DEPTH=2`, scheduler budgets) instead of
   "describe limits".

**Failure modes**

1. Written quality still rests on a handful of pages. Cannot claim repo-wide
   consistency yet.
2. Category boundaries less coherent (provider accounts as integration;
   automations as workflow).
3. Protocol boundaries underexposed vs Qwen.
4. Same WebSocket-bypass miss as DeepSeek.
5. Throughput and restore-missing-file deaths make a finished GLM wiki uncertain.

**Style:** Best writer among the flash models. Numbered pipeline, tables, then
depth. Headings every ~15 lines. Diagrams earn their keep. Dense but scannable.
Grok now occupies that slot with a finished wiki; GLM remains the fallback if
host-agent Grok is unavailable.

Sample: [control-plane-worker.md](glm-5.3-flash/architecture/control-plane-worker.md),
[architecture/overview.md](glm-5.3-flash/architecture/overview.md).

## Style on the same topic (control plane)

| | Grok | GLM | DeepSeek | Qwen |
| --- | --- | --- | --- | --- |
| Length | 178 lines | 294 lines | 221 lines | 356 lines |
| First 30s | You know ownership + failure modes | You know the pipeline | You know the system | You're in `index.ts` |
| Structure | Tables + invariants + seams | Headings + tables + lists | Prose + mermaid + invariants | Nested `###` + giant tables |
| Voice | Operator writing a contract | Operator explaining a system | Senior eng compressing a design doc | Staff eng narrating the source |
| Agent-useful | Copy the failure table | Follow the numbered steps | Copy the invariant list | Only if you already know where to look |

## Overlaps, gaps, unique discoveries

| Area | Shared | Best distinctive observation |
| --- | --- | --- |
| Control plane | Entrypoints, policies, crons, queues, D1 | **Qwen:** WS/router bypass consequences |
| Persistence | D1 vs DO SQLite, R2, KV | **Qwen:** D1 identity vs DO git state |
| Orientation | Three tiers, package graph | **DeepSeek:** single-tenant trust + change-routing map; **Grok:** job-routing quickstart |
| Sessions | Targets, lifecycle, children | **DeepSeek:** exclusive targets, nested owner, D1-before-DO; **Grok:** same, more compressed; **Grok miss:** unarchive status |
| Sandbox | Providers, lifecycle, images | **Qwen:** `/app/sandbox_runtime` packaging contract |
| Workflows | Prompt, sandbox, images, PRs | **GLM:** clearest workflow IA |
| GitHub | Bot vs Autofix | **Grok:** create-session does not pass PR head vs prompt claiming it does |
| Media | Indirect elsewhere | **GLM:** only dedicated page |
| Diff protocol | Runtime mention | **DeepSeek:** standalone `session-diffs.md` |
| Extension tasks | Embedded | **Qwen:** `adding-a-sandbox-provider.md` |

**Common gaps:** quickstart last in generation order; control-plane/backend
heavy; no strong "change the client socket/reducer safely" workflow (Qwen's
reconnect page is the closest). Grok's realtime page exists but does not replace
that specialist page.

Ideal taxonomy: DeepSeek's 26-page structure + Grok's routing/quickstart +
Qwen's highest-value specialist pages only.

## Throughput vs quality

DeepSeek is **not** cheaper thinking. Control-plane and sessions pages keep
constants, ownership, ordering invariants, failure semantics. Speed is selection
and compression. The encryption-key slip is the cost of that compression.

Grok is faster *and* more scannable, but the unarchive error shows the same
compression tax on state machines.

Qwen is not padding — the detail is usually real — but 38 pages at ~50 min/page
is the wrong first-wiki granularity.

GLM has the best flash-model workflow IA and page style; the snapshot still does
not prove it across persistence, runtime, integrations, and frontend.

## How to leverage the four

Stock OpenWiki cannot split models. The fork can
(`manikanda-kumar/openwiki`: planner/page/specialist + Grok host-agent).

| Role | Model | Env |
| --- | --- | --- |
| Planner | DeepSeek | `OPENWIKI_PLANNER_MODEL_ID` |
| Default page writer | **Grok** (host-agent) or GLM (native) | `OPENWIKI_PAGE_MODEL_ID` / Grok MCP |
| Specialist prefixes | Qwen | `OPENWIKI_SPECIALIST_MODEL_ID` |
| Workflow fallback | GLM | use when Grok drafts are too compressed |

Default specialist prefixes: `architecture/session-`,
`integrations/service-auth`, `integrations/source-control`,
`workflows/websocket`, `workflows/adding-a-sandbox-provider`,
`sandbox-runtime-protocol`.

One page, one writer. No ensemble merge.

**Reverse Grok-as-pages if** audits show it repeatedly flattening
behavior-changing state machines or security edges — the unarchive error and
WebSocket-bypass miss are that pattern. Then restore GLM as default writer and
confine Grok to orientation/editorial pages.

## Plan inventories

Full plan dumps: [plans/](plans/).

- DeepSeek 26 pages — [plans/deepseek-v4-flash.summary.json](plans/deepseek-v4-flash.summary.json)
- GLM ~28 pages — [plans/glm-5.3-flash.summary.json](plans/glm-5.3-flash.summary.json)
- Qwen 38 pages — [plans/qwen3.8-flash.summary.json](plans/qwen3.8-flash.summary.json)
- Grok 24 pages (finished manifest) — [plans/grok-4.6.summary.json](plans/grok-4.6.summary.json)
