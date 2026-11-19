
# CEYLON BREW HOUSE
# Network Architecture Documentation

## 1. Project Overview

CEYLON BREW HOUSE is a simulated coffee shop business network
infrastructure designed and implemented using Cisco Packet Tracer.

The purpose of this project is to design a secure, reliable,
manageable, and scalable network infrastructure that supports
the daily operations of a coffee shop.

The network provides connectivity for administrative staff,
Point of Sale (POS) systems, printers, wireless guests, and
network management devices.

The infrastructure uses Cisco routers and switches to provide
network segmentation, inter-VLAN routing, DHCP services,
Network Address Translation (NAT), Access Control Lists (ACLs),
port security, and secure remote administration using SSH.

The project also includes a simulated Internet connection
through an ISP router.

---

## 2. Project Objectives

The main objectives of this project are:

- Design a structured business network infrastructure.
- Separate business departments using VLAN technology.
- Enable communication between authorized VLANs.
- Provide automatic IP address allocation using DHCP.
- Protect business systems from unauthorized guest access.
- Implement secure wireless guest connectivity.
- Configure network access security using ACLs.
- Protect switch ports using Port Security.
- Enable secure remote device management using SSH.
- Provide simulated Internet connectivity using NAT.
- Demonstrate network functionality through testing.

---

## 3. Network Architecture

The network follows a centralized router-on-a-stick architecture.

The main router, CBH-RTR-01, connects to the core access switch,
CBH-SW-01, through a VLAN trunk link.

The router uses subinterfaces to provide gateways for the
different VLANs.

The switch connects the business devices and wireless
access point.

The ISP router, CBH-ISP-01, provides the simulated external
network connection.

### Main Network Devices

| Device | Model | Responsibility |
|---|---|---|
| CBH-ISP-01 | Cisco 2911 | Simulated ISP router |
| CBH-RTR-01 | Cisco 2911 | Main router and gateway |
| CBH-SW-01 | Cisco 2960 | LAN switching and VLANs |
| CBH-AP-01 | AccessPoint-PT | Guest wireless access |

### End Devices

| Device | Purpose |
|---|---|
| CBH-PC-ADMIN-01 | Administrative workstation |
| CBH-PRN-OFFICE-01 | Office printer |
| CBH-POS-01 | Point of Sale terminal |
| CBH-PRN-POS-01 | POS receipt printer |
| CBH-GUEST-01 | Guest wireless laptop |
| CBH-GUEST-02 | Guest wireless laptop |

---

## 4. Physical Network Design

The physical network is organized around a central
Cisco Catalyst 2960 switch.

The main router connects to switch port FastEthernet0/24
using an IEEE 802.1Q trunk.

The switch distributes network connectivity to the
administrative devices, POS devices, and wireless access point.

The router connects to the simulated ISP using its
GigabitEthernet0/0 interface.

### Physical Connections

| Source Device | Interface | Destination Device | Interface |
|---|---|---|---|
| CBH-RTR-01 | G0/1 | CBH-SW-01 | Fa0/24 |
| CBH-RTR-01 | G0/0 | CBH-ISP-01 | G0/1 |
| CBH-SW-01 | Fa0/1 | Admin PC | Ethernet |
| CBH-SW-01 | Fa0/2 | Office Printer | Ethernet |
| CBH-SW-01 | Fa0/6 | POS Terminal | Ethernet |
| CBH-SW-01 | Fa0/7 | POS Receipt Printer | Ethernet |
| CBH-SW-01 | Fa0/11 | Guest Access Point | Ethernet |

---

## 5. VLAN Architecture

VLANs are used to divide the business network into
separate logical networks.

This improves network organization, security, and
traffic management.

The project uses four VLANs.

| VLAN ID | VLAN Name | Network | Default Gateway |
|---|---|---|---|
| 10 | BREW-ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| 20 | BREW-POS | 192.168.20.0/24 | 192.168.20.1 |
| 30 | BREW-GUEST | 192.168.30.0/24 | 192.168.30.1 |
| 99 | BREW-MGMT | 192.168.99.0/24 | 192.168.99.1 |

### VLAN 10 - BREW-ADMIN

This VLAN is dedicated to administrative operations.

It provides connectivity for:

- Administrative workstation
- Office printer
- Network administration activities

Network: 192.168.10.0/24

Gateway: 192.168.10.1

### VLAN 20 - BREW-POS

This VLAN is dedicated to Point of Sale operations.

