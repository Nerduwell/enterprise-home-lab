# Troubleshooting Records

## 1. DHCP pool overlapped static management addresses

**Observation:** The router's automatic address pool began at `192.168.1.11`, overlapping pve02 (`.11`) and pve03 (`.12`). No active duplicate IP use was found.

**Action:** Changed the pool start to `.100`, retaining `.254` as the end; checked connected devices/leases for duplicates.

**Outcome:** Static host addresses are outside the automatic assignment pool. Existing node addresses were not changed. This was prevention of a potential conflict, not proof that an active duplicate-address outage occurred.

## 2. Package refresh failed with HTTP 401

**Observation:** `apt update` contacted the Ceph Squid enterprise repository and returned `401 Unauthorized`, even after the main Proxmox no-subscription repository was enabled.

**Cause:** The separate Ceph enterprise source required a subscription. The IP in the error was the contacted remote endpoint, not evidence that the home IP was blocked.

**Action:** Disabled that enterprise repository in the node's repository settings; left Debian sources and `pve-no-subscription` enabled.

**Outcome:** Package refresh completed without errors; host upgrades and reboots completed. Ceph was not deployed.

## 3. Cluster join certificate verification failed

**Observation:** The CLI join to pve01 on port 8006 returned `hostname verification failed`.

**Action:** Copied **Join Information** from pve01's own interface and used the GUI join workflow on pve02, then pve03. This provided the existing node's fingerprint with the join configuration.

**Outcome:** All nodes joined the same cluster; status confirmed three votes and quorum. I did not prove a deeper certificate-generation or DNS root cause, and did not disable certificate validation.

## 4. RHEL installer reported no bootable disk

**Observation:** The VM could not boot the attached RHEL media.

**Checks:** Checked the CD/DVD attachment and boot order.

**Cause found:** The ISO was `aarch64` for ARM, while the Intel OptiPlex VM used x86_64.

**Action and outcome:** Replaced the attached media with the x86_64 ISO. RHEL booted, installed to the 40 GiB virtual disk, and reached the login screen.

## 5. Extra Windows disk confused storage identification

**Observation:** `sda` showed a roughly 500 GB disk with FAT/NTFS-style partitions, unlike the expected Proxmox storage layout.

**Resolution:** Identified it as an additional drive that came with one OptiPlex. The Proxmox target was the separate `nvme0n1` SSD. No formatting or removal of the extra disk was performed.

## General approach

Identify the actual failing layer, inspect before changing configuration, make a targeted change, and validate the result. A symptom such as failed name resolution does not establish a DNS-only fault; a certificate error does not establish a network outage.
