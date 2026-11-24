# Security Policy

## Status

The project is pre-alpha. There are no supported releases yet. Security reports
are still welcome, especially anything that could make a generated kernel
unbootable or expose hardware data.

## Reporting a vulnerability

Do not open a public issue for a vulnerability. Use GitHub's private
**Security → Report a vulnerability** flow for this repository.

Include:

- affected commit or version;
- the platform and kernel release;
- a minimal reproduction or the relevant config/manifest fragment;
- impact: boot failure, data exposure, privilege boundary, or supply chain.

Redact serials, MAC addresses, disk UUIDs, hostnames, and keys before attaching
logs or manifests.

## What counts as security-relevant

- A config plan that removes a boot-critical driver, filesystem, LUKS component,
  or input device.
- A generated package or boot entry that can overwrite or shadow the stock kernel.
- A path that runs privileged commands without explicit human approval.
- Leakage of identifying hardware data in fixtures or reports.
- A dependency or build-script supply-chain issue.

## Response

Maintainers will acknowledge a report as soon as practical, agree on a disclosure
timeline, and credit reporters unless they prefer to remain anonymous. Please do
not publish details before a fix or mitigation is available.
