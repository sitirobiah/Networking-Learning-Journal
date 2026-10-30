# Network Verification

## 1. Overview

This document contains the verification results of the VLAN and trunk configuration in Cisco Packet Tracer.

The verification process checks:
- VLAN creation and port assignment.
- Trunk port status.
- Network interface status.
- Connectivity between PCs.

## 2. VLAN Verification

**Command:**

### SW1 VLAN Verification

![SW1 VLAN Verification](images/sw1-vlan-verification.png)


### SW1 Results

| VLAN ID | VLAN Name | Status | Assigned Ports |
|---|---|---|---|
| 10 | Keuangan | Active | Fa0/1, Fa0/2 |
| 20 | Engineering | Active | No access ports assigned |

**Result:** VLAN 10 and VLAN 20 were successfully created on SW1. The access ports Fa0/1 and Fa0/2 were assigned to VLAN 10.

### SW2 Results

### SW2 VLAN Verification

![SW2 VLAN Verification](images/sw2-vlan-verification.png)
## 3. Trunk Port Verification

### SW1 Results


show interfaces trunk


Output Summary:
Port	Mode	Encapsulation	Status	Native VLAN
Gig0/1	On	802.1Q	Trunking	1

VLANs Allowed: 1-1005

VLANs Allowed and Active: 1, 10, 20

VLANs in Spanning Tree Forwarding State: 1, 10, 20

Result: GigabitEthernet0/1 is successfully operating as a trunk port. VLANs 1, 10, and 20 are active and forwarding traffic.

SW2 Results
Command:
show interfaces trunk


Output Summary:
Port	Mode	Encapsulation	Status	Native VLAN
Gig0/1	On	802.1Q	Trunking	1

VLANs Allowed: 1-1005

VLANs Allowed and Active: 1, 10, 20

VLANs in Spanning Tree Forwarding State: 1, 10, 20

Result: GigabitEthernet0/1 is successfully operating as a trunk port. VLANs 1, 10, and 20 are active and forwarding traffic.
## 4. Interface Status Verification

**Command:**
```bash
show ip interface brief
```

### SW2 Results

| Interface | Status | Protocol |
|---|---|---|
| Fa0/1 | Up | Up |
| Fa0/2 | Up | Up |
| Gig0/1 | Up | Up |
| Gig0/2 | Down | Down |

**Result:** Gig0/1 is active, while Gig0/2 has no active physical connection.

## 5. Connectivity Testing

The following connectivity tests are planned:

| Source | Destination | Expected Result | Actual Result |
|---|---|---|---|
| PC0 (VLAN 10) | PC1 (VLAN 10) | Successful | Not tested yet |
| PC0 (VLAN 10) | PC3 (VLAN 10) | Successful | Not tested yet |
| PC1 (VLAN 10) | PC3 (VLAN 10) | Successful | Not tested yet |

**Ping command example:**
```bash
ping 192.168.10.11
```

## 6. Verification Summary

Based on the initial verification:

- VLAN 10 and VLAN 20 were created successfully on SW1.
- SW1 access port assignments were verified.
- SW2 Gig0/1 was confirmed to be operating as a trunk.
- SW2 Gig0/2 is currently down.
- PC connectivity testing has not been completed.

## 7. Learning Notes

Through this lab, I learned how to:

- Create VLANs on Cisco switches.
- Assign access ports to specific VLANs.
- Configure trunk ports using IEEE 802.1Q.
- Verify VLAN and interface status using Cisco IOS commands.

**Project Status:** In Progress
