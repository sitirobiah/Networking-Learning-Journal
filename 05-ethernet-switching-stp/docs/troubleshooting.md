# Troubleshooting Notes

Back to [README](../README.md) | See the [lab guide](lab-guide.md)

## 1. Switch name in the GUI does not match the CLI

**Symptom:** The workspace label says `SW1`, but the CLI prompt shows `Switch>`.

**Cause:** The GUI label and the CLI hostname are separate settings. Renaming the label does not change the hostname.

**Fix:** Set the hostname on each switch:

```
enable
configure terminal
hostname SW1
end
```

## 2. `% Invalid input detected` on `vlan 10`

**Symptom:**

```
SW2#vlan 10
         ^
% Invalid input detected at '^' marker.
```

**Cause:** The `vlan 10` command only works in global configuration mode (`SW2(config)#`). The `configure terminal` command was skipped, usually because a large paste dropped it.

**Fix:** Enter `configure terminal`, confirm the prompt shows `(config)#`, then paste the rest of the configuration. Pasting in smaller blocks helps avoid this.

| Prompt | Mode | Accepts `vlan 10`? |
|--------|------|--------------------|
| `SW2>` | User EXEC | No |
| `SW2#` | Privileged EXEC | No |
| `SW2(config)#` | Global config | Yes |

## 3. EtherChannel does not show `SU`

**Check:**

- The cables go Fa0/1 to Fa0/1 and Fa0/2 to Fa0/2 (not crossed).
- Both sides use `channel-group 1 mode active`.
- Wait until the ports turn green, then run `show etherchannel summary` again.

**If a port shows `(D)` or `(I)`**, reset the ports on both switches:

```
interface range fastEthernet 0/1-2
 shutdown
 no shutdown
```

## 4. Link lights stay orange

STP needs roughly 30-50 seconds to move ports to forwarding. This is normal and not an error.

## 5. First ping times out

The first ping often fails while ARP resolves. Run the ping again. Only PCs in the same VLAN can reach each other because there is no router in this lab.

## 6. Blocked port is not the one expected

The blocked port depends on the path costs STP calculates. What matters is that exactly one port is blocked on the SW2-SW3 segment so no loop exists. Use your own `show spanning-tree vlan 10` output as the reference.
