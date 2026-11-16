# Project 08 — RHEL Registration and Software Updates

## Objective
Register RHEL, verify software sources, and patch the guest operating system.

## Problem
The new VM initially reported that it was not registered with Red Hat.

## Diagnostic Approach
Checked registration identity, then verified BaseOS/AppStream availability and reviewed available updates.

### Commands Used

```bash
sudo subscription-manager identity
sudo subscription-manager register
sudo dnf repolist
sudo dnf check-update
sudo dnf upgrade
sudo reboot
uname -r
```

- `sudo`: runs an authorized command with administrative privileges.
- `subscription-manager identity`: checks registration identity.
- `subscription-manager register`: registers the guest using credentials entered interactively.
- `dnf repolist`: lists enabled repositories.
- `dnf check-update`: checks available updates without installing them; status 100 can mean updates are available.
- `dnf upgrade`: prepares and applies reviewed package updates.
- `uname -r`: displays the running kernel release.

## Resolution
Registered the VM successfully, verified BaseOS and AppStream, installed the reported 156 package upgrades, and rebooted the guest.

## Validation
Registration succeeded, repositories were available, updates completed, and the VM restarted. The reported kernel was `6.12.0-264.16.1.el10_3.x86_64`; the spelling is normalized from the typed report, not a retained raw transcript.

## Lessons Learned
- Proxmox host updates and RHEL guest updates are separate maintenance tasks.
- Registration enables access through the associated Red Hat account/subscription.
- Checking repository metadata does not install software.
- `uname -r` reports the running kernel after reboot.

## Future Improvements
Maintain a guest patch log and verify exact current versions during future updates. Credentials and registration identifiers are excluded from public documentation.
