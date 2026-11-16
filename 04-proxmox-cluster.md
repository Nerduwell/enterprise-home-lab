# Host Maintenance and Three-Node Proxmox Cluster

## Completed state

| Node | Management IP | Outcome |
|---|---|---|
| pve01 | `192.168.1.10` | Updated, rebooted, accessible; first cluster member |
| pve02 | `192.168.1.11` | Updated, rebooted, accessible; joined existing cluster |
| pve03 | `192.168.1.12` | Updated, rebooted, accessible; joined existing cluster |

The hosts run Proxmox VE 9.2. During the update session I reported pve01's resulting version as `9.2.27`; full raw version/kernel output was not retained. All three nodes were confirmed updated and rebooted. Use `pveversion` on each host to record exact current versions rather than treating this log as a package inventory.

## Storage validation

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
pvesm status
```

`lsblk` lists block devices; `-o` selects columns. `pvesm status` reports configured Proxmox storage and its capacity/availability.

Both `local` (directory storage) and `local-lvm` (LVM thin storage) were active on all hosts. On pve01, the reported sizes were approximately 93.9 GiB for `local` and 348.8 GiB for `local-lvm`. ISO images were stored in `local`; the RHEL VM disk was created on `local-lvm`.

One machine also contains an extra approximately 500 GB drive, `sda`, with a Windows-style partition layout. Proxmox was installed on `nvme0n1` (approximately 476.9 GiB / marketed 512 GB). The extra drive was left untouched. Device names and capacities were checked before considering storage changes.

## Repository configuration and updates

For each node, under **Updates → Repositories**:

1. Disabled `pve-enterprise`, since there is no active paid subscription.
2. Enabled `pve-no-subscription`.
3. Disabled the separate Ceph Squid enterprise repository after it caused an HTTP 401 error.
4. Kept the Debian repositories enabled.

Ceph was not configured. The no-subscription repository provides updates without an enterprise subscription; it has a different validation level from the enterprise repository.

```bash
apt update
apt list --upgradable
apt full-upgrade
reboot
pveversion
```

| Command | Role |
|---|---|
| `apt update` | Refreshes repository package indexes; does not install upgrades. |
| `apt list --upgradable` | Lists installed packages with available newer versions. |
| `apt full-upgrade` | Applies upgrades and resolves dependency changes, potentially including additions/removals; review its proposed transaction first. |
| `reboot` | Restarts the host, interrupting any workloads on it. |
| `pveversion` | Displays installed Proxmox version and running kernel. |

On pve01 the reviewed transaction proposed 213 upgrades, 2 installations, 0 removals, and 0 held back. This was a point-in-time result, not an expected count for future updates. Nodes were maintained one at a time.

## Cluster creation and joining

Before joining, `qm list` and `pct list` confirmed no existing VMs or containers on all three nodes. `qm` manages VMs and `pct` manages Linux containers. Hostnames, management addresses, clock synchronization, and connectivity were checked first.

On **pve01 only**:

```bash
pvecm create homelab
pvecm status
```

`pvecm` manages Proxmox clusters. `create homelab` creates the cluster with the local node as its first member; `status` displays membership and quorum. A one-node cluster initially showed one vote and was quorate.

A CLI join attempt from pve02 using `pvecm add 192.168.1.10` failed with certificate hostname verification. I completed the join using the GUI:

1. On pve01, opened **Datacenter → Cluster → Join Information** and copied the information directly.
2. On pve02, opened **Datacenter → Cluster → Join Cluster**, pasted the information, and supplied pve01's root password locally.
3. Used pve02's management address for its local cluster link where requested.
4. Repeated the join on pve03 with its own local address.

The join information includes the existing node's certificate fingerprint. I used the information from pve01 rather than disabling certificate verification. No passwords, join blobs, or fingerprints are stored in this repository.

## Validation and limits

Final `pvecm status` was confirmed to show three nodes, three expected/total votes, quorum of two, and `Quorate: Yes`.

- All three nodes can be managed through a cluster member's web interface.
- Cluster membership distributes configuration; CPU and memory remain per host.
- Local disks are not automatically shared by clustering.
- Automatic VM recovery on another host is not configured. HA, shared/replicated VM storage, and recovery testing remain future work.
- A three-vote cluster normally needs two communicating votes for quorum. I have not performed failure-injection testing.
