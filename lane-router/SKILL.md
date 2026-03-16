---
name: lane-router
description: Route each new task into the correct working lane before taking action. Use when a new task arrives and you need to decide whether it belongs in the default Project Lane (workspace-first, read-only-first, project-task mode) or the Ops Lane (agent runtime/config, workspace-external paths, system operations, gateway/auth/security/sandbox/service/runtime work). Triggers on any new request that touches system config, external paths, or runtime concerns.
---

# Lane Router

Route each new task **before** doing substantial work.

This skill helps agents avoid a common failure mode: accidentally treating ordinary project tasks as system operations (or vice versa), which leads to over-expanded context, unsafe side effects, and unnecessary tool use.

## The Two Lanes

| | **Project Lane** | **Ops Lane** |
|---|---|---|
| **Scope** | Workspace-local assets only | Agent runtime, system config, external paths |
| **Default?** | ✅ Yes | ❌ Explicit switch only |
| **Risk level** | Low | Medium–High |
| **First action** | Read workspace files | Confirm scope + check config |

---

## Routing Decision

### Default to Project Lane when the task involves:
- Files and projects inside the workspace directory
- Workspace docs, memory, skills, notes
- Project analysis, planning, implementation, cleanup, or validation
- Research, writing, or debugging that stays within the workspace
- Anything that doesn't mention runtime config, system paths, or agent internals

### Switch to Ops Lane only when the task explicitly involves:
- Agent runtime config (e.g. `~/.openclaw/openclaw.json`, `~/.codex/**`)
- Install-level skills, dist, or bundled system assets
- External OS paths (e.g. `/mnt/c/**`, Windows mapped drives)
- Gateway, auth, security, sandbox, service, or runtime reconfiguration
- Host-level operations (SSH, firewall, system packages, process management)

> **Key principle:** A task mentioning the agent's name casually does **not** trigger Ops Lane. Only explicit system/config/external-path work does.

---

## Routing Steps

1. Read the task text.
2. Ask: "Can this be completed using workspace-local assets only?"
   - Yes → **Project Lane**
   - No (requires runtime config or system paths) → **Ops Lane**
3. If switching to Ops Lane: **say so explicitly** before continuing.
4. Keep initial context load minimal:
   - First: relevant workspace memory or notes
   - Then: relevant workspace docs or plans
   - Then: relevant project files
5. Do not expand the search area speculatively.

---

## Output Format

Return a brief routing header before starting the task:

```
Lane: Project | reason: <one line>
```

or

```
Lane: Ops | reason: <one line> | risk: low/medium/high
```

---

## Examples

| Task | Lane | Reason |
|---|---|---|
| "Summarize the notes in docs/research.md" | Project | Workspace file only |
| "Update the task plan in temp/plan.md" | Project | Workspace asset |
| "Help me understand this project's architecture" | Project | Analysis, workspace-local |
| "Fix the agent's OpenClaw config file" | Ops | Explicitly touches runtime config |
| "Restart the gateway and test the connection" | Ops | Runtime service operation |
| "Read my emails from D:\\claw\\mail" | Ops | External path access |
| "My OpenClaw isn't responding" | Ops | System/runtime diagnosis |

---

## Boundaries

- Do **not** default to Ops Lane just because the word "OpenClaw" or "agent" appears.
- Do **not** read external paths unless the task explicitly requires them.
- Do **not** treat ordinary project work as system operations.
- Do **not** perform high-risk actions during routing itself.
- If scope is genuinely ambiguous: **default to Project Lane and ask** rather than assuming Ops.

---

## Customizing for Your Setup

This skill uses generic lane names. To adapt it:

1. Replace "Project Lane" and "Ops Lane" with names that fit your agent's context.
2. Update the Ops Lane trigger paths to match your actual runtime directories.
3. Add a `references/lanes.md` with your specific path inventory and lane rules.

See `references/lanes.md` for a template you can fill in.
