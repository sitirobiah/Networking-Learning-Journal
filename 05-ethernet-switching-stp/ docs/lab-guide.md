# Lab Guide: STP with EtherChannel and VLANs

Back to [README](../README.md) | Problems? See [troubleshooting](troubleshooting.md)

## Step 1: Build the topology

Devices: 3x Cisco 2960 switches (SW1, SW2, SW3) and 6 generic PCs.

Use Copper Straight-Through cables on FastEthernet ports only (the Gigabit ports are unused).

| From | To | Purpose |
|------|----|---------|
| SW1 Fa0/1 | SW2 Fa0/1 | EtherChannel link 1 |
| SW1 Fa0/2 | SW2 Fa0/2 | EtherChannel link 2 |
| SW1 Fa0/3 | SW3 Fa0/1 | Trunk |
| SW2 Fa0/3 | SW3 Fa0/2 | Trunk (blocked by STP) |
| PC-HR1 / PC-IT1 | SW1 Fa0/4 / Fa0/5 | Access (VLAN 10 / 20) |
| PC-HR2 / PC-IT2 | SW2 Fa0/4 / Fa0/5 | Access (VLAN 10 / 20) |
| PC-HR3 / PC-IT3 | SW3 Fa0/3 / Fa0/4 | Access (VLAN 10 / 20) |

Do not cross the EtherChannel cables (Fa0/1 to Fa0/1 and Fa0/2 to Fa0/2), otherwise the bundle may fail to form.

Reference: [topology diagram](../topology/topology-diagram.png)

## Step 2: Assign PC IP addresses

Set each PC under Desktop > IP Configuration (no gateway needed).

| PC | IP Address | Subnet Mask |
|----|------------|-------------|
| PC-HR1 | 192.168.10.11 | 255.255.255.0 |
| PC-HR2 | 192.168.10.12 | 255.255.255.0 |
| PC-HR3 | 192.168.10.13 | 255.255.255.0 |
| PC-IT1 | 192.168.20.11 | 255.255.255.0 |
| PC-IT2 | 192.168.20.12 | 255.255.255.0 |
| PC-IT3 | 192.168.20.13 | 255.255.255.0 |

## Step 3: Configure the switches

Open each switch's CLI tab and apply its configuration. Make sure you are in global configuration mode (`SW1(config)#`) before pasting.

- [SW1-config.txt](../configs/SW1-config.txt) (also set as Root Bridge)
- [SW2-config.txt](../configs/SW2-config.txt)
- [SW3-config.txt](../configs/SW3-config.txt)

Key commands:

- `channel-group 1 mode active` creates the LACP EtherChannel on SW1 and SW2.
- `switchport mode trunk` enables trunking on Port-channel 1, SW1 Fa0/3, SW2 Fa0/3, and SW3 Fa0/1-2.
- `switchport mode access` with `switchport access vlan 10` (or `20`) sets up the PC ports.
- `spanning-tree vlan 10,20 root primary` makes SW1 the Root Bridge.

## Step 4: Verify the setup

Wait 30-50 seconds for the link lights to turn green, then run:

```
show etherchannel summary      (SW1 and SW2)
show vlan brief                (all switches)
show interfaces trunk          (all switches)
```

Expected: Po1 shows `SU` with Fa0/1 and Fa0/2 marked `P`, and VLAN 10 and 20 exist with the correct access ports.

Screenshots: [EtherChannel summary](../screenshots/01-etherchannel-summary.png) and [VLAN brief](../screenshots/02-show-vlan-brief.png)

## Step 5: Demo 1, STP prevents loops

Run on SW1, SW2, and SW3:

```
show spanning-tree vlan 10
show spanning-tree vlan 20
```

Expected on SW3:

| Interface | Role | Status | Meaning |
|-----------|------|--------|---------|
| Fa0/1 | Root | FWD | Best path to the Root Bridge (SW1), cost 19 |
| Fa0/2 | Altn | BLK | Backup path via SW2, blocked to prevent a loop |
| Fa0/3 | Desg | FWD | Port to PC-HR3 |

Screenshots: [SW1](../screenshots/03-stp-vlan10-sw1.png), [SW2](../screenshots/04-stp-vlan10-sw2.png), [SW3 before failover](../screenshots/05-stp-vlan10-sw3-before.png)

Then ping between PCs in the same VLAN, for example from PC-HR1:

```
ping 192.168.10.12
ping 192.168.10.13
```

All pings succeed even though one port is blocked. The first ping may time out while ARP resolves.

Screenshot: [ping in the same VLAN](../screenshots/06-ping-same-vlan.png)

## Step 6: Demo 2, reconvergence after link failure

1. Note the blocked port on SW3 (Fa0/2 in this lab).
2. Shut down the Root Port on SW3:

   ```
   enable
   configure terminal
   interface fastEthernet 0/1
    shutdown
   end
   ```

3. Wait about 30-50 seconds, then run `show spanning-tree vlan 10` on SW3. Fa0/2 should now be Root, FWD.

   Screenshot: [SW3 after shutdown](../screenshots/07-stp-vlan10-sw3-after-shutdown.png)

4. From PC-HR3, ping PC-HR1:

   ```
   ping 192.168.10.11
   ```

   The ping still succeeds, now through SW2 and the EtherChannel.

   Screenshot: [ping during failover](../screenshots/08-ping-during-failover.png)

5. Restore the link:

   ```
   enable
   configure terminal
   interface fastEthernet 0/1
    no shutdown
   end
   ```

6. Wait for STP to reconverge and run `show spanning-tree vlan 10` on SW3 again. Fa0/1 should be Root FWD and Fa0/2 Altn BLK.

   Screenshot: [SW3 restored](../screenshots/09-stp-vlan10-sw3-restored.png)

## Step 7: Save your work

Run `write memory` on all three switches and save the Packet Tracer file as [stp-lab.pkt](../topology/stp-lab.pkt).
