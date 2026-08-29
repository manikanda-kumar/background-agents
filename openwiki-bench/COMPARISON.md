# OpenWiki model comparison — DeepSeek V4 Flash vs GLM 5.3 Flash vs Qwen 3.8 Flash

Oracle review of three `openwiki@0.4.3 --init` runs of **background-agents** (Open-Inspect) via OpenCode Go. Locked to a mid-run snapshot (~09:19 UTC 2026-08-29) plus a later style pass on the generated control-plane pages. DeepSeek later finished; Qwen/GLM were still generating.

This is about **reasoning and code-understanding quality**, not page counts.

## Verdict

**Pick DeepSeek V4 Flash as the wiki author for this repo.** Best combination of usable taxonomy, broad grounding, concise reasoning, and throughput. It already formed a coherent wiki while the others were still early pages.

Supplement later (do not mix voices on one page):

- **GLM** as house style for workflow pages (`prompt-lifecycle`, `sandbox-lifecycle`, `session-creation`).
- **Qwen** as forensic rewrite for boundary pages (WebSocket/reconnect, service-auth, SCM providers, adding a sandbox provider).

The OpenWiki fork now supports that split: `OPENWIKI_PLANNER_MODEL_ID=deepseek-v4-flash`, `OPENWIKI_PAGE_MODEL_ID=glm-5.3-flash`, `OPENWIKI_SPECIALIST_MODEL_ID=qwen3.8-flash`.

Do **not** let Qwen design the map (38 pages, over-split). Do **not** ensemble-merge pages.

## Rankings

| Dimension | 1st | 2nd | 3rd |
| --- | --- | --- | --- |
| Taxonomy / information architecture | **DeepSeek** | GLM | Qwen |
| Code-grounding | **Qwen** | DeepSeek | GLM |
| Reasoning depth per unit of prose | **DeepSeek** | GLM | Qwen |
| Usefulness for a real coding change (at snapshot) | **DeepSeek** | Qwen | GLM |
| Page style / clarity | **GLM** | DeepSeek | Qwen |
| Overall wiki author | **DeepSeek** | Qwen | GLM |

## Runtime at the review snapshot

| Model | Started | Phase | Plan | Complete | Wiki size |
| --- | --- | --- | --- | --- | --- |
| qwen3.8-flash | 05:37Z | generating | 38 | 3 | 628K |
| glm-5.3-flash | 06:58Z (3rd attempt) | generating | 27 | 1 | 236K |
| deepseek-v4-flash | 05:37Z | generating | 26 | 23 | 2.4M |

DeepSeek finished 09:38Z (`exit=0`, 33 markdown files including indexes, [quickstart.md](deepseek-v4-flash/quickstart.md)). GLM attempt 1 died on undici `headersTimeout` after ~63 min of planning; attempt 2 with HTTP streaming died in 17s (`wrapModelCall` / GLM `reasoning_content`). Attempt 3: no streaming, patched 1h headersTimeout.

None had scheduled `quickstart.md` first — a shared planning-order mistake.

## DeepSeek V4 Flash — best overall

**Taxonomy:** Compact noun-based map. Architecture (`control-plane`, `overview`, `persistence`, `sandbox-providers`, `sandbox-runtime`, `session-do`, `web-client`). Concepts are nouns (sessions, secrets, environments, automations, managed-skills, models-providers, prebuilt-images). Unique: `workflows/session-diffs.md`.

**Strengths**

1. Best top-level mental model. `architecture/overview.md` covers single-tenant trust, package graph, canonical prompt path, DO/D1 split, change-routing.
2. Compression without usually going shallow. `architecture/control-plane.md` is 159 lines and still has exact crons, queue dispatch, route policies, D1-before-DO.
3. Excellent invariant extraction. `concepts/sessions.md`: four exclusive targets, primary-repo semantics, nested `repo_owner`, D1-before-DO.
4. Broad written coverage (23 pages at review) — quality is less sample-sensitive.
5. Cross-language hazards: stale credential cache, argv git, `SESSION_DIFF_VERSION` sync.

