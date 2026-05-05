[[Routing|zurück]]

---

# Dynamisches Routing – OSPF, RIP, EIGRP, BGP, IS-IS

Dynamische Routing-Protokolle tauschen Routen automatisch zwischen Routern aus und reagieren selbstständig auf Topologieänderungen.

## Klassifizierung

```
Routing-Protokolle
├── IGP (Interior Gateway Protocol) – innerhalb einer AS
│   ├── Distance Vector: RIP, EIGRP
│   └── Link State:      OSPF, IS-IS
└── EGP (Exterior Gateway Protocol) – zwischen AS
    └── Path Vector:     BGP
```

| Protokoll | Typ | AD | Metrik | Standard |
|---|---|---|---|---|
| **RIP v2** | Distance Vector | 120 | Hop Count (max 15) | offen |
| **OSPF** | Link State | 110 | Cost (10⁸/BW) | offen (RFC 2328) |
| **IS-IS** | Link State | 115 | Cost | offen |
| **EIGRP** | Advanced DV | 90 (int) / 170 (ext) | Composite | Cisco (jetzt offen) |
| **BGP** | Path Vector | 20 (eBGP) / 200 (iBGP) | AS-Path, Attribute | offen (RFC 4271) |

## RIP – Routing Information Protocol

- **RIPv1:** classful, kein VLSM, Broadcast-Updates → veraltet
- **RIPv2:** classless, VLSM-Support, Multicast `224.0.0.9`
- **RIPng:** IPv6-Variante

**Eigenschaften:**
- Update-Intervall: 30 Sekunden (vollständige Tabelle)
- Max. Hop Count: 15 (16 = unendlich/nicht erreichbar)
- Konvergenz: langsam (Counting to Infinity Problem)
- Schleifenvermeidung: Split Horizon, Route Poisoning, Holddown Timer

```bash
router rip
 version 2
 network 192.168.1.0
 no auto-summary
```

> [!warning] **Achtung Falle**
> RIP Hop Count ≤ 15. Ein Netz mit 16 Hops = unerreichbar. Für größere Netze OSPF nutzen.

## OSPF – Open Shortest Path First

Link-State-Protokoll – jeder Router kennt die gesamte Topologie und berechnet selbst den kürzesten Pfad (Dijkstra-Algorithmus).

### Grundkonzepte

| Begriff | Bedeutung |
|---|---|
| **LSA** (Link State Advertisement) | Topologieinformation, die Router verteilen |
| **LSDB** (Link State Database) | vollständige Topologiedatenbank aller LSAs |
| **SPF-Algorithmus** | Dijkstra – berechnet kürzeste Pfade aus LSDB |
| **Area** | Hierarchische Aufteilung – alle Router müssen Area 0 (Backbone) kennen |
| **DR/BDR** | Designated Router / Backup DR – reduziert OSPF-Overhead in Multi-Access-Netzen |

### OSPF Router-Typen

| Typ | Beschreibung |
|---|---|
| **Internal Router** | alle Interfaces in einer Area |
| **ABR** (Area Border Router) | verbindet mehrere Areas (inkl. Area 0) |
| **ASBR** (AS Boundary Router) | importiert externe Routen (Redistribution) |
| **Backbone Router** | Interface in Area 0 |

### OSPF Nachbarschaft (Adjacency)

```
Down → Init → 2-Way → ExStart → Exchange → Loading → Full
```

- **Hello-Pakete** (Multicast `224.0.0.5`): Nachbarn entdecken, Keepalive
- **Hello-Interval:** 10 s (Point-to-Point), Dead-Interval: 4× Hello = 40 s
- Auf Broadcast-Netzen (Ethernet): DR/BDR-Wahl, alle bilden Adjacency nur mit DR/BDR

### DR/BDR Wahl

1. Höchste OSPF-Priorität gewinnt (Default: 1, Range 0–255; 0 = nie DR)
2. Bei Gleichstand: höchste Router-ID (höchste IP eines Interfaces, oder manuell)

