---
name: Hardware Fixture Capture
description: Capture redacted, deterministic hardware manifests for tests and community reports
---

# Hardware fixture capture

Use this skill when adding a hardware manifest under `tests/fixtures/` or
preparing a report for an issue.

## Goal

A fixture must let another contributor reproduce a plan **without the physical
machine**, and it must not identify the person who captured it.

## Keep

- CPU vendor, family, model, topology, and feature flags.
- PCI/USB vendor:device IDs, class, driver, and modalias.
- Storage topology at the class level: NVMe/SATA/virtio, LUKS, LVM, btrfs/ext4.
- Boot mode, bootloader, Secure Boot state, initramfs generator.
- Kernel release, distro, config hash, and probe version.
- Workload categories: containers, VMs, ROCm/HIP, perf/bpftrace, DKMS.

## Remove or hash

- Serial numbers, asset tags, and machine UUIDs.
- MAC addresses and interface names.
- Disk UUIDs, PARTUUIDs, and filesystem labels.
- Hostnames, usernames, home paths, and IP addresses.
- Kernel command-line secrets and tokens.

If stable identity is needed for deduplication, use a deterministic hash with a
documented namespace, never a reversible encoding.

## Determinism

- Sort arrays and object keys.
- Use a fixed timestamp or omit it from golden comparisons.
- Include `schema_version` and `probe.tool_version`.
- Keep the fixture small; omit unrelated devices.
- Store the raw capture separately and untracked; commit only the redacted file.

## Test

- `cargo xtask demo --fixture tests/fixtures/<name>.json`
- Golden plan assertion for the fixture.
- A redaction test that fails if a known-sensitive key appears.
- No root and no live hardware required in CI.

## Review checklist

- [ ] No serials, MACs, UUIDs, hostnames, usernames, or IPs.
- [ ] Vendor:device IDs preserved.
- [ ] Kernel and distro recorded.
- [ ] Fixture replays deterministically.
- [ ] Golden output reviewed, not blindly accepted.
