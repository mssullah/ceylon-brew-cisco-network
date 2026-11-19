
# CEYLON BREW HOUSE
# IP Addressing Plan

## 1. Introduction

This document describes the IPv4 addressing scheme used in
the CEYLON BREW HOUSE network infrastructure.

The network uses private IPv4 addresses to organize devices
into separate logical networks.

Each department and network service is assigned a dedicated
subnet through VLAN segmentation.

The addressing plan helps administrators identify devices,
manage network communication, and troubleshoot connectivity.

---

## 2. IPv4 Addressing Overview

The network uses IPv4 addressing.

An IPv4 address contains 32 bits and is normally represented
as four decimal octets.

Example:

192.168.10.21

Each octet represents 8 bits.

The project uses the following subnet mask for its internal
VLAN networks:

255.255.255.0

CIDR notation:

/24

A /24 network contains:

- Total addresses: 256
- Usable host addresses: 254
- Network address: First address
- Broadcast address: Last address

For example:

Network: 192.168.10.0/24
First usable: 192.168.10.1
Last usable: 192.168.10.254
Broadcast: 192.168.10.255

---

## 3. VLAN IP Address Allocation

Four VLANs are configured in the network.

| VLAN ID | VLAN Name | Network | Subnet Mask | Gateway |
|---|---|---|---|---|
| 10 | BREW-ADMIN | 192.168.10.0/24 | 255.255.255.0 | 192.168.10.1 |
| 20 | BREW-POS | 192.168.20.0/24 | 255.255.255.0 | 192.168.20.1 |
| 30 | BREW-GUEST | 192.168.30.0/24 | 255.255.255.0 | 192.168.30.1 |
| 99 | BREW-MGMT | 192.168.99.0/24 | 255.255.255.0 | 192.168.99.1 |

Each VLAN has an independent broadcast domain.

The default gateway is configured on the corresponding
router subinterface.

---

## 4. Static IP Address Assignments

Static IP addresses are reserved for important network
infrastructure and devices.

| Device | IP Address | VLAN | Purpose |
|---|---|---|---|
| CBH-RTR-01 | 192.168.10.1 | 10 | Admin gateway |
| CBH-RTR-01 | 192.168.20.1 | 20 | POS gateway |
| CBH-RTR-01 | 192.168.30.1 | 30 | Guest gateway |
| CBH-RTR-01 | 192.168.99.1 | 99 | Management gateway |
| Office Printer | 192.168.10.10 | 10 | Office printing |
| POS Receipt Printer | 192.168.20.10 | 20 | POS printing |
| CBH-SW-01 | 192.168.99.2 | 99 | Switch management |

These addresses are reserved to prevent conflicts
with dynamically assigned client addresses.

---

## 5. DHCP Address Allocation

The main router CBH-RTR-01 provides DHCP services
for VLANs 10, 20, and 30.

DHCP automatically assigns network configuration
to connected client devices.

The DHCP configuration includes:

- IPv4 address
- Subnet mask
- Default gateway
- DNS server address

### DHCP Configuration Summary

| DHCP Pool | Network | Gateway | DNS Server |
|---|---|---|---|
| BREW-ADMIN | 192.168.10.0/24 | 192.168.10.1 | 8.8.8.8 |
| BREW-POS | 192.168.20.0/24 | 192.168.20.1 | 8.8.8.8 |
| BREW-GUEST | 192.168.30.0/24 | 192.168.30.1 | 8.8.8.8 |

### DHCP Excluded Address Range

The router excludes the following range in each VLAN:

192.168.x.1 - 192.168.x.20

This reserves addresses for gateways, servers, printers,
and other infrastructure devices.

The remaining address range can be allocated to DHCP clients.

**Note:** VLAN 99 has excluded addresses configured,
but no DHCP pool is present in the supplied router configuration.

---

## 6. Example Client Address Assignments

The following client addresses were observed during
the Packet Tracer implementation.

| Client Device | Assigned IP | Default Gateway | VLAN |
|---|---|---|---|
| CBH-PC-ADMIN-01 | 192.168.10.21 | 192.168.10.1 | 10 |
| CBH-POS-01 | 192.168.20.21 | 192.168.20.1 | 20 |
| Guest wireless client | 192.168.30.x | 192.168.30.1 | 30 |

The guest address depends on the DHCP lease assigned
to the wireless client.

---

## 7. WAN IP Addressing

The main router connects to the ISP router using
a point-to-point WAN network.

The WAN network uses:

203.0.113.0/30

Subnet mask:

255.255.255.252

### WAN Address Allocation

| Device | Interface | IP Address |
|---|---|---|
| CBH-ISP-01 | GigabitEthernet0/1 | 203.0.113.1 |
| CBH-RTR-01 | GigabitEthernet0/0 | 203.0.113.2 |

### WAN Subnet Calculation

Network address: 203.0.113.0

First usable address: 203.0.113.1

Last usable address: 203.0.113.2

Broadcast address: 203.0.113.3

A /30 subnet provides two usable host addresses,
which is suitable for this point-to-point connection.

---

## 8. Simulated Internet Address

The ISP router uses a loopback interface to represent
an external Internet destination.

| Device | Interface | IP Address |
|---|---|---|
| CBH-ISP-01 | Loopback0 | 8.8.8.8/32 |

This address is used to test external connectivity
from the internal VLANs.

It represents a simulated Internet destination
within Cisco Packet Tracer.

It is not a real public Internet connection.

---

## 9. Default Routing

The main router uses a default static route to forward
unknown destinations toward the ISP router.

Command:

ip route 0.0.0.0 0.0.0.0 203.0.113.1

The ISP router uses static routes to reach the internal
VLAN networks through the main router.

These routes provide return connectivity for the
simulated network environment.

---

## 10. Network Address Translation

The main router uses NAT Overload, also known as PAT.

The internal VLAN networks use private IPv4 addresses.

NAT translates permitted internal traffic to the
WAN interface address:

203.0.113.2

The extended ACL CBH-NAT controls which traffic
is translated.

Admin traffic destined for the ISP router at
203.0.113.1 is excluded from NAT.

This allows the ISP management ACL to identify
the original Admin subnet address.

---

## 11. IP Addressing Design Benefits

The addressing scheme provides several benefits:

1. Logical separation of business departments.
2. Easier network administration.
3. Reduced broadcast domain size.
4. Organized DHCP allocation.
5. Reserved addresses for important devices.
6. Easier network troubleshooting.
7. Support for controlled inter-VLAN communication.
8. A structured foundation for future expansion.

---

## 12. Conclusion

The CEYLON BREW HOUSE network uses a structured IPv4
addressing plan based on four VLANs and a dedicated
point-to-point WAN network.

The /24 internal subnets provide sufficient address
capacity for the simulated business environment.

DHCP simplifies client configuration, while static
addresses provide predictable connectivity for
network infrastructure and printers.

The WAN /30 subnet provides an efficient addressing
solution for the router-to-router connection.

Overall, the addressing plan supports organized,
manageable, and secure network operations.

---

**Project:** CEYLON BREW HOUSE Network Infrastructure

**Platform:** Cisco Packet Tracer

**Document:** IP Addressing Plan

**End of Document**
