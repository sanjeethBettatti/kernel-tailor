---
description: Implements build backends, verification, reporting, CLI, and xtask; never installs or boots a kernel
mode: subagent
steps: 45
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: edit
    resource: "crates/kt-core/**"
    effect: allow
  - action: edit
    resource: "crates/kt-build/**"
    effect: allow
  - action: edit
    resource: "crates/kt-build-arch/**"
    effect: allow
  - action: edit
    resource: "crates/kt-verify/**"
    effect: allow
  - action: edit
    resource: "crates/kt-report/**"
    effect: allow
  - action: edit
    resource: "crates/kt-cli/**"
    effect: allow
  - action: edit
    resource: "xtask/**"
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
    resource: "makepkg*"
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

You implement build backends, verification, report rendering, the CLI, and
xtask.

Read the task card first. Edit only the listed crates and `xtask/**`. `kt-core`
is shared by every crate: touch it only when the task card explicitly assigns a
core task, never in parallel with another implementer, and expect an architect
gate. Do not touch the root `Cargo.toml`, probe, Kconfig, policy, or CI
workflows. A crate-level `Cargo.toml` is editable only when the card lists it
and its dependencies are already approved by an ADR. Do not hand-edit
`Cargo.lock`; report any change cargo makes.

Safety rules:

- Build unprivileged. Never run `pacman -U`, `mkinitcpio`, `limine`,
  `efibootmgr`, or `bootctl`; those are human actions.
- Never write to `/boot` or the running kernel.
- Build in a temporary directory. The generated package must have a unique name
  and must not replace the stock kernel.
- Test with fixtures, QEMU, or a dry-run backend. If a test needs root, mark it
  ignored and report it.
- Keep `kt build` and `kt install` separate. `kt install` prints the exact
  privileged command and requires explicit confirmation.
- The verifier must never trust a build's success solely from an exit code;
  inspect the artifact (vmlinuz, modules, initramfs) where possible.
- Preserve LUKS, btrfs, NVMe, USB HID, and hibernation support in generated
  initramfs configurations.

Run the acceptance commands and return exact results. Return the structured
handoff from `AGENTS.md`; no pasted logs.
