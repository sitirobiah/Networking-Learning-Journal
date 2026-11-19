# IP Addressing Plan

## 1. Subnet Allocation Overview

| Network Segment | Subnet / CIDR | Subnet Mask | Usable Host Range | Purpose |
| :--- | :--- | :--- | :--- | :--- |
| **HQ LAN** | `10.10.1.0/24` | `255.255.255.0` | `10.10.1.1` – `10.10.1.254` | HQ internal client subnet |
| **Branch LAN** | `10.10.3.0/24` | `255.255.255.0` | `10.10.3.1` – `10.10.3.254` | Branch internal client subnet |
| **R1 – R2 Point-to-Point** | `10.0.12.0/30` | `255.255.255.252` | `10.0.12.1` – `10.0.12.2` | Transit link between HQ and WAN |
| **R1 – R3 Point-to-Point** | `10.0.13.0/30` | `255.255.255.252` | `10.0.13.1` – `10.0.13.2` | Direct link between HQ and Branch |
| **R2 – R3 Point-to-Point** | `10.0.23.0/30` | `255.255.255.252` | `10.0.23.1` – `10.0.23.2` | Transit link between WAN and Branch |
| **Simulated Internet Link** | `203.0.113.0/30` | `255.255.255.252` | `203.0.113.1` – `203.0.113.2` | TEST-NET-3 external WAN uplink |
| **Router Loopbacks** | `10.255.255.0/24` (`/32` hosts) | `255.255.255.255` | `10.255.255.1` – `10.255.255.3` | Stable OSPF Router IDs |

---

## 2. Device Interface IP Table

| Device | Role | Interface | IP Address / Prefix | Subnet Mask | Default Gateway | Connected To |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **R1** | HQ Router | `ge-0/0/0.0` | `10.0.12.1/30` | `255.255.255.252` | — | `R2` (`ge-0/0/0.0`) |
| **R1** | HQ Router | `ge-0/0/1.0` | `10.0.13.1/30` | `255.255.255.252` | — | `R3` (`ge-0/0/1.0`) |
| **R1** | HQ Router | `ge-0/0/2.0` | `203.0.113.2/30` | `255.255.255.252` | `203.0.113.1` (Static) | `Internet-Host` (`eth0`) |
| **R1** | HQ Router | `ge-0/0/3.0` | `10.10.1.1/24` | `255.255.255.0` | — | `PC-HQ` (`eth0`) |
| **R1** | HQ Router | `lo0.0` | `10.255.255.1/32` | `255.255.255.255` | — | Loopback (OSPF RID) |
| **R2** | WAN Router | `ge-0/0/0.0` | `10.0.12.2/30` | `255.255.255.252` | — | `R1` (`ge-0/0/0.0`) |
| **R2** | WAN Router | `ge-0/0/1.0` | `10.0.23.1/30` | `255.255.255.252` | — | `R3` (`ge-0/0/0.0`) |
| **R2** | WAN Router | `lo0.0` | `10.255.255.2/32` | `255.255.255.255` | — | Loopback (OSPF RID) |
| **R3** | Branch Router | `ge-0/0/0.0` | `10.0.23.2/30` | `255.255.255.252` | — | `R2` (`ge-0/0/1.0`) |
| **R3** | Branch Router | `ge-0/0/1.0` | `10.0.13.2/30` | `255.255.255.252` | — | `R1` (`ge-0/0/1.0`) |
| **R3** | Branch Router | `ge-0/0/2.0` | `10.10.3.1/24` | `255.255.255.0` | — | `PC-BR` (`eth0`) |
| **R3** | Branch Router | `lo0.0` | `10.255.255.3/32` | `255.255.255.255` | — | Loopback (OSPF RID) |
| **PC-HQ** | HQ Client (VPCS) | `eth0` | `10.10.1.10/24` | `255.255.255.0` | `10.10.1.1` | `R1` (`ge-0/0/3.0`) |
| **PC-BR** | Branch Client (VPCS) | `eth0` | `10.10.3.10/24` | `255.255.255.0` | `10.10.3.1` | `R3` (`ge-0/0/2.0`) |
| **Internet-Host** | External Host (VPCS) | `eth0` | `203.0.113.1/30` | `255.255.255.252` | `203.0.113.2` | `R1` (`ge-0/0/2.0`) |

---

## 3. OSPF Area & Router ID Summary

| Router | OSPF Router ID | OSPF Area | Active Transit Interfaces | Passive Interfaces |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | `10.255.255.1` | `0.0.0.0` (Backbone) | `ge-0/0/0.0`, `ge-0/0/1.0` | `ge-0/0/3.0`, `lo0.0` |
| **R2** | `10.255.255.2` | `0.0.0.0` (Backbone) | `ge-0/0/0.0`, `ge-0/0/1.0` | `lo0.0` |
| **R3** | `10.255.255.3` | `0.0.0.0` (Backbone) | `ge-0/0/0.0`, `ge-0/0/1.0` | `ge-0/0/2.0`, `lo0.0` |
