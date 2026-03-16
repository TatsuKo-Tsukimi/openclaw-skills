# agent-charter

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-5-orange)](https://github.com/TatsuKo-Tsukimi/agent-charter)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-compatible-blue)](https://openclaw.ai)
[![Release](https://img.shields.io/github/v/release/TatsuKo-Tsukimi/agent-charter)](https://github.com/TatsuKo-Tsukimi/agent-charter/releases)

**Agent Charter is not a tool bundle. It is a decision layer for OpenClaw agents.**

---

### What is agent-charter?

A set of five OpenClaw skills that govern **how your agent decides before it acts** — not what it connects to. Each skill is a behavioral protocol that fires at the start of a task, before execution begins.

### Why is this harness engineering?

Because the failures it prevents happen at the harness layer: an agent that routes tasks incorrectly, executes without a safety plan, burns tokens on every turn, or breaks its own setup with one stray config change. These are structural problems, not tool problems. Skills that add APIs don't fix them.

### What problems does it solve?

- Agent starts touching runtime config for an ordinary project task → **lane-router**
- Agent dives into a multi-step destructive task without a plan → **pm-safe-core**
- Context keeps growing; every response re-reads the same files → **token-efficiency**
- "One small harness fix" requires a restart and breaks the current session → **safe-harness-change**
- You want to enable `workspaceOnly=true` but don't know what will break → **workspaceonly-preflight**

### How is it different from normal skills?

Most skills extend what your agent **can** do (new tools, new APIs, new integrations).  
These skills define what it **should** do — and enforce it before execution starts.

No API keys. No external services. No configuration. Pure behavioral protocols.

---

## Real Use Cases

→ Full detail in [use-cases.md](use-cases.md)

**Case 1: Ops/Project lane confusion**
You ask your agent to "clean up the project docs." It starts reading `~/.openclaw/openclaw.json` because it decided the task might involve agent config. `lane-router` catches this at the start: the task is workspace-local, stays in Project Lane, runtime config is never touched.

**Case 2: Harness change spirals out of control**
You ask for "a small helper script to check task status." The agent adds the script, then decides to also update the lane rules, then notices the gateway config could be improved, then triggers a restart. `safe-harness-change` enforces one tranche, one layer — the script ships, nothing else moves.

**Case 3: workspaceOnly pre-flight**
You want to tighten filesystem access by enabling `workspaceOnly=true`. `workspaceonly-preflight` runs a read-only survey first: it finds that two high-frequency workflows still depend on external paths. It returns "not ready" with the specific blockers — before you touch any config.

---

## Skills

| Skill | Fires when | Prevents |
|---|---|---|
| [`lane-router`](lane-router/) | Every new task | Wrong-lane execution |
| [`pm-safe-core`](pm-safe-core/) | Complex / risky tasks | Unplanned destructive work |
| [`token-efficiency`](token-efficiency/) | Throughout execution | Context bloat, cost waste |
| [`safe-harness-change`](safe-harness-change/) | Any harness edit | Scope creep, breakage |
| [`workspaceonly-preflight`](workspaceonly-preflight/) | Before config tightening | Surprise workflow breaks |

→ See [OVERVIEW.md](OVERVIEW.md) for how the five skills connect as a decision chain.  
→ See [FAQ.md](FAQ.md) for: why not a system prompt, why skills over policy files, safety vs productivity.

---

### `lane-router`

Routes every new task into **Project Lane** (workspace-local, safe, default) or **Ops Lane** (runtime config, external paths — explicit only). Returns a one-line routing header before any work begins.

```
Lane: Project | reason: task uses workspace-local files only
Lane: Ops | reason: task explicitly requires runtime config | risk: medium
```

---

### `pm-safe-core`

**STOP → SEARCH → RECORD → PLAN → ACT.** A structured execution loop for complex tasks. Requires approval before deletions, external sends, or irreversible actions. Flushes recoverable state to file before context gets long.

---

### `token-efficiency`

Conclusion first. No filler. Summarize before reasoning. Parallelize independent tool calls. Start on the cheaper model, escalate only when triggered. Includes output shape templates for quick answers, plans, and status updates.

---

### `safe-harness-change`

Single-tranche boundary enforcement. Probe scripts, helper scripts, lane rules, task templates — allowed. Gateway defaults, auth config, sandbox settings, restarts — blocked. One concern per change.

---

### `workspaceonly-preflight`

Read-only path dependency survey. Classifies workspace-internal vs workspace-external usage, identifies first-break tasks, and returns go / conditionally ready / not ready. Never enables the setting itself.

---

## Installation

```bash
# Single skill
cp -r lane-router ~/.openclaw/workspace/skills/

# Full set
cp -r lane-router pm-safe-core token-efficiency safe-harness-change workspaceonly-preflight \
  ~/.openclaw/workspace/skills/
```

OpenClaw auto-detects and loads skills from the workspace `skills/` directory on next restart.

---

## Roadmap

- `memory-hygiene` — promote daily logs to long-term memory, prune stale context
- `session-discipline` — lightweight session start/end protocol (lighter than pm-safe-core)

---

## License

MIT
