# Status

Last updated: 2026-11-24

## Phase

P0 — walking skeleton. The repository contains the workflow, architecture, and
contracts. No Rust code exists yet.

## Done

- Repository initialized.
- Agent workflow scaffolded: lead, architect, probe-dev, kconfig-dev,
  platform-dev, verifier, reviewer, researcher, docs.
- Task, ADR, and contract templates added.
- Project docs, contributing guide, security policy, and dual license added.
- Initial decisions recorded in `docs/decisions/ADR-0001-language-and-architecture.md`.

## Next

1. **TASK-001:** create the Cargo workspace and `kt-core` models. Human approves
   `Cargo.toml`; architect reviews the schema; implementer writes models and
   tests.
2. **TASK-002:** CPU detector with a fixture from the reference machine
   (Ryzen 5 8500G, 6C/12T, Zen 4).
3. **TASK-003:** PCI detector with a fixture containing the Radeon 740M,
   RTL8125, and MT7922.
4. **TASK-004:** storage/boot detector covering LUKS2, btrfs, NVMe, Limine,
   zram, and hibernation swapfile.
5. **TASK-005:** Markdown/JSON manifest report.
6. **TASK-006:** fixture capture and redaction.
7. **TASK-007:** `kt doctor` and `kt demo`.

## Blockers / known gaps

- Rust is not installed on the reference machine. Install rustup and the stable
  toolchain before TASK-001.
- TASK-001 needs a human pre-step: create the root `Cargo.toml` with the
  workspace members and the dependency set approved in ADR-0001. The implementer
  then owns `crates/kt-core` and its crate manifest.
- There is no Cargo workspace yet; `cargo` commands in the docs are aspirational
  until TASK-001.
- No CI workflow yet; it lands with TASK-001.
- Kernel source selection for the Omarchy kernel is unresolved; the installed
  build tree has Kconfig files, but a full pinned source checkout is needed for
  builds.

## Reference machine facts

- Omarchy 4.5.0, kernel `7.2.15-1-omarchy`, Limine, Secure Boot off.
- Ryzen 5 8500G (Zen 4, 6C/12T), 30 GiB RAM.
- Radeon 740M iGPU, 512 MB UMA carve-out, 16 GiB GTT, Mesa 26.2.5/RADV.
- LUKS2 → btrfs root, 30.5 GB swapfile plus zram, `/boot` 2 GB vfat.
- 6,322 modules built, 128 loaded, 156 MB of `.ko` files, 449 MB firmware.
- Debug/config costs: `DEBUG_INFO`, `UBSAN`, `SLUB_DEBUG`, `PAGE_POISONING`,
  `FTRACE`, `KALLSYMS_ALL`, `NR_CPUS=8192`, no `-march=native`.

## How to update this file

The lead updates it after each task closes. Keep it to one screen: phase, done,
next, blockers. Details belong in task cards and ADRs.
