# agent-charter

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-5-orange)](https://github.com/TatsuKo-Tsukimi/agent-charter)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-compatible-blue)](https://openclaw.ai)

> **Most skills extend what your agent CAN do. These define what it SHOULD do — before it starts.**

**Pre-flight governance skills for OpenClaw.** A harness engineering toolkit that intervenes *before* your agent executes — routing tasks, structuring safe execution, controlling token cost, managing incremental harness evolution, and checking filesystem boundary readiness.

No API keys. No external services. No configuration. Pure behavioral protocols.

---

## Who this is for

- You run OpenClaw as a long-term local assistant and want **predictable, bounded behavior**
- You've had an agent wander into system config, burn tokens re-reading files, or break things with "one small harness change"
- You want your agent to **think before it acts**, not just act

---

## What's inside

| Skill | What it does | Intervenes |
|---|---|---|
| [`lane-router`](lane-router/) | Routes tasks into Project Lane (safe default) or Ops Lane (explicit only) | Before any work starts |
| [`pm-safe-core`](pm-safe-core/) | STOP→SEARCH→RECORD→PLAN→ACT loop + safety gates for destructive actions | Before complex tasks |
| [`token-efficiency`](token-efficiency/) | Keeps responses concise, context minimal, tool calls parallel | Throughout execution |
| [`safe-harness-change`](safe-harness-change/) | Enforces single-tranche harness changes — no gateway, no restarts | Before any harness edit |
| [`workspaceonly-preflight`](workspaceonly-preflight/) | Read-only path dependency snapshot + go/no-go before enabling `workspaceOnly` | Before config tightening |

→ See [OVERVIEW.md](OVERVIEW.md) for how the five skills form a complete decision chain.

---

## Skills

### [`lane-router`](lane-router/)

Route tasks into the correct working lane before doing any work.

Defines two lanes: **Project Lane** (workspace-local files, safe, default) and **Ops Lane** (runtime config, external paths, system ops — explicit only). Prevents the common failure of agents touching system config for ordinary project work, or being over-cautious about routine tasks.

```
Lane: Project | reason: task uses workspace-local files only
Lane: Ops | reason: task explicitly requires runtime config | risk: medium
```

**Best for:** Anyone who wants bounded, predictable agent behavior.

---

### [`pm-safe-core`](pm-safe-core/)

Safety-first execution framework for complex or risky tasks.

Implements **STOP → SEARCH → RECORD → PLAN → ACT** — a structured loop that prevents the agent from diving into multi-step work without a plan. Includes approval gates for deletions, external sends, and irreversible actions. Designed for tasks that can't easily be undone.

**Best for:** Refactors, deploys, multi-file changes, anything involving external sends or file deletions.

---

### [`token-efficiency`](token-efficiency/)

Keep agents concise by default, deep only on demand.

A response/context/tool/model policy playbook. Conclusion first, no filler, summarize before reasoning, parallelize tool calls, start cheap and escalate only when needed. Includes output shape templates for quick answers, plans, and status updates.

**Best for:** Long-running assistant setups where context bloat and API costs accumulate.

---

### [`safe-harness-change`](safe-harness-change/)

Incremental harness improvements without disrupting what's working.

Enforces a strict **single-tranche boundary**: probe scripts, helper scripts, lane rules, task templates — but never gateway defaults, auth, sandbox settings, or restarts. Stops the "one small change breaks everything" failure mode cold.

**Best for:** Anyone iterating on a working harness who wants to stay safe.

---

### [`workspaceonly-preflight`](workspaceonly-preflight/)

Pre-flight check before tightening filesystem boundaries.

Read-only analysis of your current path dependencies. Classifies inside-workspace vs outside-workspace usage, identifies which tasks would break first if `workspaceOnly=true` were enabled, and returns a go/no-go recommendation. Never enables the setting itself.

**Best for:** Anyone considering filesystem sandboxing who wants to avoid surprises.

---

## Installation

```bash
# Single skill
cp -r lane-router ~/.openclaw/workspace/skills/

# Full set
cp -r lane-router pm-safe-core token-efficiency safe-harness-change workspaceonly-preflight \
  ~/.openclaw/workspace/skills/
```

OpenClaw auto-detects and loads skills from the workspace `skills/` directory on next restart. No configuration required.

---

## Roadmap

- `memory-hygiene` — structured memory maintenance: promote daily logs to long-term memory, prune stale context
- `session-discipline` — lightweight session start/end protocol for everyday use (lighter than pm-safe-core)

---

## License

MIT
