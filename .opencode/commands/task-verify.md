---
description: Verify a task against its acceptance criteria
agent: verifier
subagent: true
---

Read `docs/tasks/$1.md`. Run every acceptance command exactly. Check
`git diff --name-only` against the allowed scope. Return PASS or FAIL with exact
command output and any out-of-scope files. Do not edit files.
