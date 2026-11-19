## Answers to the Lab Questions

### Phase 0
**PC-HQ cannot ping PC-BR yet. Why does the ping fail?**  
The routing table on R1 contains only `Direct` and `Local` routes, meaning only networks attached to R1's own interfaces. There is no route to `10.10.3.0/24` (Branch LAN), so R1 does not know where to send the packet and drops it. Routers do not learn remote networks automatically; routes must come from static configuration or a routing protocol such as OSPF.

### Phase 1
**How many static routes would you need if this company had 10 branches, and who updates them when a link fails?**  
With 1 HQ and 10 branches (11 sites total), achieving full-mesh site-to-site connectivity requires 11 × 10 = 110 static routes across all routers (or 220 routes if floating static backup routes via R2 are added). Even in a strict hub-and-spoke design where branches only communicate with HQ, HQ requires 10 static routes and each branch requires at least 1 route (a minimum of 20 static routes). Whenever a link fails or the topology changes, a **network administrator** must manually log into the affected routers and update the static routes, because static routing lacks neighbor discovery and automatic failover.

### Phase 2
**Compare the number of lines you typed for OSPF with Phase 1. What would change if a fourth site were added?**  
On a small 3-router topology, OSPF requires more initial configuration lines (5–6 lines per router plus 4 lines for the `EXPORT-DEFAULT` policy on R1) compared to Phase 1, which only needed 3 static route statements across R1 and R3. However, if a fourth site were added, OSPF only requires configuring OSPF on the new site's router and enabling OSPF on the transit link connecting it to the existing network—**no configuration changes are needed on R1, R2, or R3**, as the new subnet is advertised and learned automatically. In contrast, static routing would require manually adding new static routes on every existing router.

### Phase 3
**How does OSPF path selection and failover compare to Static Routing in Phase 1?**  
In Phase 1, disabling the direct `R1–R3` link (`ge-0/0/1`) caused complete traffic loss until an administrator manually re-enabled the interface, because static routes cannot detect path failures or reroute via R2. In Phase 3, setting `reference-bandwidth 10g` and raising the metric to `100` on the direct `R1–R3` link steered traffic through R2 (`10.0.12.2`). When the `R2–R3` transit link failed, OSPF immediately detected the topology change, recalculated the Shortest Path First (SPF) tree, and automatically failed over to the direct `R1–R3` link—then seamlessly returned traffic to the preferred R2 path once the link was restored.
