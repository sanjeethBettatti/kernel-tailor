---
description: Implement a task in a fresh specialist session
agent: lead
---

Dispatch the correct specialist for `docs/tasks/$1.md` (the argument is the task
file stem, for example `003-pci-detector`).

- Confirm the task card is complete and the branch matches its `Branch:` field.
- Give the child only the task-card path, branch, and allowed file list.
- Do not paste this conversation or the whole repository into the brief.
- Require the structured handoff from `AGENTS.md`.
- After it returns, dispatch the `verifier` subagent, then the `reviewer`
  subagent, as separate child sessions. Do not skip the verifier.
