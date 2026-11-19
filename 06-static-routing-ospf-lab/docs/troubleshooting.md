# Troubleshooting Log

| # | Problem | Cause | Fix |
|---|---------|-------|-----|
| 1 | GNS3 kept stalling during router configuration | Resource and stability issues in the GNS3 setup | Migrated the lab to PNETLab with Juniper vMX |
| 2 | A router stayed red (would not start) in PNET | Not enough RAM/CPU on the PNET server (too many routers/labs running) | Closed other running labs and started routers one at a time |
| 3 | Inter-router links did not work | Cables were connected to fxp0 / em1 (management / internal use ports) | Rewired to ge-0/0/x data interfaces |
| 4 | Console stayed black for several minutes | vMX boots slowly when several routers start at once | Waited (5-10 min) until the login prompt appeared |
| 5 | `error setting host MAC filter table` at boot | Harmless message in the virtual environment | Ignored; no impact on routing |
| 6 | Spaces disappeared when pasting commands | Browser terminal dropped characters on paste | Typed commands manually / pasted a few lines at a time |
| 7 | `commit` failed: `Cannot assign address 0 on subnet` | Address mistyped as 10.0.1.12/30 (network address) | `rollback 0`, re-entered the correct address, checked with `show \| compare` before commit |
