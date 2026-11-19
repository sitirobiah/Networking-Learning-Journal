# Phase 0 - Base Config: Verification

Goal: every router has a hostname and interface addresses, and direct neighbors can ping each other.

## 1. Interface addresses
Command (on R1, R2, R3): `show interfaces terse | match inet`

![R1 interfaces](../screenshots/phase0/phase0-01-r1-interfaces.png)
![R2 interfaces](../screenshots/phase0/phase0-02-r2-interfaces.png)
![R3 interfaces](../screenshots/phase0/phase0-03-r3-interfaces.png)

## 2. Router-to-router ping
R1: `ping 10.0.12.2 count 3` and `ping 10.0.13.2 count 3`
![R1 ping neighbors](../screenshots/phase0/phase0-04-r1-ping-neighbors.png)

R2: `ping 10.0.23.2 count 3`
![R2 ping R3](../screenshots/phase0/phase0-05-r2-ping-r3.png)

## 3. Host-to-gateway ping

- **PC-HQ:** `ping 10.10.1.1`

  ![PC-HQ gateway ping](../screenshots/phase0/phase0-06-host-gateway-ping-a.png)

- **PC-BR:** `ping 10.10.3.1`

  ![PC-BR gateway ping](../screenshots/phase0/phase0-06-host-gateway-ping-b.png)

- **Internet-Host:** `ping 203.0.113.2`

  ![Internet-Host gateway ping](../screenshots/phase0/phase0-06-host-gateway-ping-c.png)
  
## 4. Expected failure: PC-HQ -> PC-BR
`ping 10.10.3.10` from PC-HQ fails.
![PC-HQ to PC-BR fails](../screenshots/phase0/phase0-07-pchq-pcbr-fail.png)

## 5. Routing table on R1
Command: `show route`
![R1 show route](../screenshots/phase0/phase0-08-r1-show-route.png)

## Result
- [ ] All interfaces up with correct addresses
- [ ] Direct neighbors reply to ping
- [ ] Hosts reach their gateways
- [ ] PC-HQ cannot reach PC-BR (expected)
