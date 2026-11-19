# Phase 2 - OSPF: Verification

Goal: replace the site-to-site static routes with OSPF area 0. Keep the default route on R1.

- [ ] Every router shows 2 neighbors in state Full (`show ospf neighbor`)
- [ ] `show route protocol ospf` on R1 lists 10.10.3.0/24 with preference 10
- [ ] PC-HQ pings PC-BR
- [ ] `show route 0.0.0.0/0` on R3 shows an OSPF external route with preference 150
- [ ] PC-BR pings 203.0.113.1

Screenshots: ../screenshots/phase2/
