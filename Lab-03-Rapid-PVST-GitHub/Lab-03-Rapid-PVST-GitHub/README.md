# Lab 03 — Rapid PVST+ Redundancy and Failover

## Objective
Configure Cisco Rapid PVST+ in a three-switch redundant topology; verify root bridge and port roles; simulate a primary uplink failure and recovery. This lab extends the earlier classic PVST+ STP exercise.

## Topology

```text
                  MLS1 (3560, root bridge)
                   /               \
             Gi0/1/                 \Gi0/2
                 /                   \
          Gi0/1 SW1                 SW2 Gi0/1
                 \                   /
            Fa0/24 \---------------/ Fa0/24
                     redundant link
```

- MLS1 VLAN 1 bridge priority: configured **4096**, displayed **4097** (extended system ID + VLAN 1).
- SW1 and SW2: default priority **32768**, displayed **32769** for VLAN 1.
- STP root bridge MAC: `0010.11A2.408D` (MLS1).
- SW1 bridge MAC: `000D.BD39.EC8A`; SW2 bridge MAC: `0006.2AD4.119A`.
- Gigabit link STP cost: **4**; Fast Ethernet link STP cost: **19** (short path-cost method).

## Configuration

On each switch (MLS1, SW1, SW2):

```cisco
configure terminal
spanning-tree mode rapid-pvst
end
copy running-config startup-config
```

On MLS1, retain the configured VLAN 1 root bridge priority:

```cisco
configure terminal
spanning-tree vlan 1 priority 4096
end
copy running-config startup-config
```

## Verification commands

```cisco
show spanning-tree summary
show spanning-tree vlan 1
show running-config | include spanning-tree
```

### Verified steady-state roles

| Switch | Interface | Role | State | Cost |
|---|---|---|---|---:|
| MLS1 | Gi0/1 | Designated | Forwarding | 4 |
| MLS1 | Gi0/2 | Designated | Forwarding | 4 |
| SW1 | Gi0/1 | Root | Forwarding | 4 |
| SW1 | Fa0/24 | Alternate | Blocking | 19 |
| SW2 | Gi0/1 | Root | Forwarding | 4 |
| SW2 | Fa0/24 | Designated | Forwarding | 19 |

All three switches were verified with `Switch is in rapid-pvst mode` and/or `Spanning tree enabled protocol rstp` in CLI outputs. The alternate port on SW1 prevents a Layer 2 loop. SW2 wins the designated-port election on the inter-switch segment because the two switches have equal root path costs and priorities, but SW2 has the lower MAC address.

## Failover test

**Test action:** Administratively shut down the MLS1 interface connected to SW1:

```cisco
configure terminal
interface gigabitEthernet0/1
shutdown
end
```

**Observed on SW1:**

```text
Spanning tree enabled protocol rstp
Root ID Priority 4097
Address 0010.11A2.408D
Cost 23
Port 24(FastEthernet0/24)

Fa0/24  Root FWD 19
```

**Explanation:** SW1's root path changes from the direct Gigabit connection (cost 4) to the alternate route via SW2 (Fast Ethernet cost 19 + SW2's Gigabit cost 4 = total cost **23**). MLS1 remains the root bridge.

## Recovery test

Restore the original uplink on MLS1:

```cisco
configure terminal
interface gigabitEthernet0/1
no shutdown
end
```

**Observed after restoration:** SW1 returned to `Gi0/1 Root FWD` with root path cost **4**, and `Fa0/24 Altn BLK`. These recovery observations were recorded during the lab; a subsequent check revealed some devices had reverted to classic PVST+ configuration, so Rapid PVST+ was reconfigured and verified on MLS1, SW1, and SW2. Preserve and verify saved startup configurations and the Packet Tracer file before treating a later run as a separate Rapid PVST+ recovery benchmark.

## Results and limitations

- Verified Rapid PVST+ configuration on all three switches.
- Verified stable RSTP port roles and loop prevention.
- Verified alternate-path activation on SW1 after primary-link failure.
- Verified return to the lower-cost primary path in the lab sequence.
- **No precise failover duration or packet-loss measurements were collected.** Do not claim a measured RSTP convergence improvement from this test alone.
- Packet Tracer project included for independent inspection; the file's internal device configurations were not programmatically validated here.

## Lessons learned

1. STP selects the lowest root path cost; Bridge ID breaks ties on the designated-port election.
2. Rapid PVST+ provides a separate RSTP instance per VLAN and can rapidly promote a preexisting alternate port.
3. Operational verification and saved configuration verification are both necessary; running settings can differ from those reopened later.
4. Confirm actual end-to-end recovery with timed traffic testing before reporting convergence metrics.

## Next exercises

- Test RSTP recovery with timed continuous pings and packet-loss observation.
- Introduce VLANs 10, 11, 30, 99, and 110 and verify per-VLAN spanning-tree behavior.
- Add PortFast and BPDU Guard to suitable end-host access ports (not switch-to-switch trunks).
