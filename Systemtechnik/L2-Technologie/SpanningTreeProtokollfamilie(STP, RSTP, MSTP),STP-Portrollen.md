Spanning Tree verhindert **Layer-2-Schleifen** (Loops) in redundant verkabelten Netzwerken, indem es Ports logisch blockiert und dabei dennoch Redundanz für den Fehlerfall bereithält.

## Das Loop-Problem

Ohne STP in einem redundanten L2-Netz:
- Broadcasts zirkulieren endlos (Broadcast Storm)
- MAC-Tabelle flattert (MAC Flapping)
- Netz wird innerhalb von Sekunden unbenutzbar

## STP – Spanning Tree Protocol (IEEE 802.1D, classic)

### Wahlprozess – 4 Schritte

1. **Root Bridge wählen:** Switch mit niedrigster Bridge-ID (Priority + MAC) wird Root Bridge
2. **Root-Ports bestimmen:** Jeder Nicht-Root-Switch wählt den Port mit niedrigsten Path-Cost zur Root Bridge → Root Port (RP)
3. **Designated Ports bestimmen:** Pro Segment der Port, der den besten Weg zur Root hat → Designated Port (DP)
4. **Restliche Ports blockieren:** Alle übrigen Ports → Alternate/Blocked Port

### Bridge-ID

```
Bridge-ID = Priority (2 Byte) + MAC-Adresse (6 Byte)
Default Priority: 32768 (konfigurierbar in 4096er-Schritten)
```

> [!tip] **Merksatz**
> Niedrigste Bridge-ID gewinnt. Bei gleicher Priority entscheidet die niedrigere MAC-Adresse.

### STP Path Cost (IEEE)

| Bandbreite | STP Cost (alt) | RSTP Cost (neu) |
|---|---|---|
| 10 Mbit/s | 100 | 2.000.000 |
| 100 Mbit/s | 19 | 200.000 |
| 1 Gbit/s | 4 | 20.000 |
| 10 Gbit/s | 2 | 2.000 |

### STP-Port-Zustände

```
Disabled → Blocking → Listening (15s) → Learning (15s) → Forwarding
```

| Zustand | Frames weiterleiten? | MAC lernen? | BPDUs senden? |
|---|---|---|---|
| Blocking | ❌ | ❌ | ✅ (empfangen) |
| Listening | ❌ | ❌ | ✅ |
| Learning | ❌ | ✅ | ✅ |
| Forwarding | ✅ | ✅ | ✅ |

**Konvergenzzeit classic STP:** ~30–50 Sekunden (zu langsam für moderne Netze)

## Port-Rollen

| Rolle | Abk. | Beschreibung |
|---|---|---|
| **Root Port** | RP | Bester Weg zur Root Bridge (pro Non-Root-Switch genau einer) |
| **Designated Port** | DP | Bester Port pro Segment zur Root Bridge (leitet weiter) |
| **Alternate Port** | AP | Alternativer Weg zur Root (blockiert, wird bei Ausfall aktiv) |
| **Backup Port** | BP | Backup für DP auf gleichem Segment (selten) |

> [!important] **Kernregel**
> Root Bridge hat **alle** Ports als Designated Ports. Jeder andere Switch hat genau **einen** Root Port.

## RSTP – Rapid Spanning Tree (IEEE 802.1w)

RSTP ist der Nachfolger von STP – gleiche Grundlogik, aber drastisch schnellere Konvergenz (<1–2 Sekunden).

### Verbesserungen gegenüber STP

| Merkmal | STP | RSTP |
|---|---|---|
| Konvergenzzeit | 30–50 s | <2 s |
| Port-Zustände | 5 | 3 (Discarding, Learning, Forwarding) |
| Proposal/Agreement | ❌ | ✅ (Handshake statt Warten) |
| Topology Change | durch Root | lokal möglich |
| Edge-Ports | ❌ | ✅ PortFast-Äquivalent |

### RSTP Port-Zustände

| RSTP | STP-Äquivalent |
|---|---|
| Discarding | Blocking + Listening |
| Learning | Learning |
| Forwarding | Forwarding |

### RSTP Port-Typen

- **Edge Port:** Direkt mit Endgerät verbunden → geht sofort in Forwarding (≙ PortFast)
- **Point-to-Point:** Verbindung Switch↔Switch (Full-Duplex) → Proposal/Agreement möglich
- **Shared:** Half-Duplex (Hub) → klassisches STP-Verhalten

## MSTP – Multiple Spanning Tree (IEEE 802.1s)

MSTP ermöglicht mehrere Spanning-Tree-Instanzen gleichzeitig – eine pro VLAN-Gruppe (MST-Instanz).

```
MST-Instanz 1: VLANs 1–100  → Root Bridge = Switch A
MST-Instanz 2: VLANs 101–200 → Root Bridge = Switch B
→ Load Balancing über zwei Pfade
```

| Protokoll | Standard | Instanzen | Konvergenz |
|---|---|---|---|
| STP | 802.1D | 1 | 30–50 s |
| PVST+ (Cisco) | proprietär | 1 pro VLAN | 30–50 s |
| RSTP | 802.1w | 1 | <2 s |
| Rapid PVST+ | Cisco | 1 pro VLAN | <2 s |
| MSTP | 802.1s | mehrere | <2 s |

## Wichtige Konfiguration (Cisco)

```bash
# Root Bridge erzwingen
spanning-tree vlan 10 priority 4096

# PortFast (RSTP Edge Port)
interface GigabitEthernet0/1
 spanning-tree portfast

# BPDU Guard – schützt Edge Ports
 spanning-tree bpduguard enable

# STP-Modus wechseln
spanning-tree mode rapid-pvst
spanning-tree mode mst
```

> [!warning] **Achtung Falle**
> **BPDU Guard:** Wenn ein Edge Port (PortFast) eine BPDU empfängt (z. B. ein Switch angesteckt), geht der Port sofort in **err-disabled**. Beabsichtigt – schützt vor zufälligen Loops.
