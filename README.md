
# ☕ CEYLON BREW HOUSE
### Cisco Network Infrastructure Design & Implementation

![Cisco Packet Tracer](https://img.shields.io/badge/Simulation-Cisco%20Packet%20Tracer-blue)
![Networking](https://img.shields.io/badge/Domain-Network%20Engineering-green)
![Cisco](https://img.shields.io/badge/Vendor-Cisco-049FD9)
![Project](https://img.shields.io/badge/Project-Small%20Business-orange)
![Status](https://img.shields.io/badge/Status-Completed-success)

A secure, structured, and scalable small-business network infrastructure designed and implemented using **Cisco Packet Tracer**.

The project simulates the network infrastructure of a coffee shop business, supporting administrative operations, Point of Sale (POS) systems, printers, guest wireless connectivity, and centralized network management.

---

## 📌 Project Overview

CEYLON BREW HOUSE is a practical Cisco networking project focused on designing and implementing a professional small-business network.

The infrastructure uses VLAN segmentation, router-on-a-stick inter-VLAN routing, DHCP, NAT/PAT, Access Control Lists (ACLs), port security, and SSH remote management.

A simulated ISP connection provides an external network destination for connectivity testing.

The design focuses on:

- Network segmentation
- Controlled inter-VLAN communication
- Guest network isolation
- Secure device management
- Automated IP address allocation
- Network access security
- Reliable network connectivity

## 🎯 Project Objectives

- Design a structured business network infrastructure.
- Implement VLAN-based network segmentation.
- Configure inter-VLAN routing using Router-on-a-Stick.
- Automate client IP configuration using DHCP.
- Implement NAT overload for external connectivity simulation.
- Isolate guest wireless users from internal business networks.
- Secure switch ports using sticky MAC address learning.
- Restrict remote administration using SSH and ACLs.
- Disable unused switch ports.
- Verify network functionality through practical testing.

---

## 🏗️ Network Architecture

The network follows a centralized Router-on-a-Stick architecture.

**Main components:**

| Device | Model | Role |
|---|---|---|
| CBH-ISP-01 | Cisco 2911 | Simulated ISP router |
| CBH-RTR-01 | Cisco 2911 | Main router and gateway |
| CBH-SW-01 | Cisco Catalyst 2960 | Access switching and VLANs |
| CBH-AP-01 | AccessPoint-PT | Guest wireless connectivity |

**End devices:**

- Administrative workstation
- Office printer
- POS terminal
- POS receipt printer
- Guest wireless laptops

### Network Design

```text
                    SIMULATED ISP
                         |
                  203.0.113.0/30
                         |
                    CBH-ISP-01
                    203.0.113.1
                         |
                    CBH-RTR-01
                    203.0.113.2
                         |
                    802.1Q TRUNK
                         |
                    CBH-SW-01
                    Cisco 2960
                         |
       +-----------------+------------------+
       |                 |                  |
    VLAN 10           VLAN 20            VLAN 30
  BREW-ADMIN         BREW-POS           BREW-GUEST
       |                 |                  |
    Admin PC         POS Terminal         Wi-Fi AP
    Office Printer   POS Printer          Guest Laptops

                    VLAN 99
                   BREW-MGMT
                       |
              Switch Management
                 192.168.99.2
```

---

## 🌐 VLAN & IP Addressing Plan

Four VLANs are configured to separate business operations.

| VLAN | Name | Network | Gateway |
|---|---|---|---|
| 10 | BREW-ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| 20 | BREW-POS | 192.168.20.0/24 | 192.168.20.1 |
| 30 | BREW-GUEST | 192.168.30.0/24 | 192.168.30.1 |
| 99 | BREW-MGMT | 192.168.99.0/24 | 192.168.99.1 |

### Static IP Assignments

| Device | IP Address |
|---|---|
| Office Printer | 192.168.10.10 |
| POS Receipt Printer | 192.168.20.10 |
| Switch Management | 192.168.99.2 |
| Admin PC | DHCP |
| POS Terminal | DHCP |
| Guest Clients | DHCP |

---

## ⚙️ Technologies Implemented

| Technology | Implementation |
|---|---|
| VLAN | Logical network segmentation |
| IEEE 802.1Q | VLAN trunking |
| Router-on-a-Stick | Inter-VLAN routing |
| DHCP | Automatic IP allocation |
| NAT/PAT | Address translation |
| ACL | Network traffic filtering |
| SSH v2 | Secure remote administration |
| Port Security | Sticky MAC address learning |
| WPA2-PSK | Guest wireless security |
| Static Routing | ISP and internal network routing |

---

## 🔐 Network Security

Security is implemented through multiple controls.

### 1. VLAN Segmentation

Administrative, POS, guest, and management traffic are separated into individual VLANs.

### 2. Guest Network Isolation

The `BREW-GUEST-ISOLATION` ACL restricts guest access to:

- Administrative network — VLAN 10
- POS network — VLAN 20
- Management network — VLAN 99

Guest clients can still access the simulated external network.

### 3. Switch Port Security

Port security is configured on five connected endpoint ports.

Features include:

- Sticky MAC address learning
- One authorized MAC address per protected port
- Restrict violation mode

### 4. SSH Management

SSH version 2 is configured on the routers and switch.

The `CBH-SSH-MGMT` ACL permits remote management from the administrative subnet.

### 5. Unused Port Protection

Unused switch interfaces are administratively disabled.

### 6. Wireless Security

The guest wireless network uses WPA2-PSK with AES encryption.

**Note:** Actual credentials and password hashes are intentionally excluded from the public repository.

---

## 🧪 Testing & Verification

The network was tested using Cisco Packet Tracer.

| Test | Expected Result | Status |
|---|---|---|
| Admin PC → POS terminal | Successful | PASS |
| POS terminal → Admin PC | Successful | PASS |
| Guest → Admin network | Blocked | PASS |
| Guest → POS network | Blocked | PASS |
| Guest → Simulated Internet | Successful | PASS |
| Admin → Simulated Internet | Successful | PASS |
| POS → Simulated Internet | Successful | PASS |
| Guest SSH to main router | Blocked | PASS |
| Admin SSH to main router | Successful | PASS |
| Admin SSH to switch | Successful | PASS |
| Admin SSH to ISP router | Successful | PASS |

**Verification summary:** 11 tests passed during the implementation.

The simulated Internet destination is `8.8.8.8`, configured as a loopback address on the ISP router. This is not a real Internet connection.

---

## 📁 Repository Structure

```text
ceylon-brew-cisco-network/
│
├── README.md
│
├── packet-tracer/
│   └── Ceylon_Brew_Network_Infrastructure.pkt
│
├── configurations/
│   ├── CBH-RTR-01.txt
│   ├── CBH-SW-01.txt
│   └── CBH-ISP-01.txt
│
├── documentation/
│   ├── network-architecture.md
│   ├── ip-addressing-plan.md
│   ├── vlan-design.md
│   └── security-design.md
│
├── screenshots/
│   ├── topology/
│   ├── configurations/
│   └── testing/
│
└── LICENSE
```

---

## 🚀 How to Use the Project

### Requirements

- Cisco Packet Tracer
- Windows, Linux, or macOS
- Basic understanding of Cisco networking

### Installation and Simulation

1. Clone or download this repository.
2. Open Cisco Packet Tracer.
3. Navigate to the `packet-tracer` folder.
4. Open `Ceylon_Brew_Network_Infrastructure.pkt`.
5. Explore the network topology.
6. Inspect router and switch configurations.
7. Test connectivity using the end devices.
8. Review the documentation folder.

### Clone Repository

```bash
git clone https://github.com/mssullah/ceylon-brew-cisco-network.git
```

---

## 📚 Documentation

Detailed technical documentation is available in the `documentation/` directory.

| Document | Description |
|---|---|
| Network Architecture | Overall network design and device connections |
| IP Addressing Plan | IPv4 addressing and subnet allocation |
| VLAN Design | VLAN architecture and switch port assignments |
| Security Design | ACLs, SSH, NAT and port security |

---

## 🔮 Future Improvements

Potential improvements for future versions include:

- Implement centralized Syslog monitoring.
- Configure SNMP-based network monitoring.
- Introduce a dedicated firewall.
- Implement DHCP Snooping and Dynamic ARP Inspection.
- Add redundant network links.
- Introduce a centralized authentication service.
- Integrate the infrastructure with AWS networking.
- Develop automated network configuration backups.

---

## 💡 Skills Demonstrated

- Cisco Router Configuration
- Cisco Switch Configuration
- IPv4 Addressing and Subnetting
- VLAN Design and Implementation
- Inter-VLAN Routing
- DHCP Configuration
- NAT and PAT
- Access Control Lists
- SSH Remote Management
- Switch Port Security
- Wireless Network Security
- Network Troubleshooting
- Network Documentation
- Git and GitHub

---

## 👨‍💻 Project Information

**Project Name:** CEYLON BREW HOUSE Network Infrastructure

**Project Type:** Small Business Network Design and Implementation

**Simulation Platform:** Cisco Packet Tracer

**Networking Vendor:** Cisco

**Primary Technologies:** VLAN, Routing, DHCP, NAT, ACL, SSH

**Repository:** `ceylon-brew-cisco-network`

---

## 📜 License

This project is intended for educational and professional portfolio purposes.

See the `LICENSE` file for licensing information.

---

**CEYLON BREW HOUSE — Secure. Connected. Managed.**
