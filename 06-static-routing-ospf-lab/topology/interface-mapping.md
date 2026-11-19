# Interface Mapping

The lab guide uses Junos Olive names (em0-em3). This lab runs on Juniper vMX
in PNETLab, where em0 (fxp0) and em1 are reserved for management and internal
use. Data interfaces start at ge-0/0/0.

**Rule: emN in the guide -> ge-0/0/N in PNET**

| Link               | R1        | R2        | R3        |
|--------------------|-----------|-----------|-----------|
| R1 - R2            | ge-0/0/0  | ge-0/0/0  | -         |
| R1 - R3            | ge-0/0/1  | -         | ge-0/0/1  |
| R2 - R3            | -         | ge-0/0/1  | ge-0/0/0  |
| R1 - Internet host | ge-0/0/2  | -         | -         |
| R1 - PC-HQ         | ge-0/0/3  | -         | -         |
| R3 - PC-BR         | -         | -         | ge-0/0/2  |
