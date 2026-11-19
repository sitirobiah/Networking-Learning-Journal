# Phase 1 - Static Routing: Verification

Goal: PC-HQ reaches PC-BR over the direct link (R1 -> R3) using static routes.

- [ ] `ping 10.10.3.10` from PC-HQ succeeds
- [ ] `trace 10.10.3.10` from PC-HQ shows 10.10.1.1 -> 10.0.13.2 -> 10.10.3.10
- [ ] `show route` on R1 shows both static routes with preference 5
- [ ] PC-HQ pings 203.0.113.1; PC-BR cannot yet (R3 has no default route)
- [ ] After disabling R1 ge-0/0/1, ping stops and does not recover

Screenshots: ../screenshots/phase1/
