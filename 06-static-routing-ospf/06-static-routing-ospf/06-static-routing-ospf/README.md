# 06 - Static Routing & OSPF: HQ and Branch Failover

Part of my [Networking Learning Journal](https://github.com/sitirobiah/Networking-Learning-Journal).

A hands-on lab in GNS3 that connects an HQ site and a Branch site, first with **static routes** and then with **OSPF area 0**, and shows how the network reroutes when a link fails.

> Based on the Session 5 Lab Guide from my networking course. The configurations, screenshots and answers in this folder come from my own run of the lab.

## Progress

| Phase | Topic | Status |
|---|---|---|
| Setup | Topology, cabling, GNS3 fixes | Done |
| 0 | Base configuration | In progress (R1 verified, R1-R2 ping works) |
| 1 | Static routing | Not started |
| 2 | OSPF area 0 | Not started |
| 3 | Cost and failover | Not started |

## Overview

| | |
|---|---|
| **Goal** | Compare static routing with OSPF, and observe cost-based path selection and failover |
| **Platform** | GNS3 2.2.61 on Windows, QEMU |
| **Routers** | 3x Juniper Junos Olive: R1 (HQ), R2 (WAN), R3 (Branch) |
| **Hosts** | 3x VPCS: PC-HQ, PC-BR, Internet host |
| **Skills** | IP addressing, static and default routes, OSPF, route policy, OSPF cost tuning, failover testing, troubleshooting |

## Topology

![Topology](images/topology.png)

R1 (HQ) and R3 (Branch) are linked directly and also through R2, so HQ and Branch always have two paths: direct, or around through R2. R1 also connects to the Internet host. The "Internet" cloud in the diagram is only an icon; in GNS3 it is a single VPCS node.

### Cabling (GNS3 Ethernet0 = Junos em0, and so on)

| From | To |
|---|---|
| R1 e0 | R2 e0 |
| R1 e1 | R3 e1 |
| R1 e2 | Internet host e0 |
| R1 e3 | PC-HQ e0 |
| R2 e1 | R3 e0 |
| R3 e2 | PC-BR e0 |

Each link was checked in the GNS3 Topology Summary before starting any router.

### IP plan

| Router | Interface | Connects to | IP address |
|---|---|---|---|
| R1 | em0 | R2 em0 | 10.0.12.1/30 |
| R1 | em1 | R3 em1 | 10.0.13.1/30 |
| R1 | em2 | Internet host | 203.0.113.2/30 |
| R1 | em3 | PC-HQ | 10.10.1.1/24 |
| R1 | lo0.0 | n/a | 10.255.255.1/32 |
| R2 | em0 | R1 em0 | 10.0.12.2/30 |
| R2 | em1 | R3 em0 | 10.0.23.1/30 |
| R2 | lo0.0 | n/a | 10.255.255.2/32 |
| R3 | em0 | R2 em1 | 10.0.23.2/30 |
| R3 | em1 | R1 em1 | 10.0.13.2/30 |
| R3 | em2 | PC-BR | 10.10.3.1/24 |
| R3 | lo0.0 | n/a | 10.255.255.3/32 |

| Host | IP address | Gateway | VPCS command |
|---|---|---|---|
| PC-HQ | 10.10.1.10/24 | 10.10.1.1 | `ip 10.10.1.10/24 10.10.1.1` |
| PC-BR | 10.10.3.10/24 | 10.10.3.1 | `ip 10.10.3.10/24 10.10.3.1` |
| Internet host | 203.0.113.1/30 | 203.0.113.2 | `ip 203.0.113.1/30 203.0.113.2` |

Link subnets follow 10.0.XY.0/30, where X and Y are the two router numbers; the lower-numbered router takes .1.

## Folder structure

```
06-static-routing-ospf/
├── README.md
├── images/
│   ├── topology.png
│   ├── phase0/ ... phase3/     # screenshots per phase
└── configs/
    ├── phase0-base/            # R1.txt, R2.txt, R3.txt
    ├── phase1-static/          # R1.txt, R3.txt
    ├── phase2-ospf/            # R1.txt, R2.txt, R3.txt
    └── phase3-cost-failover/   # R1.txt, R2.txt, R3.txt
```

The full router configurations are in `configs/`. This README summarises each phase.

## Junos quick reference

Junos keeps changes as a draft (the candidate configuration) until `commit`.

| Prompt | Mode | What to do |
|---|---|---|
| `root@R1:~ #` or `%` | Unix shell | type `cli` |
| `root@R1>` | Operational | type `configure`; use `show`, `ping`, `trace` here |
| `root@R1#` | Configuration | `set ...`, `delete ...`, `commit`; use `run <command>` for operational commands |

Useful commands: `show | compare` (see the draft), `rollback 0` (discard uncommitted changes), `show route`, `show ospf neighbor`, `show interfaces terse`.

## Phases

### Phase 0: Base configuration
Hostname, root password and interface addresses on every router, plus IP settings on the three VPCS hosts.
- Configs: [`configs/phase0-base`](configs/phase0-base)
- Screenshots: [`images/phase0`](images/phase0)
- Checks: `show interfaces terse` lists the addresses, each router pings its direct neighbours, each PC pings its gateway.

**Why can PC-HQ not ping PC-BR yet?**
> `show route` on R1 lists only Direct and Local routes. R1 has no route to 10.10.3.0/24, and no static route or routing protocol that could teach it one.

### Phase 1: Static routing
Static routes between the two LANs over the direct R1-R3 link, plus a default route on R1 towards the Internet host.
- Configs: [`configs/phase1-static`](configs/phase1-static)
- Screenshots: [`images/phase1`](images/phase1)
- Checks: the ping from PC-HQ fails until R3 also has the return route; `trace` shows 10.10.1.1, 10.0.13.2, 10.10.3.10; both static routes show preference 5.
- Failure test: disabling the direct link stops the ping and it does not recover, even though the path through R2 is healthy.

**How many static routes would 10 branches need, and who updates them when a link fails?**
> _Your answer here after running the phase._

### Phase 2: OSPF area 0
Static site-to-site routes replaced by OSPF. LAN and loopback interfaces are passive, R1 em2 stays out of OSPF, and the default route is injected into OSPF with an export policy.
- Configs: [`configs/phase2-ospf`](configs/phase2-ospf)
- Screenshots: [`images/phase2`](images/phase2)
- Checks: every router shows 2 neighbours in state Full; OSPF routes have preference 10; the default route on R3 is OSPF external with preference 150.

**Compare the effort of OSPF with Phase 1. What changes with a fourth site?**
> _Your answer here after running the phase._

### Phase 3: Cost and failover
Reference bandwidth set to 10g, the direct link made "slow" with metric 100 on both ends, and a failover test with a continuous ping.
- Configs: [`configs/phase3-cost-failover`](configs/phase3-cost-failover)
- Screenshots: [`images/phase3`](images/phase3)
- Checks: the next hop for 10.10.3.0/24 moves to R2 (10.0.12.2); after disabling R2 em1 the ping drops briefly and continues over the direct link.

**Key observation compared with Phase 1:**
> _Your answer here after running the phase._

## Results

| Scenario | Static routing | OSPF |
|---|---|---|
| HQ to Branch reachability | _fill in_ | _fill in_ |
| Behaviour when a link fails | _fill in_ | _fill in_ |
| Effort to add a new site | _fill in_ | _fill in_ |

## Troubleshooting notes

- **HAXM acceleration error on router start.** GNS3 required hardware acceleration that my PC does not have. Fixed by setting `enable_hardware_acceleration` and `require_hardware_acceleration` to false in the GNS3 server and GUI config files. Without acceleration each Olive router needs several minutes to boot.
- **Green node, but no login prompt.** A green node only means the QEMU process is running. Junos was still booting (it reboots once on first boot).
- **Blank console window.** The default console app showed an empty window, so I used a regular PuTTY telnet session to the node's console port instead.
- **"command not found" after root login.** Root login lands in the Unix shell. Type `cli` to enter Junos, then `configure`.
- **Low disk space warning.** Each Olive node creates a linked-clone disk, so free space on the project drive matters.

## What I learned

- **Planning the topology first.** Mapping every cable to a port (GNS3 Ethernet0 = Junos em0) before configuring made the IP plan easy to verify. I checked each link in the GNS3 Topology Summary before starting any router.
- **Junos works in two modes.** Root login lands in the Unix shell. I had to enter the Junos CLI with `cli`, then `configure`, and changes only take effect after `commit`. `run` lets me use operational commands such as `ping` and `show` without leaving configuration mode.
- **Troubleshooting from logs.** When the routers refused to start, the GNS3 GUI log showed that hardware acceleration was required but not available. Changing the config files fixed it.
- **Verifying step by step.** I confirmed interface addresses with `show interfaces terse` and tested each link with ping, starting from direct neighbours and moving outwards. That makes it clear which link or address is wrong when a ping fails.
- _Add points about static routing vs OSPF, cost and failover after completing Phases 1 to 3._

## How to reproduce

1. Install GNS3 and import a Junos Olive QEMU image (not included in this repo).
2. Build the topology and cable it as in the cabling table.
3. Apply the configs in `configs/` phase by phase.
4. Run the checks listed for each phase.
