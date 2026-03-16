# openclaw-skills

**A collection of OpenClaw agent skills for workflow governance, safety, and efficiency.**

These skills solve real problems I encountered running a local AI assistant long-term. They're framework-level patterns — not platform integrations — so they should work for most OpenClaw setups.

---

## Skills in this repo

### [`lane-router`](lane-router/)

> Route tasks into the correct working lane before taking action.

Prevents a common failure mode: agents treating ordinary project work as system operations (or vice versa). Defines a **Project Lane** (workspace-local, safe, default) and an **Ops Lane** (runtime config, external paths, system work).

**Use it when:**
- You want your agent to stay in a bounded workspace by default
- You need a lightweight decision point before starting new tasks
- You're tired of your agent touching config files for no reason

---

## Why these exist

Most OpenClaw skills are integrations (Spotify, Notion, etc.). These are different — they're about **how your agent thinks and operates**, not what it connects to.

Think of them as constitutional rules for your agent's behavior.

---

## Installation

Copy any skill directory into your OpenClaw workspace skills folder:

```bash
cp -r lane-router ~/.openclaw/workspace/skills/
```

OpenClaw will auto-detect and load it on next restart.

---

## More skills coming

Planned additions:
- `pm-safe-core` — safe execution protocol for complex multi-step tasks
- `token-efficiency` — minimize token burn without sacrificing quality
- `safe-harness-change` — incremental harness engineering without breaking things
- `workspaceonly-preflight` — pre-flight check before tightening filesystem boundaries

---

## License

MIT
