# Project 07 — RHEL Virtual Machine Deployment

## Objective
Deploy the first RHEL administration VM on pve01 using suitable virtual hardware.

## Problem
The initial RHEL installation media was ARM/aarch64 and did not boot in the Intel x86 virtual machine.

## Diagnostic Approach
Checked the attached CD/DVD ISO and boot order, then compared ISO architecture with the host/guest architecture.

### Commands Used

```bash
hostnamectl
ip -br addr
ping -c 4 <ROUTER_IP>
ping -c 4 <PUBLIC_TEST_IP>
getent ahostsv4 debian.org
```

- `hostnamectl`: checks the guest hostname and operating system.
- `ip -br addr`: displays guest interface addresses.
- `ping -c 4`: checks router or public-IP reachability.
- `getent ahostsv4`: checks IPv4 hostname lookup.

## Resolution
Replaced the ARM ISO with RHEL x86_64 media and installed RHEL 10.3 Server with GUI. Configured automatic partitioning inside the VM’s virtual disk, Ethernet connectivity, hostname `linux-lab01`, and a regular administrator account.

| VM setting | Value |
|---|---|
| Host | pve01 |
| CPU | 1 socket, 2 cores; `x86-64-v3` |
| Memory | 4096 MiB / 4 GiB; ballooning disabled |
| Disk | 40 GiB SCSI disk on `local-lvm` |
| Controller | VirtIO SCSI single |
| Disk options | Default no-cache; discard and IO thread enabled |
| Firmware / machine | SeaBIOS / default i440fx |
| Network | VirtIO on `vmbr0`; no VLAN tag |
| Addressing | DHCP; actual address omitted |

## Validation
The installer booted successfully with the correct media. RHEL reached its login screen, the hostname was confirmed, both connectivity tests passed, and hostname lookup returned addresses.

## Lessons Learned
- Installation media must match the guest architecture.
- RHEL 10 requires an x86-64-v3 baseline for the x86 guest.
- Guest CPU, memory, disks, users, and hostname are separate from the Proxmox host.
- DHCP guest addresses can change; no reservation was recorded as configured.

## Future Improvements
Add further administration VMs and document migration behavior. Configure predictable guest addressing when needed.

> Angle-bracket values are placeholders, not commands to paste unchanged. Replace them locally. Actual network addresses are omitted.
