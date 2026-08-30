# OpenWiki bench — OpenCode Go flash models + host agents

Isolated OpenWiki `--init` runs of this repo at git SHA `32470cc2`. Flash models
used stock `openwiki@0.4.3` through OpenCode Go (`https://opencode.ai/zen/go/v1`,
openai-compatible). Grok 4.6 used the fork's host-agent integration
(`host-agent/grok`). Host Qwen and the GLM finish used OpenCode CLI + OpenWiki
MCP (`host-agent/opencode`).

| Dir | Model | Runtime | Role in later fork routing |
| --- | --- | --- | --- |
| `qwen3.8-flash-opencode/` | Qwen 3.8 Flash | OpenCode host + MCP | Default page writer (depth + finished 19-page wiki) |
| `glm-5.3-flash/` | GLM 5.3 Flash | hybrid native `--init` + OpenCode host resume | Workflow specialist (finished 28-page wiki) |
| `deepseek-v4-flash/` | DeepSeek V4 Flash | native `--init` | Planner (taxonomy) |
| `grok-4.6/` | Grok 4.6 | host-agent MCP | Style / orientation fallback |
| `qwen3.8-flash/` | Qwen 3.8 Flash | native `--init` | Forensic sample only (dead 9/38) |

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
grok --always-approve --cwd /tmp/grok-4.6 -m grok-4.6 --max-turns 200 \
  -p "Resume OpenWiki init via MCP from this checkout"
```

Host Qwen (OpenCode 1.18.25 + fork MCP, worktree `/tmp/qwen3.8-flash-opencode`):

```bash
opencode run --auto --dir /tmp/qwen3.8-flash-opencode \
  -m opencode-go/qwen3.8-flash --title openwiki-qwen-host \
  "Initialize this repository's OpenWiki from the current source and tests."
```

Host GLM remaining pages (same worktree `/tmp/glm-5.3-flash`, resume interrupted
init run `554c65a3` — do **not** `openwiki_begin mode=update` while `.run.json`
is still an init run):

```bash
opencode run --auto --dir /tmp/glm-5.3-flash \
  -m opencode-go/glm-5.3-flash --title openwiki-glm-host-remaining \
  "Resume the interrupted OpenWiki init. Do not start a new wiki."
```

Each run used a detached git worktree under `/tmp/<model>/` so `AGENTS.md` /
`CLAUDE.md` snippets and `openwiki/` output did not dirty `main`. `CLAUDE.md` is
a symlink to `AGENTS.md` on this repo; worktrees replaced it with a real file
before init (OpenWiki otherwise duplicates managed markers).

GLM needed retries: first run died on Node/undici `headersTimeout` after ~63
minutes of planning; streaming died in 17s (`wrapModelCall` / `reasoning_content`).
Third native run used a patched 1h headersTimeout, no streaming, wrote 23/28,
then stalled ~5h on `git-auth-and-pull-requests.md`. Host MCP resume finished
the remaining five pages. Grok's first host-agent pass hit `Max turns reached`
at 12/24; resume with `--max-turns 200` finished.

## Snapshot

Recorded 2026-08-30. DeepSeek, Grok, host Qwen, and GLM finished. Native Qwen
died mid-restore.

| Model | Started (UTC) | Finished | Plan pages | Notes |
| --- | --- | --- | --- | --- |
| qwen3.8-flash-opencode | 16:39:17 | 19:26:15 (~2.8h) | 19 | host-agent complete; [quickstart](qwen3.8-flash-opencode/quickstart.md) |
| glm-5.3-flash | 06:58:40 + host 13:10:50 | 13:48:07 | 28 | hybrid complete; [quickstart](glm-5.3-flash/quickstart.md) |
| deepseek-v4-flash | 05:37:24 | 09:38:37 (~4h) | 26 | `exit=0`, [quickstart](deepseek-v4-flash/quickstart.md) |
| grok-4.6 | ~14:40 | 15:39:33 | 24 | host-agent complete after resume; [quickstart](grok-4.6/quickstart.md) |
| qwen3.8-flash | 05:37:24 | died 15:08 | 38 | restore-missing-file; 9/38 |

`*.run.log` is the wrapper log (start/exit only; gitignored as `*.log`).
`plans/*.summary.json` dumps each plan or finished page manifest.

## Do not treat this as production wiki

This is a model-quality artifact. Do not point `AGENTS.md` at these folders.
The fork `manikanda-kumar/openwiki` can later run one wiki with DeepSeek
planning, host Qwen writing via OpenCode MCP, host GLM on workflow pages, Grok
as style fallback.
