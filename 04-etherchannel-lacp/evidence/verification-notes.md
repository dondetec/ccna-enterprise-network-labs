# Lab 04 — Verification Notes

These results were supplied as Cisco IOS CLI output during the guided lab, not extracted from the `.pkt` file.

## LACP established
**SW1:** `1 Po1(SU) LACP Fa0/23(P) Fa0/24(P)`

**SW2:** `1 Po1(SU) LACP Fa0/23(P) Fa0/24(P)`

`S` = Layer 2; `U` = in use; `P` = bundled member.

## Trunk verification
SW1 and SW2: `Po1 on 802.1q trunking 1`. Allowed VLANs: 1–1005; active VLAN: 1. VLAN 1 forwarding on SW1 Po1: none; on SW2 Po1: 1.

## STP verification on SW1
- `Gi0/1 Root FWD 4`
- `Po1 Altn BLK 12`
- Root bridge priority 4097, MAC `0010.11A2.408D`; Rapid PVST+ (`rstp`).

## Failure and recovery
After `shutdown` on SW1 Fa0/23:
`1 Po1(SU) LACP Fa0/23(D) Fa0/24(P)`

After `no shutdown` on SW1 Fa0/23:
`1 Po1(SU) LACP Fa0/23(P) Fa0/24(P)`

## Limitations
- No timed convergence or packet-loss measurements.
- No end-to-end connectivity test.
- No independently verified full running configurations or topology screenshot.
- The uploaded Packet Tracer file has been included unchanged in content and renamed; please open it in Packet Tracer to confirm it contains the latest Lab 04 saved state.
