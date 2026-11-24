# TASK-NNN — <title>

> Copy this file to `docs/tasks/NNN-<slug>.md` and fill every section.
> A task card is a contract between the lead and one implementer, not a wishlist.

Status: proposed
Owner: <agent-id>
Branch: task/NNN-<slug>
Depends on: none
Contract impact: none
Human approval: none

## Goal

One paragraph. What will exist after this task that does not exist now?

## Why now

Why this task, and why before the tasks that depend on it.

## Allowed files

- `path/one`
- `path/two`

No other files may be edited. If the task needs one, stop and report
`CONTRACT IMPACT` or `BLOCKED`; do not widen your own scope.

## Forbidden

- `Cargo.toml` (root), `rust-toolchain.toml`, `.github/**`, `opencode.jsonc`,
  `.opencode/agents/**`, `LICENSE*` unless explicitly listed under **Allowed
  files**. A crate-level `Cargo.toml` is allowed only when this card lists it
  and its dependencies are already approved by an ADR.
- Hand edits to `Cargo.lock`; cargo may update it, but the change must be
  reported and reviewed.
- Any path outside the allowed list.
- Any privileged or boot-changing command.

## Interface contract

Name the types, traits, schemas, file formats, or CLI behavior this task must
preserve or introduce. Link to `docs/contracts/` when applicable.

If this task changes a shared type or schema, the card must say so and the
architect gate is mandatory.

## Acceptance

Run exactly these commands from the repository root and report the key output:

```sh
cargo fmt --check
cargo clippy -p <crate> --all-targets -- -D warnings
cargo nextest run -p <crate>
cargo xtask demo --fixture tests/fixtures/<name>.json
```

A command that cannot run is a failure, not an invitation to substitute an
easier one. Do not claim success from an exit code alone if the task produces an
artifact; inspect the artifact.

## Facts (verified)

- <fact> — verified <YYYY-MM-DD> with <command or file:line>.

Kernel symbols, module names, sysfs paths, and crate APIs must appear here with
a source. If a fact is not verified, say so and keep it out of the implementation.

## Known risks

- <what could break, and how the tests cover it>

## Out of scope

- <adjacent work that belongs in another task>

## Handoff

Return the structured handoff from `AGENTS.md`. Save long logs under
`target/agent-logs/NNN/` and return only the key lines.
