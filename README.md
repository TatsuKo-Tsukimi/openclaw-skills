# openclaw-skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-5-orange)](https://github.com/TatsuKo-Tsukimi/openclaw-skills)
[![OpenClaw](https://img.shields.io/badge/OpenClaw-compatible-blue)](https://openclaw.ai)

**OpenClaw agent skills for workflow governance, safety, and efficiency.**

These skills are framework-level patterns — not platform integrations. They define *how your agent thinks and operates*, not what it connects to. Each one solves a real problem that shows up when running a local AI assistant long-term.

---

## Skills

### [`lane-router`](lane-router/)

> Route tasks into the correct working lane before taking action.

Defines a **Project Lane** (workspace-local, safe, default) and an **Ops Lane** (runtime config, external paths, system ops). Prevents agents from wandering into system config for ordinary project work — or being overly cautious about it.

**Best for:** Anyone running a long-term local agent who wants bounded, predictable behavior.

---

### [`pm-safe-core`](pm-safe-core/)

> Safety-first execution framework for complex tasks.

Implements a **STOP → SEARCH → RECORD → PLAN → ACT** loop with approval gates for dangerous actions, session isolation rules, context flush patterns, and recoverable execution. Designed for tasks that can't easily be undone.

**Best for:** Multi-step tasks, refactors, deploys, anything involving external sends or file deletions.

---

### [`token-efficiency`](token-efficiency/)

> Minimize token usage without sacrificing quality.

A response/context/tool/model policy playbook that keeps agents concise by default and deep only on demand. Includes output shape templates for quick answers, plans, and status updates.

**Best for:** Reducing API costs and context bloat on long-running assistant setups.

---

### [`safe-harness-change`](safe-harness-change/)

> Incremental harness improvements without breaking your current setup.

Enforces a strict non-disruptive tranche boundary: probe scripts, helper scripts, lane rules, and task templates — but no gateway changes, no auth changes, no restarts. Stops the "one small change breaks everything" failure mode.

**Best for:** Agents with a working harness you want to improve incrementally.

---

### [`workspaceonly-preflight`](workspaceonly-preflight/)

> Pre-flight check before tightening filesystem boundaries.

Read-only analysis that classifies your path dependencies (inside vs outside workspace), identifies first-break tasks, and gives a go/no-go recommendation before you enable `workspaceOnly=true`.

**Best for:** Anyone considering tighter filesystem sandboxing who wants to avoid surprises.

---

## Why these exist

Most OpenClaw skills are integrations (Spotify, Notion, email, etc.). These are different — they're about the **constitutional layer** of your agent: how it decides what to work on, how it handles risk, how it manages context, and how it evolves its own harness safely.

---

## Installation

Copy any skill directory into your OpenClaw workspace skills folder:

```bash
cp -r lane-router ~/.openclaw/workspace/skills/
```

OpenClaw auto-detects and loads it on next restart. No configuration required.

---

## More planned

- `memory-hygiene` — structured daily/weekly memory maintenance workflow
- `agent-journal` — lightweight task logging and retrospective pattern

---

## License

MIT
