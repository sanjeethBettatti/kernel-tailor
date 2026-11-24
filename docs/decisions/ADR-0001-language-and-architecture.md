# ADR-0001 — Language and architecture

Status: accepted
Date: 2026-11-24
Deciders: project maintainer

## Context

`kernel-tailor` is a new tool that will probe a machine, compile a Kconfig plan,
build a uniquely named kernel package, verify it, and only then allow it to
become the default boot entry. The first target is Arch Linux / Omarchy. The
project must be testable without root, a kernel build, or the physical machine,
and it must be approachable for contributors who are new to Rust or kernel
configuration.

No production code exists yet. This ADR records the choices that would be
expensive to reverse once the workspace and contracts exist.

## Decision

We will:

1. **Use Rust for the tool.** The CLI is a single native binary with no runtime
   or garbage collector. Rust's type system carries the manifest/plan/report
   schemas through the pipeline, and `serde` gives deterministic JSON with
   generated schemas.
2. **Use a Cargo workspace with narrow crates.** `kt-core` owns shared models;
   `kt-probe`, `kt-kconfig`, `kt-policy`, `kt-build`, `kt-build-arch`,
   `kt-verify`, `kt-report`, and `kt-cli` own one stage each; `xtask` owns
   developer workflows. The binary is named `kt`.
3. **Keep the core synchronous.** The work is file I/O and subprocess
   orchestration. We will not add an async runtime in v1.
4. **Use an explicit detector registry.** Detectors are plain types registered
   in code. No plugin macros, dynamic loading, or hidden discovery. A newcomer
   can read the registry and see every detector.
5. **Forbid `unsafe`.** Every crate uses `#![forbid(unsafe_code)]`. If a
   dependency needs unsafe internally, that is acceptable; our code does not.
6. **Separate wire formats.** Manifests, plans, and reports are JSON with a
   `schema_version`; policy profiles are TOML. Rust models are the source of
   truth and generate JSON Schema with `schemars`. Markdown and JSON reports
   land first; HTML is deferred.
7. **Explain Kconfig, do not replace it.** Our parser reads symbols,
   dependencies, and `select` well enough to explain a decision and detect
   conflicts. The kernel's `scripts/kconfig/conf` / `make olddefconfig` remains
   the authority for final resolution. A conflict is reported, never hidden.
8. **Probe once, then compute purely.** After `kt probe`, every later stage is a
   deterministic function of a versioned `HardwareManifest`, the base config, the
   policy profile, and the tool version. This is what makes offline fixtures and
   golden tests possible.
9. **Start report-only.** The first milestone probes and reports; it does not
   generate a config, build, install, or change a boot entry.
10. **Target Arch/Omarchy first.** The build backend, initramfs, and bootloader
    integration target Limine and mkinitcpio first. Other distributions come
    after the core is trustworthy.
11. **License the tool under MIT OR Apache-2.0.** No kernel source is vendored,
    so the tool does not inherit GPL-2.0. Kernel source trees and generated
    artifacts remain under their own licenses.
12. **Approve a baseline dependency set, added only when a task first needs it:**
    `serde`, `serde_json`, `schemars`, `thiserror` for libraries; `anyhow` and
    `clap` for the CLI; `toml` for policy files. Test-only dependencies:
    `insta`, `proptest`, `assert_cmd`, `predicates`. A dependency outside this
    list still needs its own ADR and human approval.

Each addition still requires explicit human approval before it appears in a
crate manifest, as described in `AGENTS.md`.

## Consequences

### Positive

- One binary, no runtime, predictable deployment on Arch.
- Crate boundaries make ownership, review scope, and parallel work explicit.
- Typed contracts and JSON Schema catch drift between probe, planner, and report.
- Offline fixtures let contributors work without root or a 30-minute build.
- No async and no plugin machinery keeps the codebase small and readable.

### Negative

- Rust has a steeper learning curve than Python for new contributors. The
  project mitigates this with data-only and fixture tasks and a planned Rust
  primer.
- Compile times are longer; `xtask` and crate-scoped checks are required to keep
  the inner loop fast.
- Owning a Kconfig parser risks scope creep. The "explain, do not resolve" rule
  is the guard.
- Schema changes need explicit versioning and migration notes, which is slower
  than editing untyped dictionaries.

### Neutral

- The stable toolchain is used for now. TASK-001 pins an exact version once CI
  exists so local and CI builds agree.
- The initial branch is `master`; it may be renamed to `main` before the first
  public release without changing this decision.

## Alternatives considered

- **Python** — fastest to prototype and excellent for text processing, but
  distribution, startup, and typing are weaker for a tool that must be trusted to
  build a kernel. Rejected for v1.
- **Go** — good single-binary deployment and fast builds, but the schema/model
  expressiveness and error handling were judged weaker for this pipeline.
  Rejected for v1.
- **Shell plus `make`** — impossible to test safely and hard to extend. Rejected.
- **A single large crate** — simpler at the start, but it erases ownership
  boundaries and makes review scope and parallel work worse. Rejected.
- **Vendoring the kernel's Kconfig parser** — would couple us to kernel internals
  and GPL-2.0 code. Rejected; the kernel's own `conf` stays authoritative.
- **Generating a config without invoking the kernel's resolver** — would produce
  plans that ignore `select` and dependency semantics. Rejected.
- **GPL-2.0 for the tool** — unnecessary because no kernel code is vendored, and
  it would narrow adoption. Rejected.

## References

- [../architecture.md](../architecture.md)
- [../roadmap.md](../roadmap.md)
- [../contracts/manifest.md](../contracts/manifest.md)
- [../contracts/plan.md](../contracts/plan.md)
- [../contracts/report.md](../contracts/report.md)
- [../workflow.md](../workflow.md)
