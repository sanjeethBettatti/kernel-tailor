---
description: Run the architecture gate for a task
agent: architect
subagent: true
---

Read `docs/tasks/$1.md` (the argument is the task file stem, for example
`003-pci-detector`) and `docs/architecture.md`. Review the contract,
dependencies, allowed scope, boot-safety impact, and acceptance criteria.

Return `APPROVE` or `REVISE` with specific reasons. Write or update an ADR only
if the decision is durable. Do not edit code.
