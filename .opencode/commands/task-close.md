---
description: Close a task after verification and review
agent: lead
---

Read the verifier and reviewer reports for `docs/tasks/$1.md` (the argument is
the task file stem, for example `003-pci-detector`).

- If both pass, update the task status and `docs/status.md`, then summarize the
  change for the human to merge.
- If either fails, send the exact findings back to the same implementer session
  for one fix round, then re-run verification and review.
- Never merge without human approval.
- Ask the architect to record durable decisions in an ADR rather than leaving
  them in chat.
