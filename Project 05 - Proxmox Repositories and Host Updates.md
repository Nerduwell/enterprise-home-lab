# Project 05 — Proxmox Repositories and Host Updates

## Objective
Enable working software sources and update all three Proxmox hosts before clustering.

## Problem
Subscription-only repositories were enabled without an active paid subscription. A package refresh returned HTTP 401 Unauthorized from the separate Ceph Squid enterprise source.

## Diagnostic Approach
Reviewed repository entries and the failing repository URL. Distinguished the main Proxmox source from the separate Ceph enterprise source, then reviewed the proposed package transaction.

### Commands Used

```bash
apt update
apt list --upgradable
apt full-upgrade
reboot
pveversion
```

- `apt update`: refreshes package indexes without installing upgrades.
- `apt list --upgradable`: lists available upgrades.
- `apt full-upgrade`: resolves and installs updates; dependency changes can add or remove packages.
- `reboot`: restarts the host, interrupting its workloads.
- `pveversion`: displays Proxmox and running-kernel version information.

## Resolution
Disabled `pve-enterprise`, enabled `pve-no-subscription`, and disabled the Ceph enterprise source. Kept Debian repositories enabled. Updated and rebooted hosts one at a time. The pve01 transaction proposed 213 upgrades, 2 installations, and no removals or held packages.

## Validation
Repository refreshes completed without errors. All three hosts were confirmed updated, rebooted, and accessible. pve01’s resulting version was reported as `9.2.27`; complete version output was not retained.

## Lessons Learned
- “Updates available” does not mean the packages have been installed.
- HTTP 401 from the repository indicated subscription access failure, not proof that the home IP was blocked.
- Kernel updates take effect after reboot.
- Package counts are point-in-time observations; future updates will differ.

## Future Improvements
Record exact package/kernel versions with future maintenance. Keep reviewing transactions before installation and add independent backups before hosting important workloads.
