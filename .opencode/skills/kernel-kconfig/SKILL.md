---
name: Kernel Kconfig Verification
description: Verify Kconfig symbols, dependencies, and modalias mappings before changing a plan
---

# Kernel Kconfig verification

Use this skill for any task that reads, maps, or changes Kconfig symbols.

## Locate the source of truth

Prefer a pinned kernel source tree:

```sh
KERNEL_TREE="${KERNEL_TREE:-/usr/lib/modules/$(uname -r)/build}"
test -f "$KERNEL_TREE/Kconfig" || echo "not a kernel tree"
```

For release work, use an explicit checkout matching the target kernel version.
Record the version in the task card and fixtures.

## Verify a symbol exists

```sh
rg -n "^\s*(menu)?config SYMBOL\b" "$KERNEL_TREE"
```

Read the surrounding `Kconfig` entry, including:

- type and prompt;
- `depends on`;
- `select`;
- `default`;
- architecture guards;
- help text.

If the symbol does not exist in the target tree, stop. Do not invent a
replacement name or assume a symbol moved.

## Respect Kconfig semantics

- `depends on` controls visibility.
- `select` can force a symbol back on even when a policy disables it.
- The kernel's `scripts/kconfig/conf` is the authority for final resolution.
  Our parser explains decisions; it does not replace `olddefconfig`.
- After generating a config, compare **intent** with **result** and report every
  symbol that could not be disabled because another symbol selected it.

## Map modules to symbols

Use the installed module metadata:

```sh
modinfo -F filename <module>
grep -i "<modalias>" /usr/lib/modules/$(uname -r)/modules.alias
grep -x "<module>" /usr/lib/modules/$(uname -r)/modules.builtin
```

Curated mappings live in `data/modalias-kconfig.toml`. Every entry needs a
source and, where possible, a fixture that exercises it.

## Record provenance

Every decision should answer:

- Which detector/requirement asked for it?
- Which policy rule changed it?
- Which `Kconfig` file and line defines it?
- What happens if it is wrong (boot-critical? workload-specific?)?

## Test

- Add a fixture that contains the hardware/workload.
- Add a golden plan assertion.
- Add a conflict case where `select` overrides the intent.
- Run `cargo xtask demo --fixture <fixture>` once it exists.
