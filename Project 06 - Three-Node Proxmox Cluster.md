# Project 06 — Three-Node Proxmox Cluster

## Objective
Create one management environment for pve01, pve02, and pve03, and verify quorum.

## Problem
The hosts were separate installations. A CLI join attempt from pve02 failed with certificate hostname verification on pve01’s API endpoint.

## Diagnostic Approach
Confirmed unique hostnames, stable addressing, network connectivity, synchronized clocks, and no existing VMs or containers before joining. Inspected cluster membership and obtained join information directly from pve01.

### Commands Used

```bash
qm list
pct list
pvecm create homelab
pvecm status
```

- `qm list`: lists virtual machines on the node.
- `pct list`: lists Linux containers.
- `pvecm create homelab`: creates the named cluster; it was run only on pve01.
- `pvecm status`: displays cluster membership, votes, and quorum.

## Resolution
Created `homelab` on pve01. Used Datacenter → Cluster → Join Information on pve01 and the GUI Join Cluster workflow on pve02 and pve03. The copied information supplied the existing node’s certificate fingerprint. Entered credentials locally; certificate verification was not disabled.

## Validation
All three nodes appeared in the same cluster. Final status showed 3 nodes, 3 expected votes, 3 total votes, quorum 2, and `Quorate: Yes`.

## Lessons Learned
- Joining an existing cluster is different from creating a new cluster on every node.
- Three voting nodes normally require two communicating votes for quorum.
- A cluster centralizes management and configuration; it does not pool all RAM for a single VM.
- Certificate identity checks are separate from simple network reachability.
- The underlying cause of the certificate mismatch was not independently proven.

## Future Improvements
Practice migration and recovery with disposable workloads. Automatic HA, shared/replicated guest storage, and failure-injection testing remain unconfigured.
