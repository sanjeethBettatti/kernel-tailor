# Contracts

Contracts are the stable interface between the probe, planner, builder, and
report layers. They are versioned and reviewed by the architect.

| Contract | Defines |
|---|---|
| [manifest.md](manifest.md) | what the probe records about the machine |
| [plan.md](plan.md) | what the planner decides and why |
| [report.md](report.md) | what the user sees and what may be claimed |

## Rules

- JSON is the wire format; TOML is the policy format.
- Every document carries `schema_version`.
- Additive changes are backward-compatible; semantic changes require a new
  version and a migration note.
- Golden tests compare fixtures against expected documents.
- Redaction is part of the manifest contract, not an afterthought.

The authoritative Rust types will live in `kt-core` after TASK-001. Until then,
these documents describe intent, not generated schema.
