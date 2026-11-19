
# CEYLON BREW HOUSE
# Network Security Design Documentation

## 1. Introduction

This document explains the security architecture
implemented in the CEYLON BREW HOUSE network infrastructure.

The project uses multiple network security mechanisms
to protect business operations, administrative systems,
POS devices, guest wireless users, and network infrastructure.

The security design combines VLAN segmentation,
Access Control Lists (ACLs), switch port security,
SSH remote management, NAT controls, and wireless
encryption.

The implementation was developed and tested using
Cisco Packet Tracer.

---

## 2. Security Objectives

The main security objectives are:

- Protect internal business networks.
- Separate guest wireless traffic from business systems.
- Restrict unauthorized network access.
- Protect switch ports against unauthorized devices.
- Secure remote network administration.
- Control traffic between VLANs.
- Protect unused physical switch ports.
- Provide controlled external network connectivity.
- Maintain a structured and manageable security design.

---

## 3. Security Architecture Overview

The network uses a layered security approach.

| Security Mechanism | Purpose |
|---|---|
| VLAN Segmentation | Separates business networks |
| Access Control Lists | Controls network traffic |
| Port Security | Restricts unauthorized devices |
| SSH Version 2 | Secures remote administration |
| Management ACL | Restricts device management access |
| WPA2-PSK | Protects guest wireless access |
| NAT/PAT | Translates internal network addresses |
| Unused Port Shutdown | Disables unused switch interfaces |
| MOTD Banner | Displays authorized-access warning |

Each mechanism contributes to the overall
network security design.

---

## 4. VLAN-Based Network Segmentation

The network is divided into four VLANs.

| VLAN | Name | Purpose |
|---|---|---|
| 10 | BREW-ADMIN | Administrative operations |
| 20 | BREW-POS | Point of Sale operations |
| 30 | BREW-GUEST | Guest wireless access |
| 99 | BREW-MGMT | Network management |

VLAN segmentation creates separate broadcast domains.

This reduces unnecessary traffic between departments
and provides a foundation for implementing
network access-control policies.

---

## 5. Guest Network Isolation

Guest network isolation is one of the main
security features of this project.

Guest devices connect through the wireless access point
and are assigned to VLAN 30.

The router applies an extended ACL named:

BREW-GUEST-ISOLATION

The ACL is applied inbound on:

GigabitEthernet0/1.30

### ACL Configuration

ip access-list extended BREW-GUEST-ISOLATION
 permit udp any eq bootpc any eq bootps
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 deny ip 192.168.30.0 0.0.0.255 192.168.20.0 0.0.0.255
 deny ip 192.168.30.0 0.0.0.255 192.168.99.0 0.0.0.255
 permit ip 192.168.30.0 0.0.0.255 any

### Security Policy

| Source | Destination | Action |
|---|---|---|
| Guest VLAN | Admin VLAN | Deny |
| Guest VLAN | POS VLAN | Deny |
| Guest VLAN | Management VLAN | Deny |
| Guest VLAN | Other destinations | Permit |
| Guest DHCP traffic | DHCP service | Permit |

The DHCP rule allows guest devices to obtain
their network configuration.

The isolation rules prevent guests from reaching
protected internal business networks.

The final permit rule allows other guest traffic,
including traffic to the simulated external network.

### Verification

The following tests were performed:

- Guest to Admin network: Blocked.
- Guest to POS network: Blocked.
- Guest to Management network: Blocked.
- Guest to simulated Internet: Successful.

The results demonstrate the intended guest
network isolation policy.

---

## 6. Switch Port Security

Port Security is configured on connected endpoint
interfaces of CBH-SW-01.

The configuration uses sticky MAC address learning.

This allows the switch to learn connected device
MAC addresses and retain them in the configuration.

### Protected Switch Ports

| Interface | Connected Device | Security |
|---|---|---|
| Fa0/1 | Admin PC | Port Security |
| Fa0/2 | Office Printer | Port Security |
| Fa0/6 | POS Terminal | Port Security |
| Fa0/7 | POS Receipt Printer | Port Security |
| Fa0/11 | Guest Access Point | Port Security |

### Configuration Example

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security mac-address sticky
 switchport port-security violation restrict
 spanning-tree portfast

### Violation Mode

The configured violation mode is:

restrict

When an unauthorized MAC address attempts to use
a protected port, the switch restricts the violating
traffic and records the security violation.

The connected port remains operational.

### Verification

The following commands were used:

show port-security

show port-security address

show port-security interface FastEthernet0/1

The project verification showed five secure ports,
five learned secure MAC addresses, and zero violations
at the time of inspection.

---

## 7. Unused Switch Port Protection

Unused switch ports are administratively disabled.

This helps reduce the risk of unauthorized devices
connecting to the business network.

### Disabled Interfaces

- Fa0/3
- Fa0/4
- Fa0/5
- Fa0/8
- Fa0/9
- Fa0/10
- Fa0/12 through Fa0/23

These interfaces are configured with:

shutdown

Disabling unused ports is a basic but important
physical network security measure.

---

## 8. Secure Remote Management Using SSH

Secure Shell (SSH) is configured on the network devices.

SSH provides encrypted remote management access.

