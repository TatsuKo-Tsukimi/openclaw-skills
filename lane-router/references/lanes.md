# lanes.md — Lane Configuration Template

Copy this file and fill in your own paths and rules.

---

## Your Workspace Root

```
WORKSPACE=/path/to/your/workspace
```

Project Lane covers everything inside `WORKSPACE`.

---

## Your Ops Lane Trigger Paths (fill in)

```
AGENT_CONFIG=~/.openclaw/openclaw.json
AGENT_RUNTIME=~/.openclaw/
INSTALL_DIR=/path/to/openclaw/install
EXTERNAL_PATHS=
  /mnt/c/**
  /mnt/d/**
  ~/Downloads/**
```

---

## Your Ops Lane Trigger Actions (fill in)

List the action categories that always require Ops Lane in your setup:

- [ ] Gateway restart
- [ ] Auth token changes
- [ ] Security / sandbox config
- [ ] External service credentials
- [ ] System package installs
- [ ] SSH / firewall operations

---

## Risk Tiers (optional)

If you want finer-grained risk control, define tiers:

| Tier | Examples | Requires |
|---|---|---|
| Low | Read config, inspect runtime status | Ops Lane only |
| Medium | Modify config, soft restart | Ops Lane + explicit intent |
| High | Auth/token changes, delete/reset, external sends | Ops Lane + explicit confirmation |

---

## Custom Lane Names (optional)

If "Project Lane" / "Ops Lane" don't fit your context, rename them here and update `SKILL.md`:

```
SAFE_LANE_NAME=Workspace Lane
OPS_LANE_NAME=System Lane
```
