---
name: workspaceonly-preflight
description: Assess whether it is safe to enable `tools.fs.workspaceOnly=true` on your OpenClaw agent without breaking current workflows. Use before tightening filesystem boundaries — generates a path dependency snapshot, classifies inside vs outside workspace usage, identifies which tasks would break first, and recommends whether a trial is worth scheduling now. Does NOT enable workspaceOnly; read-only analysis only.
---

# WorkspaceOnly Preflight

A read-only pre-flight check before enabling `tools.fs.workspaceOnly=true`.

## What this skill does
- Surveys which paths your current workflows actually use
- Classifies dependencies as inside-workspace vs outside-workspace
- Identifies which tasks would fail first if the boundary is enabled
- Returns a go/no-go recommendation with timing advice

**This skill never enables `workspaceOnly`. It only assesses readiness.**

---

## Steps

### 1. Stay read-only
No config changes, no restarts, no gateway modifications during this preflight.

### 2. Collect representative paths
Sample paths from real recent workflows:
- Workspace project files and docs
- Agent memory/config files (if currently in use)
- External OS paths (e.g. `/mnt/c/`, external drives, Windows paths)
- Install-level paths (agent dist, global skills, system configs)

If you have a probe script at `scripts/harness/fs_scope_probe.sh`, run it now.

### 3. Classify path dependencies

| Category | Examples | WorkspaceOnly impact |
|---|---|---|
| High-frequency, inside workspace | project files, docs, memory | ✅ Safe |
| High-frequency, outside workspace | system configs, external drives | 🚨 Will break |
| Occasional, outside workspace | install-level reads, one-off ops | ⚠️ May break |

### 4. Identify first-break tasks
Which tasks would fail immediately after enabling `workspaceOnly=true`?

Common first-break patterns:
- Agent runtime config reads (`~/.openclaw/`, `~/.codex/`)
- Harness scripts outside the workspace
- Any task that explicitly touches external paths

### 5. Return recommendation

Choose one:

**Not ready** — high-frequency external path dependencies exist. Schedule after task mix shifts.

**Conditionally ready** — only occasional external dependencies. Safe for a short trial window if you accept some manual fallbacks.

**Ready** — no meaningful external dependencies in current workflows. Safe to trial.

---

## Output format

```
Path snapshot:
  Inside workspace (high-freq): [list]
  Outside workspace (high-freq): [list]
  Outside workspace (occasional): [list]

First-break tasks if workspaceOnly enabled:
  1. ...
  2. ...

Recommendation: [Not ready / Conditionally ready / Ready]
Suggested timing: [now / after X / not yet]
```

---

## What to do after this preflight

- If **Ready**: enable `workspaceOnly=true` in a short trial window, monitor for unexpected breaks.
- If **Conditionally ready**: identify which external paths you can replace with workspace-local alternatives first.
- If **Not ready**: note the blockers and re-run this preflight after the task mix shifts.