**Failure modes**

1. Compression can invent. `architecture/overview.md` named `BROWSER_AUTH_SECRET` as an encryption-at-rest domain; scoped secrets use `REPO_SECRETS_ENCRYPTION_KEY`. Fix before publishing.
2. No standalone Qwen-style protocol pages (service-auth, SCM providers, sandbox-runtime-protocol, websocket-reconnect).
3. Missed Qwen’s WebSocket bypass warning (upgrade path skips `handleRequest` → no CORS / correlation headers / HTTP log).
4. Weaker related-page graph.

**Style:** Best briefing. Walls of bullets, few tables. Invariant dump at the end is gold for an agent.

Sample: [architecture/control-plane.md](deepseek-v4-flash/architecture/control-plane.md), [architecture/overview.md](deepseek-v4-flash/architecture/overview.md), [concepts/sessions.md](deepseek-v4-flash/concepts/sessions.md).

## Qwen 3.8 Flash — deepest reader, over-expanded editor

**Taxonomy:** 38 pages, finest grain. Extra unique pages: `sandbox-runtime-protocol.md`, `service-auth-and-boundaries.md`, `source-control-providers.md`, `websocket-streaming-and-reconnect.md`, `adding-a-sandbox-provider.md`, `control-plane-integration-tests.md`. Instructions are procedural (file names, regexes, ESLint rules, Mermaid types).

**Strengths**

1. Best hidden-boundary reasoning. Control-plane worker page derives that the WebSocket branch bypasses `handleRequest` (no route policy, preflight, `x-request-id`/`x-trace-id`, HTTP log); admission splits Worker D1 check vs DO socket auth.
2. Best persistence ownership. DO SQLite vs D1 projections vs R2 payloads vs disposable KV; D1 `session_repositories` has identity, not git state.
3. Concurrency/durability, not just schemas (derived automation status, queue leases, compensating R2 cleanup).
4. Strongest provider/runtime boundary: `/app/sandbox_runtime` as a hard contract (`OpenCodeServer` absolute paths).
5. Best specialist destinations for real maintenance tasks.

**Failure modes**

1. Over-splitting. Sandbox concerns spread across six pages.
2. Bad generation order: `system-overview.md` sixth, `quickstart.md` last. Unfinished init is not useful.
3. Pages mirror implementation too closely (357–558 line samples). High drift cost.
4. Throughput: ~1 page / 45–50 min. 38-page plan is impractical for a first wiki.

**Style:** Forensic dump. Highest insight per sentence, lowest scannability. Giant table cells, `defineRoute` snippets. Fine for a specialist page, terrible as default house style.

Samples: [control-plane-worker.md](qwen3.8-flash/architecture/control-plane-worker.md), [data-model-and-persistence.md](qwen3.8-flash/architecture/data-model-and-persistence.md), [sandbox-data-plane.md](qwen3.8-flash/architecture/sandbox-data-plane.md).

## GLM 5.3 Flash — best workflow planner, thin written evidence (at review)

**Taxonomy:** 27 pages, workflow-heavy. Unique: `media-and-attachments.md`, automations as workflow not concept, model-provider-accounts as integration.

**Strengths**

1. Best workflow decomposition: `session-creation`, `prompt-lifecycle`, `sandbox-lifecycle`, `image-builds`, `git-and-pull-requests`, `media-and-attachments`.
2. The one generated control-plane page is excellent: numbered `handleRequest` pipeline, exact crons, queue-prefix dispatch (`open-inspect-github-autofix-` so a similarly named image-build queue is not stolen).
3. Plans trace convergence (web/Slack/GitHub/Linear/automations/child spawn → one init path).
4. Only dedicated media/attachments workflow.
5. Operational constants (`MAX_SPAWN_DEPTH=2`, scheduler budgets) instead of “describe limits”.

**Failure modes**

