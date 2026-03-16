# Use Cases

Three failure patterns that agent-charter intercepts.

---

## Case 1: Ops / Project lane confusion

**Problem**  
An agent asked to "clean up project docs" starts reading runtime config files it was never meant to touch.

**What goes wrong**  
The agent decides "consistent structure" might include agent configuration. It reads `~/.openclaw/openclaw.json`, consults gateway settings, and begins suggesting changes to skill loading order. The actual task — organizing workspace docs — is delayed or abandoned.

**Which skill intercepts it**  
`lane-router` fires at task start. It evaluates whether the task requires runtime config or external paths. It does not. The task routes to Project Lane and the agent never accesses runtime config.

**What outcome changes**  
The docs are organized. Runtime config is never read. The routing decision is logged in one line before any work begins:

```
Lane: Project | reason: task involves workspace docs only
```

---

## Case 2: Harness change spirals out of control

**Problem**  
A request for "one small helper script" turns into a gateway config change and a session-breaking restart.

**What goes wrong**  
The agent adds the requested script, then notices the lane rules could be improved to make the script more useful, then sees the gateway heartbeat config could be tightened, then triggers a restart to apply everything. The current session is lost.

**Which skill intercepts it**  
`safe-harness-change` enforces a single-tranche boundary before any file is written. The helper script is in scope. Lane rule changes, gateway config edits, and restarts are all explicitly out of scope for this tranche.

**What outcome changes**  
The script ships. Everything else is left untouched. The tranche summary makes the boundary explicit:

```
Tranche: helper script
Files changed: scripts/check-tasks.sh
How to use: bash scripts/check-tasks.sh
Not changed: lane rules, gateway config, session state
```

---

## Case 3: workspaceOnly enabled without pre-flight

**Problem**  
`tools.fs.workspaceOnly=true` is enabled without knowing which workflows depend on external paths. Two workflows break immediately.

**What goes wrong**  
A weekly runtime health check reads `~/.openclaw/openclaw.json`. A harness script lives outside the workspace. Both stop working. Debugging takes an hour.

**Which skill intercepts it**  
`workspaceonly-preflight` runs a read-only path survey before any config change. It classifies actual path usage across recent workflows and returns a recommendation.

**What outcome changes**  
The two blockers are identified before `workspaceOnly` is enabled. The config change is deferred until the external dependencies are resolved:

```
Path snapshot:
  Inside workspace (high-freq):   project files, docs, memory
  Outside workspace (high-freq):  ~/.openclaw/ (runtime health check)
  Outside workspace (occasional): ~/scripts/harness-check.sh

First-break tasks:
  1. Weekly runtime health check
  2. Harness check script

Recommendation: Not ready
Next step: relocate harness-check.sh into workspace, then re-run preflight
```
