# Lab 03 — Spanning Tree Protocol (STP) & Rapid PVST+

## Objective

Demonstrate Layer 2 loop prevention, root bridge election, STP port roles, and redundant-link failover and recovery using a three-switch Cisco Packet Tracer topology.

## Network Topology

The lab consists of one Cisco Catalyst 3560 multilayer switch and two Cisco Catalyst 2960 access switches.

```text
                 MLS1 (3560)
                  Root Bridge
                  /        \
              Gi0/1        Gi0/2
                /            \
          Gi0/1                Gi0/1
             SW1 ------------ SW2
           (2960)   Fa0/24   (2960)
```

**Physical connections:**

- MLS1 Gi0/1 ↔ SW1 Gi0/1
- MLS1 Gi0/2 ↔ SW2 Gi0/1
- SW1 Fa0/24 ↔ SW2 Fa0/24

## Technologies Implemented

- IEEE 802.1D Spanning Tree Protocol
- Cisco PVST and Rapid PVST+
- Root bridge election
- Root, designated, and alternate port roles
- Bridge ID and STP path cost
- Layer 2 redundancy and failover
- STP convergence and recovery

## Configuration

MLS1 was configured as the root bridge for VLAN 1 using:

```cisco
spanning-tree vlan 1 priority 4096
```

Rapid PVST+ was enabled on all three switches:

```cisco
spanning-tree mode rapid-pvst
```

## Verification and Testing

The following Cisco IOS commands were used:

```cisco
show spanning-tree vlan 1
show spanning-tree summary
show running-config
```

### Normal Operating State

| Device | Interface | STP Role | State |
|---|---|---|---|
| MLS1 | Gi0/1 | Designated | Forwarding |
| MLS1 | Gi0/2 | Designated | Forwarding |
| SW1 | Gi0/1 | Root | Forwarding |
| SW1 | Fa0/24 | Alternate | Blocking |
| SW2 | Gi0/1 | Root | Forwarding |
| SW2 | Fa0/24 | Designated | Forwarding |

### Failover Test

The MLS1 Gi0/1 interface was administratively shut down to simulate an uplink failure.

**Observed results:**

- SW1 lost its direct connection to the root bridge.
- SW1 Fa0/24 transitioned to Root Forwarding.
- SW1 reached MLS1 through SW2.
- SW1's root path cost increased from 4 to 23.

### Recovery Test

After restoring MLS1 Gi0/1:

- SW1 Gi0/1 returned to Root Forwarding.
- SW1 Fa0/24 returned to Alternate Blocking.
- The original preferred path was restored.

## Key Findings

1. MLS1 remained the root bridge with an effective VLAN 1 priority of 4097.
2. SW2 won the designated-port election on the SW1–SW2 link because it had the lower Bridge ID.
3. STP prevented a Layer 2 loop while preserving a redundant path.
4. Rapid PVST+ successfully handled the tested failover and recovery scenarios.
5. Convergence time was not measured, so no numerical performance comparison was established.

## Repository Files

- `configs/` — STP-specific configuration excerpts
- `packet-tracer/STP.pkt` — Classic STP exercise
- `packet-tracer/Lab-03-STP-Rapid-PVST.pkt` — Rapid PVST+ exercise
- `evidence/verification-notes.md` — CLI verification and failover observations
- `topology/` — Network topology diagram, to be added

## Lessons Learned

This lab strengthened practical understanding of STP root bridge selection, redundant network design, port-state transitions, and troubleshooting Layer 2 switching environments.

The lab also demonstrated how Rapid PVST+ works with redundant physical connections while maintaining a loop-free network.