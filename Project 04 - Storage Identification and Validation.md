# Project 04 — Storage Identification and Validation

## Objective
Verify available Proxmox storage and identify the correct operating-system disk before making storage changes.

## Problem
One node contained an additional drive with a Windows-style partition layout, which initially made the storage output confusing.

## Diagnostic Approach
Compared block-device names, sizes, filesystem information, and configured Proxmox storage. Distinguished the extra disk from the NVMe selected during installation.

### Commands Used

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
pvesm status
```

- `lsblk`: lists block devices; `-o` selects the displayed columns.
- `pvesm status`: lists configured storage types, availability, and usage.

## Resolution
Identified Proxmox as installed on `nvme0n1`, approximately 476.9 GiB for a marketed 512 GB SSD. The extra `sda` drive was approximately 500 GB with Windows-style partitions and was left untouched. Used `local` for installation ISOs and `local-lvm` for the VM disk.

## Validation
Both storage entries were active on every node. On pve01, `local` was approximately 93.9 GiB and `local-lvm` approximately 348.8 GiB. The later RHEL VM disk was successfully created on `local-lvm`.

## Lessons Learned
- Device names must be checked before formatting or assigning storage.
- Marketed GB and reported GiB use different units.
- LVM thin storage allocates physical blocks as data is written.
- Cluster membership does not automatically share local disks.

## Future Improvements
Plan external-storage integration and independent backups. No Ceph pool, shared storage, or repurposing of the extra Windows disk was completed.
