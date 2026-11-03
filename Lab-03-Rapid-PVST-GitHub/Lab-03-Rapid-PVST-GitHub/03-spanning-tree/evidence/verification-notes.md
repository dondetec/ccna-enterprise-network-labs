# Verification notes — Lab 03 Rapid PVST+

The following values are from the interactive CLI results recorded during the exercise.

| Check | Observed result |
|---|---|
| MLS1 `show spanning-tree summary` | `Switch is in rapid-pvst mode`; VLAN 1 root bridge; two forwarding ports |
| MLS1 running config | `spanning-tree mode rapid-pvst`; `spanning-tree vlan 1 priority 4096` |
| MLS1 save | `wr` returned `Building configuration... [OK]` |
| SW1 steady state | `Gi0/1 Root FWD 4`; `Fa0/24 Altn BLK 19` |
| SW2 steady state | `Gi0/1 Root FWD 4`; `Fa0/24 Desg FWD 19` |
| SW1 failover | `Fa0/24 Root FWD 19`; root path cost `23` |
| Root bridge | `0010.11A2.408D`, VLAN 1 priority `4097` |

The SW1/SW2 startup configuration saves were requested during the exercise, but successful save confirmations were not pasted for both switches. Inspect the attached `.pkt` file in Packet Tracer and save all device configs before further experiments.
