# Roadmap

Dates are not promises. Each phase ends with a verifiable exit criterion, not a
feature count.

## P0 — Walking skeleton

**Goal:** prove the manifest pipeline and make the project testable offline.

- TASK-001: Cargo workspace + `kt-core` models, schema version, errors.
- TASK-002: CPU detector (topology, flags, microarchitecture).
- TASK-003: PCI detector from sysfs.
- TASK-004: storage/boot detector (LUKS, btrfs, NVMe, Limine, cmdline, zram).
- TASK-005: manifest report renderer (Markdown + JSON).
- TASK-006: fixture capture, redaction, deterministic replay.
- TASK-007: `kt doctor` and `kt demo --fixture`.

**Exit:** `kt probe` and `kt report` run unprivileged on the reference machine;
a fixture replays in CI; no hardware identifiers leak.

## P1 — Planner

**Goal:** turn hardware requirements into a defensible config plan.

- Kconfig symbol index and expression parser.
- modalias/module to symbol mapping, with provenance.
- policy engine and `safe` / `balanced` / `aggressive` profiles.
- boot-critical linter.
- `kt plan`, `kt diff`, `kt explain`.

**Exit:** the reference machine plans under ~300 modules with zero boot-critical
violations; every removed symbol has a reason; every conflict is reported.

## P2 — First tailored kernel

**Goal:** build and boot a unique Arch package without touching the stock kernel.

- Arch source selection and version pinning.
- generated PKGBUILD, unique package name, module directory.
- explicit mkinitcpio module list.
- Limine entry generation.
- `kt build` and `kt install` separation.

**Exit:** boots to Hyprland with LUKS unlock, WiFi/Ethernet, GPU, audio, and
Bluetooth working; stock kernel remains selectable; rollback documented.

## P3 — Verification

**Goal:** make a tailored kernel trustworthy.

- `virtme-ng` / QEMU boot gate.
- `kt verify --boot` hardware report.
- bless/rollback flow.
- benchmark harness and baseline comparison.

**Exit:** nightly QEMU boot passes on the fixture matrix; real boots produce a
pass/fail report with metrics.

## P4 — Aggressive trimming and automation

**Goal:** measurable wins without lost safety.

- firmware and initramfs pruning.
- `NR_CPUS`, `-march=native`, debug-feature trimming with workload guards.
- GPU-specific policy (UMA warning, ROCm detection, legacy GPU support removal).
- rebuild timer / pacman hook, `kt try --kexec` experiment.

**Exit:** published before/after report versus the stock kernel; no unguarded
boot-critical removal.

## P5 — Community and 1.0

- Debian/Fedora build backends.
- community hardware compatibility matrix.
- `kt new-detector` generator and Rust primer.
- release automation, signed tags, 1.0 docs.

**Exit:** external contributors' hardware reports are reproducible; a release can
be installed by a new user without reading the source.
