# Answers to the Lab Questions

## Phase 0
**PC-HQ cannot ping PC-BR yet. Why does the ping fail?**
The routing table on R1 contains only Direct and Local routes, meaning only networks
attached to R1's own interfaces. There is no route to 10.10.3.0/24 (LAN Branch), so R1
does not know where to send the packet and drops it. Routers do not learn remote
networks automatically; routes must come from static configuration or a routing
protocol such as OSPF.

## Phase 1
_TBD_

## Phase 2
_TBD_
