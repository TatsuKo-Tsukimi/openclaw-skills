---
name: safe-harness-change
version: 1.0.0
author: TatsuKo-Tsukimi
description: Make low-risk, incremental improvements to your OpenClaw agent harness without disrupting the current working setup. Use when adding opt-in probes, local helper scripts, lane rules, task-entry scaffolds, or other lightweight harness improvements. This skill enforces a strict non-disruptive tranche boundary — it will not touch gateway defaults, auth, sandbox settings, workspaceOnly, or trigger a restart.
---

# Safe Harness Change

Ship harness improvements in small, non-disruptive tranches.

## When to use this skill
- You want to improve how your agent handles tasks (routing, validation, observability)
- The change is opt-in and should not alter current default behavior
- You want to avoid the "one small change breaks everything" failure mode

## What counts as a safe tranche

✅ Allowed:
- Opt-in probe script
- Local helper script (manually invoked)
- Task-entry template
- Lane rule addition
- Local validation helper
- New docs or memory files

❌ Not allowed in this skill:
- Gateway default behavior changes
- Auth / token / group config changes
- Sandbox enablement
- `tools.fs.workspaceOnly=true`
- Anything requiring a restart
- Mixing multiple concerns in one tranche

## Steps

1. **Confirm scope** — does this task stay within a safe tranche? If not, stop and use a different approach.
2. **Pick the change type** — probe, helper script, template, lane rule, or validation helper.
3. **Keep it narrow** — one tranche, one layer, no opportunistic cleanup.
4. **Implement only what is directly usable** — don't write docs as the main deliverable.
5. **Make it manually runnable** — the result should be testable without restarting anything.
6. **Verify no default behavior change** — confirm existing workflows are unaffected.
7. **Summarize** — what was added, how to use it, what was intentionally left untouched.

## Output format

```
Tranche: [what was shipped]
Files changed: [list]
How to use: [1-2 lines]
Not changed: [what was intentionally left alone]
```

## Why tranches matter

Agent harness engineering tends to expand. A "small helper script" turns into a config change, which turns into a lane redesign, which requires a restart. This skill exists to prevent that chain reaction.

One tranche = one layer = one concern = no surprises.