It provides connectivity for:

- POS terminal
- POS receipt printer

Network: 192.168.20.0/24

Gateway: 192.168.20.1

The separation of POS devices helps protect payment-related
business operations from guest wireless traffic.

### VLAN 30 - BREW-GUEST

This VLAN provides wireless Internet access for coffee shop guests.

Guest devices connect through the wireless access point.

Network: 192.168.30.0/24

Gateway: 192.168.30.1

Guest users are restricted from accessing internal
administrative, POS, and management networks.

### VLAN 99 - BREW-MGMT

This VLAN is reserved for network management.

It provides a dedicated management network for infrastructure
devices.

Network: 192.168.99.0/24

Gateway: 192.168.99.1

Switch management address: 192.168.99.2

---

## 6. IP Addressing Design

The network uses private IPv4 addressing.

Each VLAN has its own /24 subnet.

The subnet mask is:

255.255.255.0

The following addresses are reserved for infrastructure
and manually configured devices.

| Device | IP Address | VLAN |
|---|---|---|
| Main Router - Admin Gateway | 192.168.10.1 | 10 |
| Main Router - POS Gateway | 192.168.20.1 | 20 |
| Main Router - Guest Gateway | 192.168.30.1 | 30 |
| Main Router - Management Gateway | 192.168.99.1 | 99 |
| Office Printer | 192.168.10.10 | 10 |
| POS Receipt Printer | 192.168.20.10 | 20 |
| Switch Management | 192.168.99.2 | 99 |

The router DHCP configuration excludes addresses
192.168.x.1 through 192.168.x.20 in each VLAN.

This prevents DHCP from allocating reserved addresses
to client devices.

---

## 7. Inter-VLAN Routing

Inter-VLAN routing allows devices in different VLANs
to communicate when permitted.

This project uses the Router-on-a-Stick method.

The physical router interface GigabitEthernet0/1 is connected
to switch port FastEthernet0/24 through a trunk link.

The router uses four logical subinterfaces:

- G0/1.10 - VLAN 10
- G0/1.20 - VLAN 20
- G0/1.30 - VLAN 30
- G0/1.99 - VLAN 99

Each subinterface uses IEEE 802.1Q encapsulation
and acts as the default gateway for its VLAN.

This architecture allows multiple VLANs to communicate
through one physical router interface.

Access Control Lists determine which traffic is permitted.

---

## 8. DHCP Services

Dynamic Host Configuration Protocol (DHCP) automatically
assigns network settings to client devices.

The main router provides DHCP services for:

- BREW-ADMIN
- BREW-POS
- BREW-GUEST

The DHCP configuration provides:

- IP address
- Subnet mask
- Default gateway
- DNS server address

The configured DNS server address is 8.8.8.8.

The VLAN 99 network has reserved addresses but no DHCP
pool is shown in the supplied router configuration.

---

## 9. Wireless Network Architecture

The guest wireless network is provided through CBH-AP-01.

Wireless network name (SSID):

CEYLON_BREW_GUEST

Security:

WPA2-PSK

Encryption:

AES

The wireless access point connects to switch port Fa0/11,
which is assigned to VLAN 30.

Wireless guest devices receive IP addresses from the
BREW-GUEST DHCP pool.

This keeps guest wireless traffic separated from
business operations.

---

## 10. Network Security Architecture

Security is implemented at multiple layers.

### 10.1 VLAN Segmentation

Different business functions are separated into individual VLANs.

This reduces unnecessary communication between departments.

### 10.2 Guest Isolation ACL

The extended ACL BREW-GUEST-ISOLATION is applied inbound
on router subinterface GigabitEthernet0/1.30.

It:

- Permits DHCP traffic.
- Blocks guest access to VLAN 10.
- Blocks guest access to VLAN 20.
- Blocks guest access to VLAN 99.
- Permits other guest traffic.

This allows guests to access the simulated external network
without accessing protected internal networks.

### 10.3 Port Security

Port security is enabled on connected endpoint ports:

- Fa0/1 - Administrative PC
- Fa0/2 - Office printer
- Fa0/6 - POS terminal
- Fa0/7 - POS receipt printer
- Fa0/11 - Wireless access point

Sticky MAC address learning is configured.

The violation mode is set to restrict.

This helps prevent unauthorized devices from using
protected switch ports.

### 10.4 Unused Port Protection

Unused switch ports are administratively shut down.

This reduces the risk of unauthorized physical connections.

