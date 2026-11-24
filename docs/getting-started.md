# Getting started

This project is a scaffold. There is no runnable `kt` binary yet. The fastest way
to contribute is to read the contracts, pick a task, and work with fixtures.

## Prerequisites

- Linux, ideally Arch Linux or Omarchy for the first target.
- Git.
- Stable Rust via [rustup](https://rustup.rs/).

```sh
rustup toolchain install stable
rustup component add rustfmt clippy
```

Optional but expected for kernel work:

```sh
sudo pacman -S --needed base-devel bc flex bison cpio zstd pahole \
  qemu-full virtme-ng ccache
```

Run setup commands from your own shell. Agent sessions are denied `sudo`,
`pacman`, and `cargo install` by design.

Do not install or boot a tailored kernel during P0/P1. Use fixtures and QEMU.

## First 15 minutes

1. Read [README.md](../README.md).
2. Read [docs/architecture.md](architecture.md).
3. Read [docs/status.md](status.md) and the task template in
   [docs/tasks/000-template.md](tasks/000-template.md).
4. If you are using an AI agent, read [AGENTS.md](../AGENTS.md) and
   [docs/workflow.md](workflow.md).

## Three contribution paths

### Data only

Add a mapping or policy rule under `data/` with a fixture and a golden test.
No Rust required.

### Fixtures

Capture a hardware manifest, redact it, and add it under `tests/fixtures/`.
Follow `.opencode/skills/fixture-capture/SKILL.md`. This is the highest-leverage
contribution before the code exists.

### Rust

Start with a detector in `crates/kt-probe/`. Every detector task includes a
skeleton, fixture, test, and docs stub. If you are new to Rust, start with a
data or fixture task and pair on the first detector.

## Agent workflow

Start OpenCode from the repository root, not from a parent directory, so
`.opencode/` and `AGENTS.md` are discovered.

Useful commands once the agent files are loaded:

```
/task-new <title>
/task-plan <task-file-stem>
/task-do <task-file-stem>
/task-verify <task-file-stem>
/task-review <task-file-stem>
/task-close <task-file-stem>
```

The task file stem includes the number and slug, for example
`003-pci-detector` for `docs/tasks/003-pci-detector.md`. The lead writes task
cards and dispatches specialists. It does not edit code. Implementers work in a
fresh child session with a self-contained brief.

## Safety rules

- Never run `sudo`, `pacman -U`, `mkinitcpio`, `limine`, or `efibootmgr` from an
  agent session.
- Never write to `/boot` or modify the running kernel.
- Never commit unredacted hardware data.
- Never add a dependency without an ADR and maintainer approval.
- If a task needs any of the above, stop and ask the human.

## Where to look

- Design: [architecture.md](architecture.md)
- Contracts: [contracts/](contracts/)
- Decisions: [decisions/](decisions/)
- Workflow: [workflow.md](workflow.md)
- Roadmap: [roadmap.md](roadmap.md)
- Current state: [status.md](status.md)
