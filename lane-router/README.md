# lane-router

An OpenClaw skill that routes each new task into the correct working lane **before** doing any substantial work.

## The problem it solves

AI agents frequently make one of two mistakes:
1. **Over-cautious**: treating ordinary project work as risky system operations
2. **Under-cautious**: wandering into runtime config or system paths when they shouldn't

This skill gives your agent a lightweight decision checkpoint at the start of every task.

## The two lanes

| Lane | Scope | Default? |
|---|---|---|
| **Project Lane** | Workspace-local files, docs, projects | ✅ Yes |
| **Ops Lane** | Agent runtime config, external paths, system ops | ❌ Explicit only |

## Quick start

1. Copy this directory into your OpenClaw workspace skills folder:
   ```bash
   cp -r lane-router ~/.openclaw/workspace/skills/
   ```

2. (Optional) Fill in `references/lanes.md` with your specific paths and risk tiers.

3. Restart OpenClaw. The skill triggers automatically on new task requests.

## What it outputs

A brief routing header before the agent starts work:

```
Lane: Project | reason: task uses workspace-local files only
```
or
```
Lane: Ops | reason: task explicitly requires runtime config | risk: medium
```

## Customizing

See [`references/lanes.md`](references/lanes.md) for a template to define your own:
- Workspace root path
- Ops Lane trigger paths
- Risk tiers
- Custom lane names

## Part of the `openclaw-skills` collection

This skill pairs well with:
- `pm-safe-core` — safe execution protocol for complex tasks
- `safe-harness-change` — incremental harness engineering
- `workspaceonly-preflight` — filesystem boundary pre-flight
