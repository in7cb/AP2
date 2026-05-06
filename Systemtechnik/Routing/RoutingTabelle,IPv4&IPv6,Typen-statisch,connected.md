Die Routing-Tabelle enthält alle Routen, die ein Router kennt. Für jedes eingehende Paket entscheidet der Router anhand der Ziel-IP und des **Longest-Prefix-Match**-Prinzips, über welchen Next-Hop das Paket weitergeleitet wird.

## Longest-Prefix-Match

Der spezifischste (längste) Präfix gewinnt:

```
Routing-Tabelle:
  10.0.0.0/8     via 192.168.1.1
  10.1.0.0/16    via 192.168.1.2
  10.1.1.0/24    via 192.168.1.3

Paket an 10.1.1.50:
  → passt auf alle drei
  → /24 ist spezifischer → via 192.168.1.3 ✅
```

> [!important] **Kernregel**
> Längster Präfix = höchste Priorität. Kein Match → Default-Route (0.0.0.0/0). Kein Match und keine Default-Route → Paket wird verworfen + ICMP Unreachable.

## Cisco IOS – Routing-Tabelle lesen (IPv4)

```
Router# show ip route

Codes: C - connected, S - static, R - RIP, O - OSPF, B - BGP
       D - EIGRP, i - IS-IS, * - candidate default

Gateway of last resort is 10.0.0.1 to network 0.0.0.0

C     192.168.1.0/24 is directly connected, GigabitEthernet0/0
L     192.168.1.1/32 is directly connected, GigabitEthernet0/0
S     10.0.0.0/8 [1/0] via 10.0.0.1
O     172.16.0.0/16 [110/20] via 10.0.0.1, 00:05:23, GigabitEthernet0/1
S*    0.0.0.0/0 [1/0] via 10.0.0.1
```

Format: `<Code> <Netz>/<Prefix> [AD/Metrik] via <Next-Hop>, <Alter>, <Interface>`

## Routen-Typen

### Connected (C / L)

Automatisch eingetragen wenn ein Interface eine IP bekommt und up/up ist:

```bash
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
! → C 192.168.1.0/24 in Routing-Tabelle
! → L 192.168.1.1/32 (Host-Route auf eigene IP, ab IOS 15)
```

Kein Next-Hop nötig – direkt erreichbar über das Interface.

### Static (S)

Manuell konfigurierte Route:

```bash
! Via Next-Hop IP
ip route 10.0.0.0 255.0.0.0 192.168.1.254

! Via Interface (nur Point-to-Point sinnvoll)
ip route 10.0.0.0 255.0.0.0 GigabitEthernet0/1

! Rekursive Route (Next-Hop muss selbst in Tabelle sein)
ip route 172.16.0.0 255.255.0.0 10.0.0.1
```

### Dynamic (O, R, B, D, …)

Vom Routing-Protokoll automatisch gelernt – siehe [[Dynamisches Routing, Protokolle OSPF, RIP, EIGRP, BGP, ISIS]].

## Administrative Distance (AD)

Bei mehreren Quellen für dieselbe Route gewinnt die mit der **niedrigsten AD**:

| Quelle | AD (Cisco) |
|---|---|
| Connected | 0 |
| Static | **1** |
| EIGRP Summary | 5 |
| eBGP | 20 |
| EIGRP (internal) | 90 |
| OSPF | **110** |
| IS-IS | 115 |
| RIP | 120 |
| EIGRP (external) | 170 |
| iBGP | 200 |
| Unbekannt / nicht vertraut | 255 (nie genutzt) |

> [!tip] **Merksatz**
> **S**tatic=1, **O**SPF=110, **R**IP=120 – „SOR" aufsteigend. Connected=0 schlägt immer alles.

## Metrik

Wenn AD gleich ist (gleiche Quelle), entscheidet die **Metrik** (niedrigere Metrik gewinnt):

| Protokoll | Metrik-Basis |
|---|---|
| RIP | Hop Count (max. 15) |
| OSPF | Cost (basiert auf Bandbreite: 10⁸/BW) |
| EIGRP | Composite (BW + Delay + ggf. Load/Reliability) |
| BGP | mehrere Attribute (AS-Path, MED, Local Pref…) |

## Equal-Cost Multi-Path (ECMP)

Mehrere Routen gleicher AD und Metrik → Load Balancing über mehrere Pfade:

```
O  10.0.0.0/8 [110/20] via 192.168.1.1, Gi0/0
O  10.0.0.0/8 [110/20] via 192.168.2.1, Gi0/1
→ Traffic wird aufgeteilt (per-destination oder per-packet)
```

Cisco Default: max. 4 Equal-Cost-Pfade (konfigurierbar bis 16 mit `maximum-paths`).

## IPv6 Routing-Tabelle (Cisco)

```bash
show ipv6 route

IPv6 Routing Table - default - 5 entries
C   2001:db8:1::/64 [0/0]
     via GigabitEthernet0/0, directly connected
L   2001:db8:1::1/128 [0/0]
     via GigabitEthernet0/0, receive
S   2001:db8:2::/64 [1/0]
     via 2001:db8:1::254
O   ::/0 [110/1]
     via FE80::1, GigabitEthernet0/0
```

```bash
! Statische IPv6-Route
ipv6 route 2001:db8:2::/64 2001:db8:1::254
ipv6 route ::/0 GigabitEthernet0/1 FE80::1   ! Default-Route via Link-Local
```

> [!warning] **Achtung Falle**
> Bei IPv6 static routes über Link-Local Next-Hops **muss** das ausgehende Interface mit angegeben werden, da Link-Local-Adressen nicht eindeutig über alle Interfaces sind.

## Routing-Entscheidung – Ablauf

```
Paket eingehend
     ↓
Ziel-IP aus IP-Header lesen
     ↓
Routing-Tabelle: Longest-Prefix-Match suchen
     ↓
Match gefunden?
  Ja → Next-Hop bestimmen → ARP/ND für Next-Hop MAC → weiterleiten
  Nein → Default-Route vorhanden?
    Ja → Default-Route nutzen
    Nein → ICMP Destination Unreachable → Paket verwerfen
```
