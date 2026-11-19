# Static Routing & OSPF Lab (Junos on PNETLab)

## Overview
This lab connects an HQ site and a Branch site, first with static routes and then with OSPF, and observes how the network reroutes when a link fails. It is based on the Session 5 Lab Guide (Static Routing & OSPF).

## Topology
![Topology](topology/topology-diagram.png)

- **IP plan:** [topology/ip-addressing-plan.md](topology/ip-addressing-plan.md)
- **Interface names:** [topology/interface-mapping.md](topology/interface-mapping.md)

## Tools
- **PNETLab**
- **Juniper vMX** (3 routers: `R1-HQ`, `R2-WAN`, `R3-Branch`)
- **VPCS** (3 hosts: `PC-HQ`, `PC-BR`, `Internet-Host`)

## Phases
| Phase | Topic | Config | Proof |
| :--- | :--- | :--- | :--- |
| 0 | Base config | [configs/phase0-base](configs/phase0-base) | [verification/phase0-base.md](verification/phase0-base.md) |
| 1 | Static routing | [configs/phase1-static-routing](configs/phase1-static-routing) | [verification/phase1-static-routing.md](verification/phase1-static-routing.md) |
| 2 | OSPF | [configs/phase2-ospf](configs/phase2-ospf) | [verification/phase2-ospf.md](verification/phase2-ospf.md) |
| 3 | Cost and failover | [configs/phase3-cost-and-failover](configs/phase3-cost-and-failover) | [verification/phase3-failover.md](verification/phase3-failover.md) |

## Key Results
- **Phase 0 (Base Setup):** Configured point-to-point `/30` links, LAN `/24` subnets, and `/32` loopbacks across R1–R3. Verified local neighbor and gateway pings succeed, while end-to-end `PC-HQ` to `PC-BR` ping fails because routers only know `Direct` and `Local` routes.
- **Phase 1 (Static Routing):** Established bidirectional HQ–Branch reachability over the direct `R1–R3` link (`[Static/5]`) and Internet access from `PC-HQ` via R1's default route (`0.0.0.0/0`). Confirmed that disabling `R1 ge-0/0/1` drops traffic completely with no automatic failover via R2.
- **Phase 2 (OSPF Dynamic Routing):** Replaced site-to-site static routes with single-area OSPF (`Area 0.0.0.0`). Every router formed 2 `Full` neighbor adjacencies, R1 dynamically learned `10.10.3.0/24` (`[OSPF/10]`), and exporting R1's default route (`[OSPF/150]`) enabled Internet reachability from `PC-BR`.
- **Phase 3 (Cost & Failover):** Adjusted `reference-bandwidth 10g` across all routers and raised the direct `R1–R3` link metric to `100`, steering HQ–Branch traffic through R2 (`10.0.12.2`). Verified automatic OSPF failover back to the direct `R1–R3` link (`10.0.13.2`) when `R2 ge-0/0/1` failed, and seamless recovery via R2 once restored.

## Lessons Learned
See [docs/troubleshooting.md](docs/troubleshooting.md) and [docs/answers.md](docs/answers.md).
