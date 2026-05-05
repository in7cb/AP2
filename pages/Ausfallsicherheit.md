[[L2-Technologie|zurück]]

---

# Ausfallsicherheit auf Layer 2

Ausfallsicherheit im LAN wird durch redundante Verkabelung, Link-Aggregation und schnelle Failover-Mechanismen erreicht.

## Link Aggregation (LAG / EtherChannel / LACP)

Mehrere physische Links werden zu einem logischen Bundle zusammengefasst – erhöht Bandbreite und bietet Redundanz.

| Begriff | Standard / Hersteller |
|---|---|
| **LACP** (Link Aggregation Control Protocol) | IEEE 802.3ad / 802.1AX – offen |
| **EtherChannel** | Cisco-Bezeichnung (kann LACP oder PAgP nutzen) |
| **PAgP** (Port Aggregation Protocol) | Cisco-proprietär |
| **LAG** (Link Aggregation Group) | generischer IEEE-Begriff |

### LACP-Modi

| Modus | Verhalten |
|---|---|
| **Active** | Sendet LACP-Pakete aktiv, verhandelt LAG |
| **Passive** | Wartet auf LACP-Pakete – antwortet, aber initiiert nicht |
| **On** | Erzwingt LAG ohne LACP (kein Protokoll) – beide Seiten müssen `on` sein |

> [!tip] **Merksatz**
> Active + Active = LAG bildet sich. Active + Passive = LAG bildet sich. Passive + Passive = **nichts passiert**.

### EtherChannel Konfiguration (Cisco)

```bash
interface range GigabitEthernet0/1 - 2
 channel-group 1 mode active        ! LACP active
 channel-protocol lacp

interface Port-channel1
 switchport mode trunk
 switchport trunk allowed vlan all
```

### Load Balancing im LAG

Hashing-Algorithmus bestimmt, welcher physische Link verwendet wird:
- Source/Destination MAC
- Source/Destination IP
- Source/Destination Port

→ gleicher Flow bleibt immer auf gleichem Link (kein Reordering)

## Redundante Uplinks & Spanning Tree

Mehrere Uplinks zwischen Switches → STP/RSTP blockiert redundante Links:

```
Distribution Switch
    /         \
   /           \
Access-SW A    Access-SW B
(RP auf Link1) (AP auf Link2 – blockiert)
```

Bei Ausfall von Link1 → RSTP konvergiert, Link2 wird aktiv (<2 s bei RSTP).

→ Details zu STP/RSTP: [[Spanning-Tree Protokollfamilie (STP, RSTP, MSTP), STP-Portrollen]]

## Stacking (Switch-Stack)

Mehrere physische Switches werden zu einem logischen Switch zusammengefasst:

| Merkmal | Stack | einzelner Switch |
|---|---|---|
| Management | eine IP, eine Config | pro Switch separat |
| Ausfallsicherheit | andere Switches übernehmen | kein Failover |
| Ports | alle Ports des Stacks verfügbar | nur eigene Ports |
| Technologien | Cisco StackWise, HPE IRF, ... | – |

```
[SW1] ──Stack-Kabel── [SW2] ──Stack-Kabel── [SW3]
  └───────────────────────────────────────────┘
        Ring-Topologie → doppelte Redundanz
```

> [!important] **Kernregel**
> Ein Stack verhält sich nach außen wie **ein einziger Switch** – eine IP, eine MAC, eine STP-Bridge-ID.

## Dual-Homing / Uplink-Redundanz

Gerät/Server mit zwei NICs an zwei verschiedene Switches angeschlossen:

```
Server
├── NIC1 ──→ Switch A (aktiv)
└── NIC2 ──→ Switch B (standby / LACP-Partner)
```

Varianten:
- **Active-Standby:** Ein Link aktiv, einer wartet
- **Active-Active (LACP):** Beide Links gleichzeitig – mehr Bandbreite + Redundanz

## Verfügbarkeit berechnen

```
Verfügbarkeit = MTBF / (MTBF + MTTR)

MTBF = Mean Time Between Failures (mittlere Zeit zwischen Ausfällen)
MTTR = Mean Time To Repair (mittlere Reparaturzeit)
```

| Verfügbarkeit | Ausfallzeit/Jahr |
|---|---|
| 99 % | ~87,6 h |
| 99,9 % (3 Nines) | ~8,76 h |
| 99,99 % (4 Nines) | ~52,6 min |
| 99,999 % (5 Nines) | ~5,26 min |

Redundante Systeme verbessern die Verfügbarkeit:
```
Parallele Redundanz: 1 - (1-V₁) × (1-V₂)
→ Zwei 99%-Systeme parallel: 1 - (0,01 × 0,01) = 99,99 %
```

> [!warning] **Achtung Falle**
> LACP ohne STP kann Loops erzeugen, wenn die LAG-Konfiguration auf einer Seite fehlt. Immer LACP + STP gemeinsam denken oder PortFast/BPDU Guard auf Access-Ports nutzen.
