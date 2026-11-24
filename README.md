# kernel-tailor

Hardware-aware kernel configuration compiler and verifier.

> **Status: pre-alpha scaffold.** There is no Rust code yet. The workflow,
> architecture, and contracts are in place; TASK-001 creates the workspace.

`kernel-tailor` probes the machine it runs on, records a versioned hardware
manifest, and compiles a Kconfig plan that keeps what the hardware and workloads
need while trimming what they do not. Every build is verified before it can be
blessed as the default boot entry. The stock kernel is never overwritten.

The CLI is `kt`. The first target is Arch Linux / Omarchy; other distributions
come after the core is trustworthy.

## How it works

```
kt probe ──► HardwareManifest (JSON, redacted, versioned)
                 │
running config ──┤   pure, offline, deterministic
policy profiles ─┤
                 ▼
            BuildPlan (per-symbol decision + provenance + risk)
                 ▼
     Builder (Arch/Generic) ──► package + vmlinuz + initramfs
                 ▼
     Verifier: config lint → build → QEMU boot → real boot → bless
```

The keystone rule: **probe once, then make everything a pure function over a
manifest.** Contributors can work on most of the project without root, a kernel
source tree, or a 30-minute build.

## Safety model

- The running kernel and its modules are never overwritten.
- A tailored kernel gets a unique package name and boot entry.
- The default boot entry changes only after a real boot is verified.
- A boot-critical linter checks storage, LUKS, filesystems, input, firmware, and
  out-of-tree modules before a build is allowed.
- The tool never runs `mkinitcpio`, `limine`, or `pacman -U` automatically.
- Fixtures are redacted before they are committed.

This is not a kernel fork. V1 does not patch kernel source; it selects a source
tree, imports the running config, and changes Kconfig plus build/initramfs setup.

## Repository layout

```
crates/            Rust workspace (planned; TASK-001)
data/              modalias, boot-critical, firmware ownership data
docs/              architecture, contracts, decisions, tasks, roadmap
tests/fixtures/    captured hardware manifests, usable offline
.opencode/         agent definitions, commands, skills
```

See [docs/architecture.md](docs/architecture.md) for the crate boundaries and
[docs/status.md](docs/status.md) for what exists today.

## Contributing

New contributors do not need to build a kernel to start. The first three paths
are data-only, fixtures, and docs. Read [CONTRIBUTING.md](CONTRIBUTING.md),
[AGENTS.md](AGENTS.md), and [docs/getting-started.md](docs/getting-started.md).

If you use an AI agent on this repository, start it from the repository root so
OpenCode discovers `.opencode/` and `AGENTS.md`.

## License

Licensed under either of:

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE))
- MIT license ([LICENSE-MIT](LICENSE-MIT))

at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in this project shall be dual-licensed as above, without additional
terms or conditions.
