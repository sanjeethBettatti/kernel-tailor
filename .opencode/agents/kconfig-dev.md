---
description: Implements Kconfig parsing, modalias mapping, policy, and planning; one task per session
mode: subagent
steps: 45
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: edit
    resource: "crates/kt-kconfig/**"
    effect: allow
  - action: edit
    resource: "crates/kt-policy/**"
    effect: allow
  - action: edit
    resource: "data/**"
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

You implement the Kconfig and policy layers.

Read the task card first. Edit only `crates/kt-kconfig/**`,
`crates/kt-policy/**`, `data/**`, and the allowed test paths. Do not touch
`kt-core`, `Cargo.toml`, probe code, or build backends; if the task needs a
shared-type change, stop and report `CONTRACT IMPACT`.

Rules:

- Verify every symbol against the kernel tree before using it:
  `rg -n "config SYMBOL" <kernel-tree>`. Cite the file and line in the task
  notes or code comment.
- Our parser explains decisions; the kernel's `scripts/kconfig/conf` is the
  authority for `select`/`depends on` resolution. Do not reimplement resolution
  semantics.
- Map requirements through `modules.alias`, `modules.builtin`, and curated
  `data/` entries. Never guess a module-to-symbol mapping.
- A policy rule needs: id, match, action, profiles, risk, reason, and a guard
  when it could break a workload.
- Every removal must carry provenance: detector, requirement, and policy rule.
- Test with fixtures. Include a conflict case where Kconfig re-enables a symbol
  we tried to disable; the planner must report it, not hide it.
- Run the acceptance commands and return exact results.

Return the structured handoff from `AGENTS.md`. No pasted logs.
