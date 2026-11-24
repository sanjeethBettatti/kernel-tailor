# Architecture

## Purpose

`kernel-tailor` is a hardware-aware Kconfig compiler and verifier, not a kernel
fork. It answers one question: **given this machine and these workloads, which
kernel configuration is sufficient, and how do we prove it boots?**

V1 does not patch kernel source. It selects a source tree, imports the running
config, generates a plan, builds a uniquely named package, and verifies the
result before it can become the default boot entry.

## Principles

1. **Probe once, compute purely.** All decisions after probing are deterministic
   functions of `HardwareManifest`, the base config, and a policy profile.
2. **Every change has provenance.** A symbol is kept or removed because a
   detector, a requirement, or a policy rule said so. Nothing is anonymous.
3. **Safety before optimization.** A boot-critical linter and independent
   verification gates outrank any size or performance claim.
4. **Never clobber.** The stock kernel, its modules, and its boot entry remain
   intact. A tailored kernel gets a unique identity.
5. **Offline testability.** Fixtures and golden plans let contributors work
   without root, a kernel tree, or a build.
6. **Measured claims only.** Size, boot time, memory, and benchmarks are reported;
   unmeasured performance claims are rejected.

## Pipeline

```
                 ┌──────────────────────────────┐
                 │ probe detectors (kt-probe)   │
                 │ CPU, PCI, USB, storage, net, │
                 │ GPU, boot, workloads         │
                 └──────────────┬───────────────┘
                                │
                                ▼
                 HardwareManifest (JSON, versioned, redacted)
                                │
        running config ─────────┤
        policy profile ─────────┤  pure functions
        kernel Kconfig tree ────┤
                                ▼
                 BuildPlan (decisions + provenance + risk)
                                │
                                ▼
        kt-build / kt-build-arch (makepkg, initramfs, Limine)
                                │
                                ▼
        artifacts: package, vmlinuz, modules, initramfs
                                │
                                ▼
        kt-verify: config lint → build → QEMU → real boot → bless
                                │
                                ▼
        kt-report: diff, sizes, boot time, memory, benchmarks
```

## Crates

| Crate | Responsibility | Owner |
|---|---|---|
| `kt-core` | Manifest, plan, decision, report models; schema version; errors | platform workstream, architect gate |
| `kt-probe` | `Detector` trait, registry, detectors, redaction | probe-dev |
| `kt-kconfig` | Symbol index, expression parser, modalias mapping, boot linter, `explain` | kconfig-dev |
| `kt-policy` | TOML rule engine, profiles, guards, planner | kconfig-dev |
| `kt-build` | Build backend trait, orchestration, artifact inspection | platform-dev |
| `kt-build-arch` | `makepkg`, mkinitcpio module list, Limine entry generation | platform-dev |
| `kt-verify` | QEMU/virtme boot, hardware verification, benchmark harness | platform-dev |
| `kt-report` | Markdown, JSON, and HTML renderers | platform-dev |
| `kt-cli` | `clap` binary named `kt` | platform-dev |
| `xtask` | Developer workflows: demo, fixtures, CI | platform-dev |

Data lives in `data/`:

| File | Purpose |
|---|---|
| `modalias-kconfig.toml` | curated module/modalias to Kconfig mapping |
| `boot-critical.toml` | symbols/modules that must never be removed without a guard |
| `firmware-owners.toml` | firmware files required by kept drivers |

## Contracts

- `docs/contracts/manifest.md` — what the probe records.
- `docs/contracts/plan.md` — what the planner decides and why.
- `docs/contracts/report.md` — what the user sees and what may be claimed.

Schemas are versioned. A field addition is backward-compatible; a semantic change
requires a new schema version and a migration note.

## Kconfig authority

`kt-kconfig` parses Kconfig well enough to explain dependencies, `select`, and
visibility. It does **not** reimplement resolution. Final config resolution is
delegated to the kernel's `scripts/kconfig/conf` / `make olddefconfig`. When a
policy intent conflicts with a `select`, the plan records the conflict rather
than pretending the symbol was disabled.

## Safety invariants

- The running kernel and module tree are never modified.
- Generated packages use `linux-kt-<hwid>` and `CONFIG_LOCALVERSION="-kt-<hwid>"`.
- The default boot entry changes only after a real boot is verified.
- The boot-critical linter checks storage, LUKS, filesystems, input, firmware,
  out-of-tree modules, and hibernation before a build is allowed.
- `kt build` is unprivileged. `kt install` prints the privileged command and
  requires explicit confirmation.
- Fixtures are redacted by default; no serials, MACs, UUIDs, or hostnames.
- Agent sessions cannot run privileged or boot-changing commands.

## Testing strategy

| Layer | Tool | Runs |
|---|---|---|
| Unit | `cargo nextest` | every PR |
| Golden plans | `insta` snapshots | every PR |
| Property tests | `proptest` for Kconfig expressions | every PR |
| CLI behavior | `assert_cmd` + `predicates` | every PR |
| Lint | `cargo fmt`, `clippy -D warnings` | every PR |
| Supply chain | `cargo-deny`, `cargo-audit` | every PR |
| Kernel build | `cargo xtask build-fixture` | nightly |
| Boot smoke | QEMU / `virtme-ng` | nightly |
| Hardware boot | `kt verify --boot` | opt-in, human-run |

Fixtures are the primary test input. A fixture must replay without the physical
machine and without root.

## Non-goals for v1

- Patching kernel source or carrying scheduler/performance patches.
- Non-x86 architectures.
- Distributing prebuilt kernels.
- Replacing the distro package manager.
- Automatic installation or automatic default-entry changes.
- Claiming FPS or memory wins without measurement.

## Open decisions

- Exact schema for `HardwareManifest` v1 (TASK-001).
- Whether a separate `kt-planner` crate is needed once `kt-policy` grows.
- How to pin kernel source per distro package.
- Firmware pruning: initramfs first, filtered package later.
- Benchmark suite and acceptance thresholds for P4.
