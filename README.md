# Enterprise Homelab | Linux, Networking & Cybersecurity

## Overview

Welcome to my Enterprise Homelab project!

This repository documents the planning, deployment, configuration, and ongoing development of my three-node virtualization environment built using Dell OptiPlex Micro computers and Proxmox Virtual Environment (VE).

I created this homelab to gain practical experience in **Linux system administration, networking, virtualization, cybersecurity, and infrastructure automation**, while developing the technical skills needed for a career in Linux engineering and cloud security.

The project covers the entire infrastructure lifecycle, from researching and purchasing hardware to deploying servers, troubleshooting issues, implementing security controls, and managing self-hosted services.

## Hardware & Infrastructure

| Component | Configuration |
|---|---|
| Servers | 3 × Dell OptiPlex 7050 Micro |
| Processors | Intel Core i7 |
| Memory | 16 GB RAM per node (48 GB total) |
| Storage | 512 GB M.2 SSD per node |
| Hypervisor | Proxmox VE |
| Network Switch | NETGEAR MS308E (planned) |
| Rack | GeekPi Rackmate T1 |
| External Storage | TerraMaster D4-320 (planned integration) |

Hardware selections and upgrades are documented to explain the technical requirements, budget considerations, and tradeoffs behind each decision.

## Project Objectives

- **Linux Administration:** Develop proficiency in RHEL, Bash scripting, user management, storage, system services, and troubleshooting.
- **Virtualization:** Deploy and administer virtual machines, containers, and a three-node Proxmox cluster.
- **Networking:** Practice TCP/IP, DNS, DHCP, VLANs, routing, firewalls, and packet analysis.
- **Cybersecurity:** Implement network segmentation, security monitoring, system hardening, and vulnerability management.
- **Automation:** Build repeatable administrative workflows using Bash, Ansible, and infrastructure automation tools.
- **Self-Hosting:** Deploy personal services, including media servers, game servers, and network-wide DNS filtering.
- **Documentation:** Maintain technical guides, configuration records, troubleshooting reports, and architecture diagrams.

## Project Roadmap

| Phase | Description | Status |
|---|---|---|
| 00 | Hardware Research, Selection & Upgrades | Completed |
| 01 | Proxmox VE Installation on Three Nodes | Completed |
| 02 | Management Network Configuration & Troubleshooting | Completed |
| 03 | Proxmox Cluster Configuration | Planned |
| 04 | RHEL Virtual Machines & Linux Administration | Planned |
| 05 | Networking, VLANs & Packet Analysis | Planned |
| 06 | Cybersecurity Monitoring & System Hardening | Planned |
| 07 | Bash Scripting & Infrastructure Automation | Planned |
| 08 | Self-Hosted Services & Storage | Planned |
| 09 | Monitoring, Backups & Disaster Recovery | Planned |

*Project statuses will be updated as implementation and validation progress.*

## Documentation

Each project includes, where applicable:

- Project objectives and technical requirements
- Hardware and software decisions
- Implementation steps and Linux commands
- Network diagrams and configuration examples
- Troubleshooting procedures and resolutions
- Validation results and lessons learned

## Current Progress

Proxmox VE has been successfully installed on all three Dell OptiPlex systems. Each node is accessible remotely through its web management interface.

The next milestone is to verify inter-node connectivity, configure hostname resolution, and establish the Proxmox cluster.

## Career Development

This homelab serves as a practical learning environment and technical portfolio, demonstrating skills relevant to:

Linux Systems Administrator

Systems Engineer

Infrastructure Engineer

Cloud Engineer

Cloud Security Engineer

Cybersecurity Engineer

The goal is to build, troubleshoot, secure, automate, and document an increasingly capable infrastructure environment while applying concepts used in professional IT operations.

Project Status: Active Development

Primary Focus: Linux Engineering | Networking | Virtualization | Cloud Security
