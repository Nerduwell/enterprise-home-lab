# Project 03 — Management Network and DHCP Configuration

## Objective
Validate connectivity across three Proxmox hosts and separate manually configured server addresses from DHCP assignments.

## Problem
The router’s DHCP pool overlapped two manually configured host addresses, creating a risk of future duplicate assignments. No active duplicate IP use was found.

## Diagnostic Approach
Checked each node’s interfaces, routing, router reachability, external connectivity, name resolution, and clock synchronization. Reviewed the router’s DHCP range and device leases. Tested communication between all three nodes.

### Commands Used

```bash
ip -br addr
ip route
ping -c 4 <ROUTER_IP>
ping -c 4 <PUBLIC_TEST_IP>
getent ahostsv4 debian.org
hostnamectl
timedatectl
```

- `ip -br addr`: lists interface addresses in brief format.
- `ip route`: displays the routing table and default gateway.
- `ping -c 4`: sends four ICMP echo requests, then stops.
- `getent ahostsv4`: requests IPv4 results through the configured name services.
- `hostnamectl`: displays the system hostname and operating-system details.
- `timedatectl`: displays time settings and synchronization status.

## Resolution
Moved the automatic DHCP pool above the three hosts’ manually configured addresses. Retained the existing gateway and node addresses. Checked for existing duplicate leases. Connected the router LAN port and the nodes through the physical switch.

## Validation
All three nodes were reachable through their web interfaces. Router, internet-IP, and inter-node ping tests returned 0% packet loss during checks. Hostname lookups returned addresses. Each host had correct local time, active NTP, and a synchronized clock.

## Lessons Learned
- DHCP pool changes prevent future assignments in the excluded range but do not create reservations or necessarily clear existing leases.
- A successful public-IP ping and a successful hostname lookup test different layers.
- `vmbr0` is a Linux bridge connecting the host and guests to the network.
- A patch panel organizes connections; a switch forwards network traffic.

## Future Improvements
Configure the managed switch, plan VLANs, and document segmentation after the baseline is stable. Those changes have not yet been implemented.

> Angle-bracket values are placeholders, not commands to paste unchanged. Replace them locally. Actual network addresses are omitted.
