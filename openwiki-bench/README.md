# OpenWiki bench — three OpenCode Go flash models

Isolated `openwiki@0.4.3 --init` runs of this repo through OpenCode Go
(`https://opencode.ai/zen/go/v1`, openai-compatible). Same checkout (`32470cc`),
same provider, different model IDs.

| Dir | Model | Role in later fork routing |
| --- | --- | --- |
| `deepseek-v4-flash/` | DeepSeek V4 Flash | Planner (taxonomy) |
| `glm-5.3-flash/` | GLM 5.3 Flash | Default page writer (style) |
| `qwen3.8-flash/` | Qwen 3.8 Flash | Specialist pages (forensic) |

Comparison write-up: [COMPARISON.md](COMPARISON.md).

## How these were generated

```bash
OPENWIKI_PROVIDER=openai-compatible
OPENAI_COMPATIBLE_BASE_URL=https://opencode.ai/zen/go/v1
OPENWIKI_MODEL_ID=<model>
openwiki --init --print --modelId <model>
```

Each run used a detached git worktree under `/tmp/<model>/` so `AGENTS.md` /
`CLAUDE.md` snippets and `openwiki/` output did not dirty `main`. `CLAUDE.md` is
a symlink to `AGENTS.md` on this repo; worktrees replaced it with a real file
before init (OpenWiki otherwise duplicates managed markers).

GLM needed two retries: first run died on Node/undici `headersTimeout` after ~63
minutes of planning; streaming died in 17s (`wrapModelCall` / `reasoning_content`).
Third run used a patched 1h headersTimeout, no streaming.

## Snapshot

Recorded 2026-08-29. DeepSeek finished. Qwen and GLM were still generating when
this directory was first committed; later copies overwrite those folders.

| Model | Started (UTC) | Finished | Plan pages | Notes |
| --- | --- | --- | --- | --- |
| deepseek-v4-flash | 05:37:24 | 09:38:37 (~4h) | 26 | `exit=0`, quickstart present |
| qwen3.8-flash | 05:37:24 | — | 38 | Slow (~1 page / 45–50 min) |
| glm-5.3-flash | 06:58:40 (3rd attempt) | — | 27 | Timeout-patched client |

`*.run.log` is the wrapper log (start/exit only). `plans/*.summary.json` is a
dump of each `.run.json` plan (titles, seeds, instructions) from mid-run.

## Do not treat this as production wiki

This is a model-quality artifact. Do not point `AGENTS.md` at these folders.
The fork `manikanda-kumar/openwiki` (`6e2fc0f` + `2fc5850`) can later run one
wiki with DeepSeek planning, GLM writing, Qwen on specialist prefixes.
