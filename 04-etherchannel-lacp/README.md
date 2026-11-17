# Lab 04 — EtherChannel / LACP

## Objective
Configure and verify a two-member Layer 2 LACP EtherChannel between Cisco Catalyst 2960 switches, examine Rapid PVST+ interaction, and test member-link failure and recovery.

## Topology
- MLS1 (Catalyst 3560): root bridge for VLAN 1; Gi0/1 to SW1 and Gi0/2 to SW2.
- SW1 (2960) and SW2 (2960): Fa0/23 ↔ Fa0/23 and Fa0/24 ↔ Fa0/24, bundled as Port-Channel 1.
- Po1 configured as an 802.1Q trunk with native VLAN 1. Only VLAN 1 was verified as active in this exercise.

## Configuration summary
Both switches use `channel-group 1 mode active` on Fa0/23–24 and `switchport mode trunk` on Port-Channel 1. See `configs/` for **configuration excerpts**, not full running-config exports.

## Verified results
- On both switches, `show etherchannel summary` reported `Po1(SU)` with `Fa0/23(P) Fa0/24(P)`.
- Both switches reported Po1 as an operational 802.1Q trunk, native VLAN 1.
- SW1 Rapid PVST+ showed Gi0/1 `Root FWD` (cost 4) and Po1 `Altn BLK` (cost 12).
- SW1 member failure test: shutting down Fa0/23 produced `Po1(SU) ... Fa0/23(D) Fa0/24(P)`.
- After `no shutdown`, SW1 again showed `Po1(SU) ... Fa0/23(P) Fa0/24(P)`.

## Engineering observations
LACP bundles member links into one logical interface. STP evaluates Po1 as a single interface and may block the entire logical port to prevent loops. Although Po1 remained operational during the member failure, **end-to-end user traffic and failover duration were not measured**; SW1's Po1 was STP-blocked for VLAN 1.

## Files
- `packet-tracer/Lab-04-EtherChannel-LACP.pkt`: user-supplied Packet Tracer file (original filename `STP(1).pkt`), renamed for organization; internal contents have not been independently inspected.
- `configs/SW1.txt`, `configs/SW2.txt`: lab command excerpts.
- `evidence/verification-notes.md`: recorded CLI observations and limitations.

## Suggested verification commands
```text
show etherchannel summary
show interfaces trunk
show spanning-tree vlan 1
show interfaces fa0/23
show lacp neighbor
```

## Skills demonstrated
LACP active/active negotiation, EtherChannel flags, 802.1Q trunks, STP Port-Channel roles, member-link resilience, and troubleshooting.
