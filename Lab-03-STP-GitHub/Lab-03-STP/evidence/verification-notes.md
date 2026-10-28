# Lab 03 — CLI verification notes

The excerpts below are transcribed from the interactive lab session. They are not screenshots or automated captures from the Packet Tracer project file.

## Normal state (SW1)

```text
Root ID    Priority    4097
           Address     0010.11A2.408D
           Cost        4
           Port        25(GigabitEthernet0/1)
Fa0/24           Altn BLK 19
Gi0/1            Root FWD 4
```

## Primary uplink failure (SW1)

```text
Root ID    Priority    4097
           Address     0010.11A2.408D
           Cost        23
           Port        24(FastEthernet0/24)
Fa0/24           Root FWD 19
```

## Uplink restoration (SW1)

```text
Root ID    Priority    4097
           Address     0010.11A2.408D
           Cost        4
           Port        25(GigabitEthernet0/1)
Fa0/24           Altn BLK 19
Gi0/1            Root FWD 4
```