1. At review, written quality rested on **one page**. Cannot claim repo-wide consistency yet.
2. Category boundaries less coherent (provider accounts as integration; automations as workflow).
3. Protocol boundaries underexposed vs Qwen.
4. Same WebSocket-bypass miss as DeepSeek.

**Style:** Best writer. Numbered pipeline, tables, then depth. Headings every ~15 lines. Diagrams earn their keep. Dense but scannable.

Sample: [control-plane-worker.md](glm-5.3-flash/architecture/control-plane-worker.md).

## Style on the same topic (control plane)

Comparable generated pages:

| | GLM | DeepSeek | Qwen |
| --- | --- | --- | --- |
| Length | 295 lines | 159 lines | 356 lines |
| First 30s | You know the pipeline | You know the system | You’re in `index.ts` |
| Structure | Headings + tables + lists | Prose + mermaid + invariants | Nested `###` + giant tables |
| Voice | Operator explaining a system | Senior eng compressing a design doc | Staff eng narrating the source |
| Agent-useful | Follow the numbered steps | Copy the invariant list | Only if you already know where to look |

## Overlaps, gaps, unique discoveries

| Area | Shared | Best distinctive observation |
| --- | --- | --- |
| Control plane | Entrypoints, policies, crons, queues, D1 | **Qwen:** WS/router bypass consequences |
| Persistence | D1 vs DO SQLite, R2, KV | **Qwen:** D1 identity vs DO git state |
| Orientation | Three tiers, package graph | **DeepSeek:** single-tenant trust + change-routing map |
| Sessions | Targets, lifecycle, children | **DeepSeek:** exclusive targets, nested owner, D1-before-DO |
| Sandbox | Providers, lifecycle, images | **Qwen:** `/app/sandbox_runtime` packaging contract |
| Workflows | Prompt, sandbox, images, PRs | **GLM:** clearest workflow IA |
| Media | Indirect elsewhere | **GLM:** only dedicated page |
| Diff protocol | Runtime mention | **DeepSeek:** standalone `session-diffs.md` |
| Extension tasks | Embedded | **Qwen:** `adding-a-sandbox-provider.md` |

**Common gaps:** quickstart last; control-plane/backend heavy; no strong “change the client socket/reducer safely” workflow (Qwen’s reconnect page is the closest).

Ideal taxonomy: DeepSeek’s 26-page structure + Qwen’s highest-value specialist pages only.

## Throughput vs quality

DeepSeek is **not** cheaper thinking. Control-plane and sessions pages keep constants, ownership, ordering invariants, failure semantics. Speed is selection and compression. The encryption-key slip is the cost of that compression.

Qwen is not padding — the detail is usually real — but 38 pages at ~50 min/page is the wrong first-wiki granularity.

GLM has the best workflow IA and the best page style; the snapshot did not yet prove it across persistence, runtime, integrations, and frontend.

## How to leverage the three families

Stock OpenWiki cannot split models. The fork now can (`manikanda-kumar/openwiki` `6e2fc0f` + GLM timeout `2fc5850`).

| Role | Model | Env |
| --- | --- | --- |
| Planner | DeepSeek | `OPENWIKI_PLANNER_MODEL_ID` |
| Default page writer | GLM | `OPENWIKI_PAGE_MODEL_ID` |
| Specialist prefixes | Qwen | `OPENWIKI_SPECIALIST_MODEL_ID` |

Default specialist prefixes: `architecture/session-`, `integrations/service-auth`, `integrations/source-control`, `workflows/websocket`, `workflows/adding-a-sandbox-provider`, `sandbox-runtime-protocol`.

One page, one writer. No ensemble merge.

## Plan inventories

Full plan dumps: [plans/](plans/).

- DeepSeek 26 pages — see [plans/deepseek-v4-flash.summary.json](plans/deepseek-v4-flash.summary.json)
- GLM 27 pages — [plans/glm-5.3-flash.summary.json](plans/glm-5.3-flash.summary.json)
- Qwen 38 pages — [plans/qwen3.8-flash.summary.json](plans/qwen3.8-flash.summary.json)
