# Lab Topology & Replication Guide (PNETLab)

> **Note:** The raw PNETLab archive (`.unl`/`.zip`) is omitted due to server export restrictions. Use the topology diagram in this repository along with the wiring matrix, VPCS commands, and Junos `set` configurations in `configs/` to recreate the lab from scratch.

---

## 1. Node & Image Specifications

| Node Name | Role | Image / Type | Loopback / Host IP |
| :--- | :--- | :--- | :--- |
| **R1** | HQ Edge Router | Juniper vMX | `10.255.255.1/32` (`lo0.0`) |
| **R2** | WAN Transit Router | Juniper vMX | `10.255.255.2/32` (`lo0.0`) |
| **R3** | Branch Router | Juniper vMX | `10.255.255.3/32` (`lo0.0`) |
| **PC-HQ** | HQ Client Host | VPCS | `10.10.1.10/24` (GW: `10.10.1.1`) |
| **PC-BR** | Branch Client Host | VPCS | `10.10.3.10/24` (GW: `10.10.3.1`) |
| **Internet-Host** | Simulated External Host | VPCS | `203.0.113.1/30` (GW: `203.0.113.2`) |

---

## 2. Port Wiring & IP Addressing Matrix

Connect all router links using data-plane interfaces (`ge-0/0/0` – `ge-0/0/3`). Do **not** use `fxp0` or `em1`.

| Link / Segment | Node A & Port | IP Address A | Node B & Port | IP Address B | Subnet |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R1 – R2 Transit** | `R1` (`ge-0/0/0.0`) | `10.0.12.1/30` | `R2` (`ge-0/0/0.0`) | `10.0.12.2/30` | `10.0.12.0/30` |
| **R1 – R3 Direct** | `R1` (`ge-0/0/1.0`) | `10.0.13.1/30` | `R3` (`ge-0/0/1.0`) | `10.0.13.2/30` | `10.0.13.0/30` |
| **R2 – R3 Transit** | `R2` (`ge-0/0/1.0`) | `10.0.23.1/30` | `R3` (`ge-0/0/0.0`) | `10.0.23.2/30` | `10.0.23.0/30` |
| **HQ LAN** | `R1` (`ge-0/0/3.0`) | `10.10.1.1/24` | `PC-HQ` (`eth0`) | `10.10.1.10/24` | `10.10.1.0/24` |
| **Branch LAN** | `R3` (`ge-0/0/2.0`) | `10.10.3.1/24` | `PC-BR` (`eth0`) | `10.10.3.10/24` | `10.10.3.0/24` |
| **Internet Uplink** | `R1` (`ge-0/0/2.0`) | `203.0.113.2/30` | `Internet-Host` (`eth0`) | `203.0.113.1/30` | `203.0.113.0/30` |

---

## 3. VPCS Host Configuration Commands

Since `configs/` stores router configurations only, apply the following commands to each VPCS node after wiring:

- **PC-HQ:**
  ```text
  ip 10.10.1.10/24 10.10.1.1
  save
