[[Server, Rechenzentrum, Cloud|zurück]]

---

# Spine & Leaf-Netze

Spine & Leaf ist eine **zweilagige Rechenzentrum-Topologie** (basierend auf dem CLOS-Netz), die gleichmäßige Latenz und horizontale Skalierbarkeit bietet – der Standard in modernen Data Centern.

## Klassisch: 3-Tier-Architektur (zum Vergleich)

```text
         [Core]
        /      \
  [Distribution] [Distribution]
   /    \           /    \
[Access][Access] [Access][Access]
```

Problem: **Oversubscription** (hohe Uplinks), **STP-Abhängigkeit**, **North-South-Traffic dominiert** – schlecht für Ost-West-Traffic (Server ↔ Server).

## Spine & Leaf – Aufbau

```text
   [Spine 1]   [Spine 2]   [Spine 3]
     / | \       / | \       / | \
   L1  L2  L3  L1  L2  L3  L1  L2  L3    ← jedes Leaf
                                            verbunden mit
                                            JEDEM Spine
```

| Schicht | Rolle |
|---|---|
| **Spine** | Backbone – nur Verbindungen zu Leaf-Switches, keine Server |
| **Leaf** | Access-Schicht – verbindet Server, Storage, Uplinks zu Spine |

**Jedes Leaf ist mit jedem Spine verbunden** → kein Leaf spricht direkt mit einem anderen Leaf.

## Eigenschaften

| Merkmal | Details |
|---|---|
| **Hops** | immer genau 2 Hops zwischen beliebigen Servern |
| **Latenz** | konstant & vorhersehbar |
| **Skalierung** | mehr Leaf-Switches einfach hinzufügen |
| **Redundanz** | kein STP nötig – ECMP (Equal-Cost Multi-Path) |
| **ECMP** | Traffic wird über alle Spine-Links verteilt → volle Bandbreite |

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
> Spine & Leaf = **2 Ebenen**, **jedes Leaf an jeden Spine**, **ECMP statt STP**, **konstant 2 Hops**. Ermöglicht Ost-West-Traffic ohne Engpass.
