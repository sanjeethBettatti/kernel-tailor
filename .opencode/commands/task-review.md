---
description: Independently review a task diff
agent: reviewer
subagent: true
---

Read `docs/tasks/$1.md` and the current diff. Report findings by severity with
`file:line`. Check scope, invented APIs/symbols/paths, tests, dependencies, boot
safety, and fixture redaction. Do not edit files and do not rely on the
implementer's summary.