The project uses SSH version 2.

The following devices support SSH management:

- CBH-RTR-01
- CBH-SW-01
- CBH-ISP-01

A local administrator account is configured
for authentication.

The administrator account is:

cbhadmin

The actual password is intentionally excluded
from this public documentation.

### SSH Management Benefits

- Encrypted remote administration.
- Local username authentication.
- Restricted remote access.
- Administrative access without relying on Telnet.

### Verification Commands

show ip ssh

show running-config

show privilege

SSH access was tested successfully from
the administrative workstation.

---

## 9. Management Access Control List

A standard ACL named:

CBH-SSH-MGMT

is configured on the network devices.

The ACL permits the administrative subnet:

192.168.10.0/24

All other source addresses are denied.

### ACL Configuration

ip access-list standard CBH-SSH-MGMT
 permit 192.168.10.0 0.0.0.255
 deny any

The ACL is applied inbound to the VTY lines
using the access-class command.

Example:

line vty 0 4
 access-class CBH-SSH-MGMT in
 login local
 transport input ssh

This restricts remote management access
to the authorized administrative subnet.

### Verification

The following management tests were completed:

- Admin PC to main router SSH: Successful.
- Admin PC to switch SSH: Successful.
- Admin PC to ISP router SSH: Successful.
- Guest access attempt to main router SSH: Blocked.

---

## 10. NAT Security and Management Exemption

The main router uses Network Address Translation
with Port Address Translation (NAT Overload).

The active NAT rule uses the extended ACL:

CBH-NAT

### NAT Configuration

ip nat inside source list CBH-NAT interface GigabitEthernet0/0 overload

### NAT ACL Configuration

ip access-list extended CBH-NAT
 deny ip 192.168.10.0 0.0.0.255 host 203.0.113.1
 permit ip 192.168.10.0 0.0.0.255 any
 permit ip 192.168.20.0 0.0.0.255 any
 permit ip 192.168.30.0 0.0.0.255 any
 permit ip 192.168.99.0 0.0.0.255 any

The first deny statement excludes Admin traffic
destined for the ISP router from NAT translation.

In a NAT selection ACL, a deny statement means
the traffic is not selected for translation.
It does not itself block the packet.

This preserves the original Admin source address
when connecting to the ISP router through SSH.

Other permitted internal traffic is selected
for NAT overload.

---

## 11. Wireless Network Security

The guest wireless network uses WPA2-PSK security.

SSID:

CEYLON_BREW_GUEST

Encryption:

AES

Authentication:

Pre-Shared Key (PSK)

The wireless access point is connected to VLAN 30.

This provides wireless access while maintaining
separation from internal business networks.

The actual wireless password is not included
in this documentation.

---

## 12. Access Control and Security Testing

The security implementation was verified
through practical testing in Cisco Packet Tracer.

| Test | Expected Behaviour | Result |
|---|---|---|
| Guest to Admin | Blocked | Passed |
| Guest to POS | Blocked | Passed |
| Guest to Management | Blocked | Passed |
| Guest to simulated Internet | Allowed | Passed |
| Guest SSH to main router | Blocked | Passed |
| Admin SSH to main router | Allowed | Passed |
| Admin SSH to switch | Allowed | Passed |
| Admin SSH to ISP router | Allowed | Passed |
| Port Security | Five secure ports | Passed |
| Port Security Violations | Zero at verification | Passed |

These tests demonstrate the intended network
segmentation and management access policies.

---

## 13. Security Limitations and Future Improvements

Although the project implements several important
security mechanisms, additional improvements
could strengthen a production deployment.

Potential improvements include:

1. Implement dedicated firewall policies.
2. Add centralized logging using a Syslog server.
3. Implement network monitoring using SNMP.
4. Introduce stronger authentication mechanisms.
5. Implement DHCP Snooping where supported.
6. Implement Dynamic ARP Inspection where supported.
7. Introduce a dedicated management access network
   with a controlled administrative jump host.
8. Implement redundant network devices and links.
9. Review and update device firmware regularly.
10. Apply formal backup and recovery procedures.

These improvements could be considered in future
versions of the network infrastructure.

---

## 14. Conclusion

The CEYLON BREW HOUSE network security design
uses multiple security mechanisms to protect
the business network.

VLAN segmentation separates business departments,
while ACLs control traffic between networks.

Port Security helps restrict unauthorized devices
from accessing protected switch interfaces.

SSH provides secure remote administration,
and management ACLs restrict access to authorized
administrative addresses.

Guest wireless users are isolated from internal
business networks while retaining access to the
simulated external network.

The project demonstrates practical implementation
of fundamental Cisco network security technologies
in a small-business environment.

---

## 15. Project Information

Project: CEYLON BREW HOUSE Network Infrastructure

Simulation Software: Cisco Packet Tracer

Main Devices:
- Cisco 2911 Router
- Cisco Catalyst 2960 Switch
- AccessPoint-PT

Security Technologies:
- VLAN Segmentation
- Access Control Lists
- Port Security
- SSH Version 2
- WPA2-PSK
- NAT/PAT
- Unused Port Shutdown

Project Type: Small Business Network Security

Purpose: Practical Networking Portfolio Project

---

End of Security Documentation
