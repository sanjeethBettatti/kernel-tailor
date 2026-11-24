# Contributing

Thank you for helping make kernels fit their hardware. This project is designed
so that the first contribution does not require root, a kernel build, or deep
Rust experience.

## Start here

1. Read [README.md](README.md) and [docs/architecture.md](docs/architecture.md).
2. Read [AGENTS.md](AGENTS.md) if you are working with an AI agent.
3. Pick a task from [docs/status.md](docs/status.md) or open an issue using the
   task template in [docs/tasks/000-template.md](docs/tasks/000-template.md).

## Three ways to contribute

### 1. Data only (15 minutes)

Add a modalias-to-Kconfig mapping, a boot-critical rule, or a policy rule under
`data/`, with a fixture and a golden test. These changes need no Rust.

### 2. Hardware fixtures (30 minutes)

Run the probe on your machine, redact it, and submit the manifest under
`tests/fixtures/`. This is how the project learns about hardware it does not own.
Follow `.opencode/skills/fixture-capture/SKILL.md`.

### 3. Rust (1 hour+)

Start with a detector in `crates/kt-probe/`, using `kt new-detector` once it
exists. Every detector task includes a compiling skeleton, a fixture, a test, and
a docs stub.

If you are new to Rust, read `docs/rust-primer.md` (planned) and start with a
data or fixture task. Pairing sessions are welcome.

## Development setup

The project targets stable Rust. `rust-toolchain.toml` pins the channel and
components; install [rustup](https://rustup.rs/) first.

```sh
rustup toolchain install stable
rustup component add rustfmt clippy
cargo install cargo-nextest cargo-deny cargo-audit
```

Arch/Omarchy also needs the kernel build prerequisites listed in
[docs/getting-started.md](docs/getting-started.md).

Agent sessions are denied package installation and `cargo install`. Run the
setup commands yourself; an agent cannot install the toolchain for you.

## Task workflow

Every nontrivial change starts as a task card in `docs/tasks/`. The card lists the
goal, allowed files, forbidden files, interface contract, acceptance commands,
facts, and risks. A change is done when:

- the acceptance commands pass;
- the verifier reports PASS;
- the reviewer has no blocking findings;
- fixtures are redacted;
- `docs/status.md` is updated.

Do not open a large pull request without a task card. Small fixes and typo
corrections do not need one.

## Commit rules

- Use Conventional Commits: `feat(probe): ...`, `fix(kconfig): ...`,
  `docs: ...`, `chore: ...`.
- Sign off every commit with `git commit -s` (Developer Certificate of Origin).
- Keep one concern per commit.
- Do not add dependencies without an ADR and maintainer approval.
- Do not include generated build artifacts, logs, or unredacted hardware data.

## Safety rules

- Never test install commands against `/boot` or the running kernel.
- Builds are unprivileged. Installation is a separate, human-approved step.
- Never patch kernel source in v1; change Kconfig and build inputs instead.
- Do not commit secrets, serial numbers, MAC addresses, disk UUIDs, or hostnames.

## Code of conduct

Participation is governed by [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md). Be
patient with people learning Rust and kernel concepts; that is the point of the
project.
