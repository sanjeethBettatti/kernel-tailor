---
name: Arch Packaging and Boot Safety
description: Build and initramfs rules for Arch/Omarchy, Limine, LUKS, btrfs, and rollback
---

# Arch packaging and boot safety

Use this skill for build backends, initramfs generation, Limine entries, and
anything that could affect boot.

## Non-negotiable invariants

- Never overwrite the running kernel or its module tree.
- Generate a unique package and module directory, for example
  `linux-kt-<hwid>` and `CONFIG_LOCALVERSION="-kt-<hwid>"`.
- Add a new boot entry; do not change the default entry until a real boot passes.
- `kt build` is unprivileged. Installation is a separate human-approved step.
- Never run `pacman -U`, `mkinitcpio`, `limine`, `efibootmgr`, or `bootctl` from
  an agent session.
- Never write to `/boot` during development or tests.

## Arch flow

1. Select and pin a kernel source tree matching the running kernel version.
2. Import the running config (`/proc/config.gz` or `/boot/config-*`).
3. Generate the plan and a Kconfig fragment.
4. Build in a temporary directory with the distro's packaging inputs.
5. Package as `linux-kt-<hwid>-<pkgver>-<pkgrel>-x86_64.pkg.tar.zst`.
6. Inspect `vmlinuz`, `System.map`, `modules.builtin`, and the module tree.
7. Generate an initramfs module list from the manifest plus the boot-critical
   set; verify with `lsinitcpio` in a safe copy.
8. Add a Limine entry. Limine has no boot counting, so fallback is explicit:
   keep the stock entry and require a menu selection or a verified default change.
9. Verify in QEMU/virtme, then on real hardware, before `kt bless`.

## Boot-critical modules for this project

At minimum, the linter and initramfs must account for:

- storage controller: NVMe, AHCI, virtio-blk;
- encryption: dm-crypt, the cipher/hash used by LUKS;
- filesystems: btrfs/ext4/xfs as used by root and `/boot`;
- input: USB HID keyboard before root unlock;
- display: simpledrm/efifb for early output;
- firmware for every kept module;
- out-of-tree modules and their headers (NVIDIA, ZFS, VirtualBox);
- hibernation swapfile/resume offset when configured.

## Do not

- Do not disable `DRM_AMD_DC`, `DRM_AMD_DC_FP`, `AMD_IOMMU`, or the active
  storage/filesystem stack for a performance claim.
- Do not delete firmware files owned by packages; prune initramfs first.
- Do not claim boot success from a package build exit code.
