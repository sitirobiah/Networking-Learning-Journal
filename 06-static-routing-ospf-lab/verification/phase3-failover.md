# Phase 3 - Cost and Failover: Verification

Goal: see how OSPF picks a path, steer traffic with cost, and watch it recover from a link failure.

- [ ] `show ospf interface detail` shows higher cost after setting reference-bandwidth 10g
- [ ] After raising metric on the R1-R3 link, `show route 10.10.3.0/24` on R1 shows next hop 10.0.12.2 (R2)
- [ ] `trace 10.10.3.10` from PC-HQ goes through 10.0.12.2
- [ ] After disabling R2 ge-0/0/1, ping drops briefly and then continues
- [ ] `trace 10.10.3.10` now goes through 10.0.13.2 (direct link)

Screenshots: ../screenshots/phase3/
