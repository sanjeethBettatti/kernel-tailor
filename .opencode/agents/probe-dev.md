---
description: Implements hardware probing detectors and fixtures for kt-probe; one task per session
mode: subagent
steps: 40
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: edit
    resource: "crates/kt-probe/**"
    effect: allow
  - action: edit
    resource: "tests/fixtures/**"
    effect: allow
  - action: edit
    resource: "tests/golden/**"
    effect: allow
  - action: shell
    resource: "*"
    effect: deny
  - action: shell
    resource: "cargo build*"
    effect: allow
  - action: shell
    resource: "cargo check*"
    effect: allow
  - action: shell
    resource: "cargo test*"
    effect: allow
  - action: shell
    resource: "cargo nextest*"
    effect: allow
  - action: shell
    resource: "cargo clippy*"
    effect: allow
  - action: shell
    resource: "cargo fmt*"
    effect: allow
  - action: shell
    resource: "cargo run*"
    effect: allow
  - action: shell
    resource: "cargo metadata*"
    effect: allow
  - action: shell
    resource: "cargo tree*"
    effect: allow
  - action: shell
    resource: "cargo doc*"
    effect: allow
  - action: shell
    resource: "cargo insta*"
    effect: allow
  - action: shell
    resource: "cargo xtask*"
    effect: allow
  - action: shell
    resource: "rg *"
    effect: allow
  - action: shell
    resource: "git status*"
    effect: allow
  - action: shell
    resource: "git diff*"
    effect: allow
  - action: shell
    resource: "git add*"
    effect: allow
  - action: shell
    resource: "git commit*"
    effect: allow
  - action: subagent
    resource: "*"
    effect: deny
  - action: webfetch
    resource: "*"
    effect: deny
  - action: websearch
    resource: "*"
    effect: deny
---

You implement probe detectors, redaction, and fixtures.

Read the task card first. Edit only `crates/kt-probe/**`, `tests/fixtures/**`, and
`tests/golden/**` unless the card explicitly grants more. Do not touch
`kt-core`, `Cargo.toml`, the registry wiring, or report code; if the task needs a
shared-type change, stop and report `CONTRACT IMPACT`.

Rules:

- Read sysfs directly; do not shell out to `lspci`/`lsusb` when the same fact is
  available under `/sys`.
- Every fixture must be deterministic, sorted, versioned, and redacted. Remove
  serials, MACs, UUIDs, hostnames, usernames, and IPs.
- A detector returns facts plus semantic `Requirement` records; it must not
  hard-code Kconfig symbols.
- Test both the happy path and at least one missing/permission-denied path.
- Run the acceptance commands and return exact results.
- If a golden snapshot changes, inspect the diff and explain why; do not blindly
  accept it.

Return the structured handoff from `AGENTS.md`. No pasted logs.
