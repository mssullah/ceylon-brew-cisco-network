
# CEYLON BREW HOUSE
# VLAN Design Documentation

## 1. Introduction

Virtual Local Area Networks (VLANs) are used to divide
a physical network into multiple logical networks.

In the CEYLON BREW HOUSE infrastructure, VLANs separate
administrative operations, Point of Sale systems,
guest wireless users, and network management.

Although all devices connect through the same physical
switch, VLANs allow them to operate in separate
broadcast domains.

This improves network organization, security,
and traffic management.

---

## 2. VLAN Design Objectives

The main objectives of VLAN implementation are:

- Separate business departments logically.
- Isolate guest wireless users from internal systems.
- Separate POS devices from guest devices.
- Organize network management traffic.
- Reduce unnecessary broadcast traffic.
- Provide controlled communication between VLANs.
- Simplify network administration.

---

## 3. VLAN Allocation

The network uses four VLANs.

| VLAN ID | VLAN Name | Purpose | Network |
|---|---|---|---|
| 10 | BREW-ADMIN | Administrative operations | 192.168.10.0/24 |
| 20 | BREW-POS | Point of Sale operations | 192.168.20.0/24 |
| 30 | BREW-GUEST | Guest wireless access | 192.168.30.0/24 |
| 99 | BREW-MGMT | Network management | 192.168.99.0/24 |

Each VLAN has a separate IP subnet and default gateway.

---

## 4. VLAN 10 - BREW-ADMIN

VLAN 10 is dedicated to administrative activities.

Connected devices:

- CBH-PC-ADMIN-01
- CBH-PRN-OFFICE-01

Switch ports:

| Switch Port | Connected Device |
|---|---|
| Fa0/1 | Admin PC |
| Fa0/2 | Office Printer |

Network:

192.168.10.0/24

Default Gateway:

192.168.10.1

The administrative network is also used as the
authorized source network for SSH management.

---

## 5. VLAN 20 - BREW-POS

VLAN 20 is dedicated to Point of Sale operations.

Connected devices:

- CBH-POS-01
- CBH-PRN-POS-01

Switch ports:

| Switch Port | Connected Device |
|---|---|
| Fa0/6 | POS Terminal |
| Fa0/7 | POS Receipt Printer |

Network:

192.168.20.0/24

Default Gateway:

192.168.20.1

Separating POS devices into their own VLAN helps
protect business transaction systems from
unnecessary guest network traffic.

---

## 6. VLAN 30 - BREW-GUEST

VLAN 30 provides wireless access for coffee shop guests.

Connected device:

- CBH-AP-01

Switch port:

Fa0/11

Wireless SSID:

CEYLON_BREW_GUEST

Network:

192.168.30.0/24

Default Gateway:

192.168.30.1

Guest wireless clients receive their IP addresses
through the BREW-GUEST DHCP pool.

An ACL on the router restricts guest access to
the internal administrative, POS, and management
networks.

Guests can still reach the simulated external network.

---

## 7. VLAN 99 - BREW-MGMT

VLAN 99 is reserved for network management.

Network:

192.168.99.0/24

Default Gateway:

192.168.99.1

Switch management IP:

192.168.99.2

This VLAN provides a separate logical network
for managing infrastructure devices.

---

## 8. Switch Port Allocation

The Cisco Catalyst 2960 switch uses the following
port allocation.

| Interface | VLAN | Description | Status |
|---|---|---|---|
| Fa0/1 | 10 | Admin PC | Active |
| Fa0/2 | 10 | Office Printer | Active |
| Fa0/3 | 10 | Unused Admin Port | Shutdown |
| Fa0/4 | 10 | Unused Admin Port | Shutdown |
| Fa0/5 | 10 | Unused Admin Port | Shutdown |
| Fa0/6 | 20 | POS Terminal | Active |
| Fa0/7 | 20 | POS Receipt Printer | Active |
| Fa0/8 | 20 | Unused POS Port | Shutdown |
| Fa0/9 | 20 | Unused POS Port | Shutdown |
| Fa0/10 | 20 | Unused POS Port | Shutdown |
| Fa0/11 | 30 | Guest Access Point | Active |
| Fa0/12-23 | - | Unused Ports | Shutdown |
| Fa0/24 | 10,20,30,99 | Router Trunk | Active |