### 10.5 SSH Remote Management

Secure Shell (SSH) version 2 is configured on the
network devices.

The local administrator account is used for authentication.

VTY access is restricted using the CBH-SSH-MGMT ACL.

Only the Admin subnet, 192.168.10.0/24, is permitted
by this management ACL.

### 10.6 NAT Exemption for ISP Management

An extended ACL named CBH-NAT is used for NAT overload.

It excludes traffic from the Admin subnet destined for
the ISP router address 203.0.113.1 from translation.

This preserves the original Admin PC source address
when accessing the ISP router through SSH.

Other permitted VLAN traffic is translated using
the router's WAN interface address.

---

## 11. WAN and Simulated Internet Connectivity

The main router connects to the simulated ISP router
through a point-to-point WAN network.

WAN addressing:

| Device | Interface | IP Address |
|---|---|---|
| CBH-ISP-01 | G0/1 | 203.0.113.1/30 |
| CBH-RTR-01 | G0/0 | 203.0.113.2/30 |

The main router uses a default route pointing to
203.0.113.1.

The ISP router uses static routes to reach the internal
VLAN networks through 203.0.113.2.

The ISP router also uses Loopback0:

8.8.8.8/32

This loopback represents a simulated external Internet
destination for connectivity testing.

The main router uses NAT overload to translate
internal client traffic to its WAN interface address.

**Note:** This is a simulated Internet environment.
It does not provide a connection to the real public Internet.

---

## 12. Network Traffic Flow

### Administrative Traffic

1. The Admin PC connects to switch port Fa0/1.
2. The switch assigns the port to VLAN 10.
3. The Admin PC receives an IP address through DHCP.
4. The default gateway is 192.168.10.1.
5. The router forwards permitted traffic to other networks
   or the simulated ISP.

### POS Traffic

1. The POS terminal connects to Fa0/6.
2. The switch assigns the port to VLAN 20.
3. The POS terminal receives an IP address through DHCP.
4. The default gateway is 192.168.20.1.
5. The router handles permitted internal and external traffic.

### Guest Wireless Traffic

1. A guest connects to CEYLON_BREW_GUEST.
2. The wireless access point connects the guest to VLAN 30.
3. The guest receives an IP address through DHCP.
4. The default gateway is 192.168.30.1.
5. The guest isolation ACL filters traffic.
6. Permitted external traffic is forwarded through NAT.

---

## 13. Network Testing and Verification

The network was tested using Cisco Packet Tracer.

The following tests were performed.

| Test | Expected Result | Status |
|---|---|---|
| Admin PC to POS terminal | Successful | Passed |
| POS terminal to Admin PC | Successful | Passed |
| Guest to Admin network | Blocked | Passed |
| Guest to POS network | Blocked | Passed |
| Guest to simulated Internet | Successful | Passed |
| Admin to simulated Internet | Successful | Passed |
| POS to simulated Internet | Successful | Passed |
| Guest SSH to main router | Blocked | Passed |
| Admin SSH to main router | Successful | Passed |
| Admin SSH to switch | Successful | Passed |
| Admin SSH to ISP router | Successful | Passed |

These tests demonstrate the intended connectivity,
segmentation, and management access controls in the
simulated network.

---

## 14. Conclusion

The CEYLON BREW HOUSE network infrastructure demonstrates
how a small business can implement a structured and
secure network using Cisco networking technologies.

The project combines VLAN segmentation, inter-VLAN routing,
DHCP, wireless connectivity, NAT, ACLs, port security,
and SSH management.

The guest network is isolated from internal business networks
while maintaining access to the simulated external network.

Administrative users can manage the network infrastructure
through secure SSH connections.

Overall, the project demonstrates practical networking skills
and provides a foundation for further improvements such as
centralized logging, network monitoring, redundant connectivity,
and advanced security controls.

---

## 15. Project Information

**Project Name:** CEYLON BREW HOUSE Network Infrastructure

**Simulation Software:** Cisco Packet Tracer

**Network Devices:** Cisco 2911 Router, Cisco Catalyst 2960 Switch

**Routing Method:** Router-on-a-Stick

**Network Protocols and Technologies:**
- IPv4
- VLAN
- IEEE 802.1Q
- DHCP
- NAT/PAT
- ACL
- SSH
- Port Security
- WPA2-PSK

**Project Type:** Small Business Network Infrastructure

**Purpose:** Practical networking portfolio project

---

**End of Documentation**
