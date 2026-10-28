# Lab 03 — Spanning Tree Protocol (STP): Redundancy and Failover

## Objective
Build a three-switch redundant Layer 2 topology in Cisco Packet Tracer, configure a deterministic STP root bridge, inspect port roles and root path costs, and verify automatic failover and recovery after a link interruption.

## Topology

```text
                 MLS1 (3560-24PS)
                /               \
          Gi0/1/                 \Gi0/2
              /                   \
       Gi0/1 SW1 (2960)       SW2 (2960) Gi0/1
              \                   /
         Fa0/24\_________________/Fa0/24
                   FastEthernet
```

- MLS1 is the VLAN 1 root bridge.
- Both access switches have direct Gigabit Ethernet uplinks to MLS1.
- SW1 and SW2 are connected by a redundant Fast Ethernet link.
- STP prevents the redundant triangle from creating a forwarding loop.

## Key configuration

On **MLS1**:

```cisco
configure terminal
spanning-tree vlan 1 priority 4096
end
show spanning-tree vlan 1
```

The configured priority is **4096**. The displayed Bridge ID priority for VLAN 1 is **4097** because of the extended system ID (4096 + VLAN 1).

## Verification: Normal operation

The following output was captured from **SW1** after the primary uplink was restored:

```text
Spanning tree enabled protocol ieee
Root ID    Priority    4097
           Address     0010.11A2.408D
           Cost        4
           Port        25(GigabitEthernet0/1)
Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
           Address     000D.BD39.EC8A

Interface        Role Sts Cost
Fa0/24           Altn BLK 19
Gi0/1            Root FWD 4
```

The direct Gigabit link is preferred because its root path cost (**4**) is lower than the alternate path via SW2 (**19 + 4 = 23**). SW1 Fa0/24 remains blocked to prevent a Layer 2 loop.

## Failure test

**Action:** Administratively shut down the MLS1 interface connected to SW1:

```cisco
configure terminal
interface gigabitEthernet0/1
shutdown
end
```

**Observed SW1 result:**

```text
Root ID    Priority    4097
           Address     0010.11A2.408D
           Cost        23
           Port        24(FastEthernet0/24)

Interface        Role Sts Cost
Fa0/24           Root FWD 19
```

**Interpretation:** MLS1 remained root. SW1 selected Fa0/24 as its root port, using the backup path **SW1 → SW2 → MLS1**, with total root path cost **23**.

## Recovery test

**Action:** Restore the MLS1 uplink:

```cisco
configure terminal
interface gigabitEthernet0/1
no shutdown
end
```

**Observed result:** SW1 returned to **Gi0/1 Root FWD**, **Fa0/24 Altn BLK**, and root path cost **4**.

## Results

| Test | Expected behavior | Observed behavior | Result |
|---|---|---|---|
| Root bridge election | MLS1 elected root for VLAN 1 | MLS1 root, Bridge ID priority 4097 | PASS |
| Loop prevention | One redundant port blocked | SW1 Fa0/24 Altn BLK | PASS |
| Primary uplink failure | Backup path activated | SW1 Fa0/24 Root FWD; cost 23 | PASS |
| Primary uplink restoration | Preferred path restored | SW1 Gi0/1 Root FWD; cost 4 | PASS |

> **Scope of verification:** STP control-plane behavior was verified with switch CLI outputs. End-to-end PC ping/traffic testing and measured failover timing were not performed in this phase.

## Engineering takeaways

1. The lowest Bridge ID wins root election; bridge priority is compared before MAC address.
2. The root bridge has no root port in its own STP instance.
3. Root path cost determines the preferred path; link speed influences default cost.
4. Redundancy means an alternative path exists; convergence is the process of adapting to a topology change.
5. Blocking a redundant link is intentional loop prevention, not a fault.

## Next phase

Configure **Rapid PVST+** on MLS1, SW1, and SW2; repeat the failover exercise; observe RSTP port roles/states and compare recovery behavior without claiming unmeasured convergence times.

## Project file

Open [`STP.pkt`](./STP.pkt) in Cisco Packet Tracer. The `.pkt` project is included as supplied; the file's internal device configurations have not been independently decoded or validated outside Packet Tracer.
