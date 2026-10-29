# VLAN Segmentation and Inter-VLAN Routing

A Cisco Packet Tracer networking lab demonstrating VLAN segmentation, router-on-a-stick inter-VLAN routing, extended access control lists (ACLs), and switch port security.

## Project Overview

This project simulates a small organization's network divided into three departmental VLANs: Human Resources (HR), Finance, and IT.

The network uses VLANs to separate departmental broadcast domains, a router to enable communication between VLANs, and an extended ACL to enforce a directional communication policy between HR and Finance. Switch port security is also configured on the HR access port.

## Objectives

- Create and configure departmental VLANs.
- Configure access ports and 802.1Q trunk links.
- Implement router-on-a-stick inter-VLAN routing.
- Configure an extended ACL to restrict HR-initiated traffic to Finance.
- Configure sticky MAC port security on an access port.
- Verify routing, connectivity, and access-control behavior.

## Network Topology

**Devices**
- 1 Cisco 2911 router (R1)
- 2 Cisco 2960 switches (SW1 and SW2)
- 4 end devices

**Connections**
- R1  G0/0  ↔ SW1 G0/1
- SW1 G0/2  ↔ SW2 G0/1
- SW1 Fa0/1 ↔ PC-HR1
- SW1 Fa0/2 ↔ PC-FIN1
- SW2 Fa0/1 ↔ PC-IT1
- SW2 Fa0/2 ↔ PC-HR2

## VLAN and IP Addressing Plan

| VLAN | Name    | Network                     | Default Gateway |
|------|---------|-----------------------------|-----------------|
| 10   | HR      | 192.168.10.0/24             | 192.168.10.1    |
| 20   | FINANCE | 192.168.20.0/24             | 192.168.20.1    |
| 30   | IT      | 192.168.30.0/24             | 192.168.30.1    |
| 99   | NATIVE  | No end-host subnet assigned | Not applicable  |

### End-Device Addressing

| Device  | VLAN | IP Address       | Default Gateway |
|---------|---:--|------------------|-----------------|
| PC-HR1  | 10   | 192.168.10.11/24 | 192.168.10.1    |
| PC-HR2  | 10   | 192.168.10.12/24 | 192.168.10.1    |
| PC-FIN1 | 20   | 192.168.20.11/24 | 192.168.20.1    |
| PC-IT1  | 30   | 192.168.30.11/24 | 192.168.30.1    |

## Implementation

### VLANs and Switching

- VLAN 10: HR
- VLAN 20: FINANCE
- VLAN 30: IT
- VLAN 99: NATIVE
- Access ports are assigned to the appropriate departmental VLAN.
- Trunk links carry VLANs 10, 20, 30, and 99.
- VLAN 99 is configured as the native VLAN on the trunk links.

### Inter-VLAN Routing

Router-on-a-stick is implemented on R1 using subinterfaces on GigabitEthernet0/0:

| Subinterface | 802.1Q VLAN | IP Address      |
|--------------|-------------|-----------------|
| G0/0.10      | 10          | 192.168.10.1/24 |
| G0/0.20      | 20          | 192.168.20.1/24 |
| G0/0.30      | 30          | 192.168.30.1/24 |

Each subinterface acts as the default gateway for its respective VLAN.

### Access Control List

The extended ACL `HR-TO-FINANCE` is applied inbound on R1's G0/0.10 subinterface.

The policy:
- Permits ICMP echo replies from HR to Finance to support the tested reverse-direction ping.
- Denies other IP traffic from HR to Finance.
- Permits other traffic not matched by the preceding entries.

**Important limitation:** This ACL is stateless. The ICMP exception supports the tested ping behavior; it does not provide general stateful return-traffic handling for arbitrary TCP or UDP sessions.

### Switch Port Security

Port security is configured on SW1 FastEthernet0/1, connected to PC-HR1.

- Maximum secure MAC addresses: 1
- MAC learning: Sticky
- Violation mode: Shutdown
- Verified port status: Secure-up
- Verified security violation count: 0

## Verification and Results

The following tests were performed in Packet Tracer:

| Source  | Destination               | Expected Result | Observed Result |
|---------|---------------------------|-----------------|-----------------|
| PC-HR1  | HR gateway (192.168.10.1) | Allow           | Successful      |
| PC-HR1  | PC-FIN1 (192.168.20.11)   | Block           | Blocked         |
| PC-FIN1 | PC-HR1 (192.168.10.11)    | Allow           | Successful      |
| PC-IT1  | IT gateway (192.168.30.1) | Allow           | Successful      |
| PC-IT1  | PC-HR1 (192.168.10.11)    | Allow           | Successful      |
| PC-IT1  | PC-FIN1 (192.168.20.11)   | Allow           | Successful      |

Port security verification confirmed one sticky MAC address on SW1 Fa0/1 and zero recorded violations.

## Project Structure

```text
vlan-segmentation-inter-vlan-routing/
├── packet-tracer/
│   └── vlan-lab-v4-port-security.pkt
├── configs/
│   ├── R1-running-config.txt
│   ├── SW1-running-config.txt
│   └── SW2-running-config.txt
├── screenshots/
│   ├── 01-sw1-trunks.png
│   ├── 02-sw2-trunk.png
│   ├── 03-router-interfaces.png
│   ├── 04-routing-table.png
│   ├── 05-acl-rules.png
│   ├── 08-sw1-port-security-address.png
│   ├── 09-final-hr-connectivity-tests.png
│   ├── 10-final-finance-to-hr-allowed.png
│   └── 11-final-it-connectivity.png
└── README.md
```

*Update this list if your actual filenames differ or if you add more screenshots.*

## Tools and Technologies

- Cisco Packet Tracer
- Cisco IOS CLI
- VLANs and 802.1Q trunking
- Router-on-a-stick inter-VLAN routing
- Extended ACLs
- Switch port security and sticky MAC learning
- IPv4 addressing and ICMP connectivity testing

## How to Use

1. Download the `.pkt` file from the `packet-tracer` directory.
2. Open it in Cisco Packet Tracer.
3. Inspect the topology and device configurations.
4. Use the verification commands and screenshots to review the implementation.

## Learning Outcomes

This project provided practical experience with VLAN segmentation, trunk configuration, inter-VLAN routing, ACL rule ordering and direction, switch port security, and systematic connectivity troubleshooting.