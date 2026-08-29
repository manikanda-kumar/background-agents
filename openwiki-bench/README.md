# OpenWiki bench — OpenCode Go flash models + Grok 4.6

Isolated OpenWiki `--init` runs of this repo at git SHA `32470cc2`. Flash models
used stock `openwiki@0.4.3` through OpenCode Go (`https://opencode.ai/zen/go/v1`,
openai-compatible). Grok 4.6 used the fork's host-agent integration
(`host-agent/grok`): Grok researches and writes; OpenWiki MCP owns page jobs.

| Dir | Model | Runtime | Role in later fork routing |
| --- | --- | --- | --- |
| `deepseek-v4-flash/` | DeepSeek V4 Flash | native `--init` | Planner (taxonomy) |
| `grok-4.6/` | Grok 4.6 | host-agent MCP | Default page writer (style + finished wiki) |
| `glm-5.3-flash/` | GLM 5.3 Flash | native `--init` | Workflow-page fallback (style; incomplete) |
| `qwen3.8-flash/` | Qwen 3.8 Flash | native `--init` | Specialist pages (forensic; incomplete) |

Comparison write-up: [COMPARISON.md](COMPARISON.md).

## How these were generated

Flash models:

```bash
OPENWIKI_PROVIDER=openai-compatible
OPENAI_COMPATIBLE_BASE_URL=https://opencode.ai/zen/go/v1
OPENWIKI_MODEL_ID=<model>
openwiki --init --print --modelId <model>
```

Grok 4.6 (fork CLI + Grok Build, worktree `/tmp/grok-4.6`):

```bash
# OpenWiki MCP installed into Grok; then:
grok --always-approve --cwd /tmp/grok-4.6 -m grok-4.6 --max-turns 200 \
  -p "Resume OpenWiki init via MCP from this checkout"
```

Each run used a detached git worktree under `/tmp/<model>/` so `AGENTS.md` /
`CLAUDE.md` snippets and `openwiki/` output did not dirty `main`. `CLAUDE.md` is
a symlink to `AGENTS.md` on this repo; worktrees replaced it with a real file
before init (OpenWiki otherwise duplicates managed markers).

GLM needed two retries: first run died on Node/undici `headersTimeout` after ~63
minutes of planning; streaming died in 17s (`wrapModelCall` / `reasoning_content`).
Third run used a patched 1h headersTimeout, no streaming. Grok's first host-agent
pass hit `Max turns reached` at 12/24; resume with `--max-turns 200` finished.

## Snapshot

Recorded 2026-08-29. DeepSeek and Grok finished. Qwen and GLM were still
generating (Qwen later died mid-restore; GLM still crawling) when those folders
were first committed; later copies overwrite those folders.

| Model | Started (UTC) | Finished | Plan pages | Notes |
| --- | --- | --- | --- | --- |
| deepseek-v4-flash | 05:37:24 | 09:38:37 (~4h) | 26 | `exit=0`, [quickstart](deepseek-v4-flash/quickstart.md) |
| grok-4.6 | ~14:40 | 15:39:33 | 24 | host-agent complete after resume; [quickstart](grok-4.6/quickstart.md) |
| qwen3.8-flash | 05:37:24 | — | 38 | Dead 15:08Z restore-missing-file; 9/38 |
| glm-5.3-flash | 06:58:40 (3rd attempt) | — | 28 | Manual restart still generating |

`*.run.log` is the wrapper log (start/exit only; gitignored as `*.log`).
`plans/*.summary.json` dumps each plan (titles, seeds, instructions). Grok's
summary is from the finished page manifest.

## Do not treat this as production wiki

This is a model-quality artifact. Do not point `AGENTS.md` at these folders.
The fork `manikanda-kumar/openwiki` can later run one wiki with DeepSeek
planning, Grok writing, Qwen on specialist prefixes (GLM as workflow fallback).
