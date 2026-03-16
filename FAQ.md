# FAQ

---

**Why not just write a better system prompt?**

A system prompt sets intent. It tells the agent what it is and what it should generally do. But it doesn't enforce behavior at decision points — it doesn't fire when a new task arrives, check whether that task should touch runtime config, or gate a harness change before it happens. Skills do. They activate at specific moments in the agent's execution loop and provide structured decision logic, not general guidance.

---

**Why use skills instead of one long policy file?**

A single policy file grows without structure, gets stale, and is hard to reason about. Skills are modular: each one covers one concern, has a defined trigger, and can be updated or replaced independently. `lane-router` changes without touching `pm-safe-core`. `token-efficiency` applies to all tasks; `workspaceonly-preflight` applies to one. Composability beats length.

---

**Is this a safety layer or a productivity layer?**

Both, and the distinction matters less than it seems. `pm-safe-core` and `safe-harness-change` are safety-oriented — they prevent irreversible mistakes. `lane-router` and `token-efficiency` are productivity-oriented — they keep execution focused and cheap. `workspaceonly-preflight` is neither until you need it, then it's critical. The point is they all intervene *before* the problem occurs, not after.

---

**Does this replace sandboxing or permissions?**

No. Sandboxing and permissions are enforced at the OS or runtime level — they're hard constraints. agent-charter skills are behavioral protocols — they're soft constraints that shape how the agent reasons before acting. They work best together: hard constraints define what is possible; agent-charter defines what the agent should choose to do within those constraints.
