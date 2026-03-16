# Use Cases

Three concrete scenarios where agent-charter skills prevent real failures.

---

## Case 1: Ops / Project lane confusion

**Situation**  
You ask your agent: *"Clean up the project docs and make sure the structure is consistent."*

**What goes wrong without lane-router**  
The agent decides "consistent structure" might include agent configuration files. It reads `~/.openclaw/openclaw.json`, checks the gateway config for doc-related settings, and starts suggesting changes to skill loading order. None of this was asked for.

**What lane-router does**  
At task start, lane-router evaluates: does this require runtime config or external paths? No — it's a workspace doc task. Routes to Project Lane. Runtime config is never consulted.

```
Lane: Project | reason: task involves workspace docs only
```

The agent stays scoped. The docs get cleaned up. Nothing else moves.

**Skill:** [`lane-router`](lane-router/)

---

## Case 2: Harness change spirals out of control

**Situation**  
You ask: *"Add a small helper script that checks whether my active tasks are up to date."*

**What goes wrong without safe-harness-change**  
The agent adds the script. Then notices the task-checking logic could be improved if the lane rules were updated. Updates the lane rules. Then sees that the gateway heartbeat config could be tightened. Edits the gateway config. Then triggers a restart to apply it. Your current session is gone.

**What safe-harness-change does**  
Enforces a single-tranche boundary before writing anything. The requested change (helper script) is allowed. Lane rule changes, gateway config edits, and restarts are all out of scope for this tranche. The script ships. Everything else is explicitly left untouched.

```
Tranche: helper script — scripts/check-tasks.sh
Files changed: scripts/check-tasks.sh
How to use: bash scripts/check-tasks.sh
Not changed: lane rules, gateway config, session state
```

**Skill:** [`safe-harness-change`](safe-harness-change/)

---

## Case 3: workspaceOnly pre-flight

**Situation**  
You want to tighten your agent's filesystem access by enabling `tools.fs.workspaceOnly=true`. You're not sure what will break.

**What goes wrong without workspaceonly-preflight**  
You enable it. Two workflows immediately fail: the agent can no longer read the OpenClaw runtime config it uses for a weekly health check, and a harness script that lives outside the workspace stops running. You spend an hour debugging.

**What workspaceonly-preflight does**  
Runs a read-only path survey first. No config changes, no restarts. Classifies your actual path usage:

```
Path snapshot:
  Inside workspace (high-freq): project files, docs, memory
  Outside workspace (high-freq): ~/.openclaw/ (runtime health check)
  Outside workspace (occasional): ~/scripts/harness-check.sh

First-break tasks if workspaceOnly enabled:
  1. Weekly runtime health check (reads ~/.openclaw/openclaw.json)
  2. Harness check script (lives outside workspace)

Recommendation: Not ready
Suggested timing: after relocating harness-check.sh into workspace
```

You fix the two blockers first. Then enable `workspaceOnly` without surprises.

**Skill:** [`workspaceonly-preflight`](workspaceonly-preflight/)
