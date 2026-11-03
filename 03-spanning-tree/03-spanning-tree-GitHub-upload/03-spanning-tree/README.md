# Lab 03 — Spanning Tree Protocol (STP) and Rapid PVST+

## Objective
Demonstrate Layer 2 loop prevention, root bridge election, STP port roles, and redundant-link failover/recovery using a three-switch Cisco Packet Tracer topology.

## Topology
```text
             MLS1 (3560, root)
             /             \
        Gi0/1               Gi0/2
          /                   \
    SW1 (2960) ---Fa0/24--- SW2 (2960)
        Gi0/1               Gi0/1
```
MLS1 Gi0/1 connects to SW1 Gi0/1; MLS1 Gi0/2 connects to SW2 Gi0/1; SW1 Fa0/24 connects to SW2 Fa0/24.

## Lab files
- `packet-tracer/STP.pkt`: classic PVST/STP exercise.
- `packet-tracer/Lab-03-STP-Rapid-PVST.pkt`: Rapid PVST+ exercise.
- `configs/`: **documented STP-specific command excerpts**, not full running-config exports.
- `evidence/verification-notes.md`: observed CLI results and failover evidence.

## Procedure
1. Establish redundant triangle topology.
2. Set MLS1 VLAN 1 bridge priority to 4096.
3. Verify root bridge and SW1/SW2 port roles with `show spanning-tree vlan 1`.
4. Disable MLS1 Gi0/1 and observe SW1's alternate link transition to root forwarding.
5. Restore Gi0/1 and verify normal port roles.
6. Configure `spanning-tree mode rapid-pvst` on all switches; repeat failover and recovery checks.

## Findings
MLS1 remained the root bridge (VLAN 1 effective priority 4097). In the steady state SW1 Gi0/1 was root forwarding and SW1 Fa0/24 alternate blocking; SW2 Fa0/24 was designated forwarding. SW2 won the shared-segment designated-port tie because its Bridge ID MAC was lower than SW1's. After SW1 lost its direct uplink, its Fa0/24 became root forwarding with total root path cost 23.

## Limitations
Convergence time was not measured. See evidence notes for verification and configuration-save caveats.