```bash
interface GigabitEthernet0/0
 ip ospf priority 100     ! erhöhte Priorität → DR-Kandidat
```

### OSPF Metrik (Cost)

```
Cost = 10^8 / Bandbreite (in bit/s)

100 Mbit/s → Cost = 10^8 / 10^8 = 1
1 Gbit/s   → Cost = 10^8 / 10^9 = 0,1 → gerundet auf 1
→ Problem: GigE und 100MbE haben gleichen Cost!
Lösung: auto-cost reference-bandwidth 1000 (oder 10000 für 10G)
```

### OSPF Konfiguration (Cisco)

```bash
router ospf 1
 router-id 1.1.1.1
 auto-cost reference-bandwidth 10000    ! 10 Gbit/s als Referenz
 network 192.168.1.0 0.0.0.255 area 0
 network 10.0.0.0 0.255.255.255 area 1
 passive-interface GigabitEthernet0/0   ! kein OSPF auf Access-Ports

! Alternativ: Interface-basiert
interface GigabitEthernet0/1
 ip ospf 1 area 0
 ip ospf cost 10
```

```bash
show ip ospf neighbor          ! Nachbarn und Status
show ip ospf database          ! LSDB
show ip route ospf             ! nur OSPF-Routen
```

### OSPFv3 (IPv6)

```bash
ipv6 router ospf 1
 router-id 1.1.1.1

interface GigabitEthernet0/0
 ipv6 ospf 1 area 0
```

## EIGRP – Enhanced Interior Gateway Routing Protocol

Hybrid: Eigenschaften von Distance Vector und Link State. Cisco-Protokoll, seit 2013 als RFC 7868 offen.

- **DUAL-Algorithmus** (Diffusing Update Algorithm): garantiert schleifenfreie Routen
- Successor (beste Route) + Feasible Successor (Backup, sofort verfügbar)
- Partial Updates: nur Änderungen werden gesendet (nicht vollständige Tabelle)
- Hello/Dead: 5 s / 15 s (LAN), 60 s / 180 s (WAN)

```bash
router eigrp 100
 network 192.168.1.0
 no auto-summary
```

## BGP – Border Gateway Protocol

Das Routing-Protokoll des Internets. Verbindet Autonomous Systems (AS).

| Typ | Beschreibung |
|---|---|
| **eBGP** | zwischen verschiedenen AS (AD=20) |
| **iBGP** | innerhalb eines AS (AD=200) |

- **AS-Number:** 16-Bit (1–65535) oder 32-Bit; private AS: 64512–65535
- Metrik-Ersatz: **Path Attributes** (AS-Path, Next-Hop, MED, Local Preference, Weight)
- BGP bevorzugt Routen nach: Weight → Local Pref → Locally Originated → AS-Path (kürzer = besser) → MED → eBGP > iBGP → IGP-Metrik

```bash
router bgp 65001
 neighbor 203.0.113.1 remote-as 65002   ! eBGP-Peer
 network 192.168.0.0 mask 255.255.0.0   ! eigenes Netz ankündigen
```

## IS-IS – Intermediate System to Intermediate System

- Link-State wie OSPF, läuft direkt über L2 (kein IP nötig)
- Standard in ISP-Backbone-Netzen (schnellere Konvergenz als OSPF in großen Netzen)
- Level 1 (intra-area), Level 2 (inter-area, ≙ OSPF Area 0)
- Unterstützt IPv4 + IPv6 in einer Instanz (Multi-Topology)

## Vergleich auf einen Blick

| Merkmal | RIP | OSPF | EIGRP | BGP |
|---|---|---|---|---|
| Einsatz | Legacy/klein | Enterprise | Cisco-Netz | Internet/ISP |
| Konvergenz | langsam | schnell | sehr schnell | langsam |
| Skalierung | schlecht (max. 15 Hops) | gut (Areas) | gut | sehr groß |
| Komplexität | gering | mittel | mittel | hoch |
| Protokoll | UDP 520 | IP 89 | IP 88 | TCP 179 |
