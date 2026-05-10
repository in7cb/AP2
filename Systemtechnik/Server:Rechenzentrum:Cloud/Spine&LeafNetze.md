Spine & Leaf ist eine **zweilagige Rechenzentrum-Topologie** (basierend auf dem CLOS-Netz), die gleichmäßige Latenz und horizontale Skalierbarkeit bietet – der Standard in modernen Data Centern.

## Nord-Süd vs. Ost-West Traffic

| Richtung | Bedeutung | Typisch bei |
|---|---|---|
| **Nord-Süd** | Client → Server (extern ins RZ rein/raus) | klassische Web-Apps, Clients greifen auf Server zu |
| **Ost-West** | Server ↔ Server (intern im RZ) | Microservices, Virtualisierung, Storage-Replikation |

**Warum Ost-West heute dominiert:**
- Virtualisierung: VMs kommunizieren ständig untereinander (vMotion, Clustering)
- Microservices / Container: ein Request löst Dutzende interne Service-zu-Service-Calls aus
- Distributed Storage (Ceph, HDFS): Datenblöcke werden zwischen Nodes repliziert
- Datenbanken: Read-Replicas, Sharding → permanenter Server-zu-Server-Austausch

> [!warning] **Achtung Falle**
> In der Prüfung: **Nord-Süd = Client zu Server**, **Ost-West = Server zu Server** im Rechenzentrum. Nicht durcheinander bringen!

## Klassisch: 3-Tier-Architektur (zum Vergleich)

```text
         [Core]
        /      \
  [Distribution] [Distribution]
   /    \           /    \
[Access][Access] [Access][Access]
```

Probleme mit Ost-West-Traffic:
- **Langer Pfad:** Server A → Access → Distribution → Core → Distribution → Access → Server B = viele Hops
- **Engpass oben:** Core und Distribution werden Flaschenhals wenn viel Ost-West-Traffic fließt
- **STP-Abhängigkeit:** Spanning Tree blockiert redundante Pfade → keine Lastverteilung möglich
- **Hohe Oversubscription:** Uplinks deutlich schmaler als Summe der Downlink-Bandbreiten

## Spine & Leaf – Aufbau

![[L3Spine&LeafNetz.png]]

| Schicht | Rolle |
|---|---|
| **Spine** | Backbone – nur Verbindungen zu Leaf-Switches, keine Server direkt |
| **Leaf** | Access-Schicht – verbindet Server, Storage, Uplinks zu Spine |

**Jedes Leaf ist mit jedem Spine verbunden** → kein Leaf spricht direkt mit einem anderen Leaf.

## Ost-West-Traffic in Spine & Leaf

```text
Server A          Server B
   |                 |
[Leaf 1]          [Leaf 3]
   |    \        /    |
[Spine 1][Spine 2][Spine 3]
```

- Server A → Leaf 1 → **ein Spine** → Leaf 3 → Server B = **immer genau 2 Hops**
- **Kein Core-Flaschenhals**: Traffic wird auf alle Spines verteilt (ECMP)
- **Volle Bisektionsbandbreite**: im Idealfall kann die Hälfte aller Server gleichzeitig mit der anderen Hälfte kommunizieren ohne Engpass

## Eigenschaften

| Merkmal | Details |
|---|---|
| **Hops** | immer genau 2 Hops zwischen beliebigen Servern |
| **Latenz** | konstant & vorhersehbar |
| **Skalierung** | mehr Leaf-Switches einfach hinzufügen (horizontal) |
| **Redundanz** | kein STP nötig – ECMP (Equal-Cost Multi-Path) |
| **ECMP** | Traffic wird über alle Spine-Links verteilt → volle Bandbreite |
| **Oversubscription** | deutlich geringer als bei 3-Tier (typisch 3:1 statt 20:1) |

## Oversubscription

**Oversubscription** = Verhältnis Downlink-Bandbreite zu Uplink-Bandbreite eines Switches.

```text
Beispiel Leaf-Switch:
  - 48 × 10 GbE Downlinks (zu Servern) = 480 Gbit/s
  - 6 × 40 GbE Uplinks zu Spines       = 240 Gbit/s
  → Oversubscription 480:240 = 2:1
```

| Architektur | Typische Oversubscription |
|---|---|
| 3-Tier (klassisch) | 20:1 bis 80:1 am Core |
| Spine & Leaf | 3:1 bis 1:1 (non-blocking) |

**Non-blocking Fabric:** Uplink-Bandbreite = Downlink-Bandbreite → kein Stau möglich, jeder Server bekommt volle Bandbreite.

> [!tip] **Merksatz**
> Je kleiner das Oversubscription-Verhältnis, desto besser. **1:1 = non-blocking** = kein Engpass. Spine & Leaf kommt diesem Ideal viel näher als 3-Tier.

## ECMP – Equal-Cost Multi-Path

Da alle Pfade zwischen Leaf und Spine gleich lang sind, kann der Router / Switch **mehrere Pfade gleichzeitig nutzen**. Traffic-Verteilung per Hash (Src/Dst IP + Port).

```text
Leaf A → Spine 1 → Leaf B  ┐
Leaf A → Spine 2 → Leaf B  ├─ alle gleichwertig → ECMP-Load-Balancing
Leaf A → Spine 3 → Leaf B  ┘
```

> [!tip] **Merksatz**
> Spine = Wirbelsäule, Leaf = Blatt. Jedes Blatt hängt an **jeder** Wirbel. Genau **2 Hops** überall. Skalierung durch mehr Leafs.

## Routing im Fabric

Spine & Leaf verwendet **Layer-3-Routing** im Core (kein L2-Spanning-Tree):
- Jedes Leaf-Switch fungiert als **IP-Gateway** für seine Server (Anycast-Gateway)
- Routing-Protokoll zwischen Spine & Leaf: typischerweise **BGP** (eBGP im Fabric) oder **OSPF**
- Overlay-Protokoll für L2-Segmentierung über L3: **VXLAN** → siehe [[VXLANs]]

## Skalierungsgrenzen

- Anzahl Server pro Leaf: begrenzt durch Port-Anzahl des Leaf-Switch
- Anzahl Leafs: begrenzt durch Port-Anzahl der Spine-Switches
- Für größere RZs: **Super-Spine-Schicht** (3 Ebenen) oder **Pods**

> [!important] **Kernregel**
> Spine & Leaf = **2 Ebenen**, **jedes Leaf an jeden Spine**, **ECMP statt STP**, **konstant 2 Hops**. Optimiert für **Ost-West-Traffic** (Server ↔ Server), der in modernen RZs mit Microservices und Virtualisierung dominiert.