---

## 9. Access Ports

An access port carries traffic for one assigned VLAN.

The switch uses access ports to connect end devices.

Examples:

Fa0/1 belongs to VLAN 10.

Fa0/6 belongs to VLAN 20.

Fa0/11 belongs to VLAN 30.

These ports are configured using:

switchport mode access

switchport access vlan VLAN_ID

This ensures that each connected device is placed
into the intended logical network.

---

## 10. Trunk Port Configuration

A trunk port carries traffic from multiple VLANs
over a single physical connection.

In this project, switch port Fa0/24 connects
CBH-SW-01 to CBH-RTR-01.

The trunk uses IEEE 802.1Q VLAN tagging.

Allowed VLANs:

10, 20, 30, 99

Configuration:

interface FastEthernet0/24
 description TRUNK-TO-CBH-RTR-01
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99

This connection allows the router to receive
traffic from all four VLANs.

---

## 11. Router-on-a-Stick Integration

The project uses Router-on-a-Stick inter-VLAN routing.

The main router uses one physical LAN interface:

GigabitEthernet0/1

Four logical subinterfaces are configured:

| Router Subinterface | VLAN | Gateway |
|---|---|---|
| G0/1.10 | 10 | 192.168.10.1 |
| G0/1.20 | 20 | 192.168.20.1 |
| G0/1.30 | 30 | 192.168.30.1 |
| G0/1.99 | 99 | 192.168.99.1 |

Each subinterface uses 802.1Q encapsulation.

Example:

interface GigabitEthernet0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

The router processes traffic between VLANs
according to its routing table and security ACLs.

---

## 12. VLAN Communication and Security

VLAN separation creates individual broadcast domains,
but it does not automatically prevent all
communication between VLANs.

The router provides inter-VLAN routing.

Access Control Lists control which traffic
is permitted or denied.

The guest isolation ACL provides the following policy:

| Source | Destination | Policy |
|---|---|---|
| Guest VLAN | Admin VLAN | Denied |
| Guest VLAN | POS VLAN | Denied |
| Guest VLAN | Management VLAN | Denied |
| Guest VLAN | Simulated external network | Permitted |
| Admin VLAN | POS VLAN | Permitted |

These policies were tested in Cisco Packet Tracer.

---

## 13. Unused Port Security

Unused switch ports are administratively disabled.

The following interfaces are shut down:

- Fa0/3-Fa0/5
- Fa0/8-Fa0/10
- Fa0/12-Fa0/23

This reduces the risk of unauthorized physical
network connections.

Connected endpoint ports also use switch
port security with sticky MAC address learning
and restrict violation mode.

---

## 14. VLAN Verification Commands

The following Cisco commands can be used to verify
the VLAN configuration.

### Display VLAN Information

show vlan brief

### Display Trunk Information

show interfaces trunk

### Display Interface Configuration

show running-config

### Display Switch Management Address

show ip interface brief

### Display Port Security Information

show port-security

### Display Secure MAC Addresses

show port-security address

---

## 15. VLAN Design Benefits

The VLAN design provides:

1. Logical separation of business departments.
2. Improved network organization.
3. Reduced unnecessary broadcast traffic.
4. Better control of inter-department communication.
5. Guest network isolation.
6. Easier troubleshooting.
7. Centralized inter-VLAN routing.
8. A structured foundation for future expansion.

---

## 16. Conclusion

The CEYLON BREW HOUSE network uses four VLANs
to separate administrative, POS, guest wireless,
and management traffic.

The Cisco Catalyst 2960 switch provides VLAN-based
network segmentation and connects end devices.

The trunk connection between the switch and router
carries traffic from multiple VLANs.

The main router provides inter-VLAN routing,
while ACLs enforce the intended communication policies.

This design provides an organized and manageable
network infrastructure for a small business.

---

**Project:** CEYLON BREW HOUSE Network Infrastructure

**Platform:** Cisco Packet Tracer

**Device:** Cisco Catalyst 2960 and Cisco 2911

**Technology:** VLAN, IEEE 802.1Q, Router-on-a-Stick

**End of Document**
