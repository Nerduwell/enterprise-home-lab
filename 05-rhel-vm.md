# RHEL VM Deployment and Baseline

## VM configuration

| Setting | Configured value |
|---|---|
| Proxmox node | pve01 |
| VM name / guest hostname | `linux-lab01` |
| Guest OS | RHEL 10.3, x86_64, Server with GUI |
| CPU | 1 socket, 2 cores, `x86-64-v3` model |
| Memory | 4096 MiB (4 GiB); ballooning disabled for initial lab |
| Disk | 40 GiB, SCSI, `local-lvm` |
| Controller | VirtIO SCSI single |
| Disk settings | Default no-cache; discard and IO thread enabled |
| Firmware / machine | SeaBIOS / default i440fx |
| Network | VirtIO adapter on `vmbr0`; no VLAN tag |
| Guest Agent | Enabled in Proxmox; package installed and service active in RHEL |
| Address observed | `192.168.1.108`, assigned by DHCP |
| Snapshot | `baseline-updated`, without RAM |

The virtual CPU model supplies RHEL 10's x86-64-v3 baseline. A VM uses its host's resources; the cluster's combined memory is not a single memory pool for this guest.

## Installation and boot troubleshooting

The initial ISO was `aarch64`, intended for ARM systems, and failed to boot in the Intel x86 VM. I replaced it with the RHEL 10 x86_64 ISO. The installer then booted successfully.

I selected the VM's approximately 40 GiB disk with automatic partitioning, enabled Ethernet connectivity, set the hostname, chose Server with GUI, and created a regular administrator account. These installation choices apply inside the VM's disk, not directly to the host's physical drives.

The regular account was confirmed to work with `sudo`. I have not retained a separate verification of the root-account lock state, so it is not claimed as a completed hardening control here.

## Networking and guest integration

```bash
hostnamectl
ip -br addr
ping -c 4 192.168.1.1
ping -c 4 1.1.1.1
getent ahostsv4 debian.org
rpm -q qemu-guest-agent
systemctl status qemu-guest-agent --no-pager
```

- `hostnamectl` and `ip -br addr` verified hostname and interface addresses.
- Both ping checks returned 0% packet loss during setup; `getent` returned IPv4 addresses.
- `rpm -q` queries whether a package is installed.
- `systemctl status` inspects a service; `--no-pager` prints directly without a scrolling viewer.
- The guest-agent package was present and its service active. The Proxmox Summary page displayed the VM's address, confirming guest-to-host integration.

## Registration and software maintenance

```bash
sudo subscription-manager identity
sudo subscription-manager register
sudo dnf repolist
sudo dnf check-update
sudo dnf upgrade
sudo reboot
uname -r
```

| Command | Explanation |
|---|---|
| `sudo` | Runs an authorized command with administrative privileges. |
| `subscription-manager identity` | Checks the system's registration identity. |
| `subscription-manager register` | Registers the VM; account credentials were entered interactively and are not documented. |
| `dnf repolist` | Lists enabled RHEL software repositories. BaseOS and AppStream were present. |
| `dnf check-update` | Checks for updates without installing them; exit status 100 can mean updates are available. |
| `dnf upgrade` | Resolves and applies package updates after transaction review. The session reported 156 upgrades. |
| `uname -r` | Displays the currently running kernel after reboot. |

Registration succeeded. Updates completed and the VM rebooted. The kernel was reported as `6.12.0-264.16.1.el10_3.x86_64` (the `el10_3` spelling was normalized from the typed report); use `uname -r` for the exact current value.

## Security checks and snapshot

```bash
getenforce
systemctl is-active firewalld
```

`getenforce` returned `Enforcing`, and firewalld returned `active`. These verify that existing security mechanisms are running; they do not demonstrate a complete hardening audit or custom firewall policy.

I created the `baseline-updated` VM snapshot after patching and verification, leaving **Include RAM** unchecked. It is a local rollback point without the running memory state. It is not an independent backup and will not protect against loss of its underlying host storage.

## Next work

Continue Linux administration labs, document recovery exercises, and plan independent backups before adding important data. DHCP reservation/static guest addressing, remote administration configuration, and additional VMs are not recorded as completed.
