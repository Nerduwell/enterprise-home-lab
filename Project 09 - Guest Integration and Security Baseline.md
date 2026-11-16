# Project 09 — Guest Integration and Security Baseline

## Objective
Verify Proxmox guest integration, inspect RHEL security mechanisms, and create a baseline rollback point.

## Problem
The VM needed validation of guest-agent communication and security state before starting configuration labs.

## Diagnostic Approach
Queried the guest-agent package/service, inspected the VM Summary page, and checked SELinux and firewalld states.

### Commands Used

```bash
rpm -q qemu-guest-agent
systemctl status qemu-guest-agent --no-pager
getenforce
systemctl is-active firewalld
```

- `rpm -q`: queries whether a package is installed.
- `systemctl status`: displays service state; `--no-pager` prints directly.
- `getenforce`: displays SELinux’s operating mode.
- `systemctl is-active`: checks whether the named service is active.

## Resolution
Enabled the QEMU Agent option in the VM hardware configuration and confirmed the installed guest service was active. Verified existing security controls. Created the `baseline-updated` snapshot through Proxmox with Include RAM unchecked.

## Validation
Proxmox displayed the guest address, confirming integration. SELinux returned `Enforcing`; firewalld returned `active`. The snapshot completed successfully.

## Lessons Learned
- Enabling the agent in Proxmox and running it inside the guest are separate requirements.
- An enforcing SELinux mode and an active firewall are baseline checks, not a complete hardening audit.
- A snapshot without RAM does not retain the running desktop/session state.
- A snapshot depends on its backing storage and is not an independent backup.

## Future Improvements
Test rollback using disposable changes, plan independent backups, and document firewall rules and SELinux policies in later security labs. Root-account lock state was not separately verified.
