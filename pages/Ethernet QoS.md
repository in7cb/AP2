[[L2-Technologie|zurück]]

---

# Ethernet QoS (Quality of Service)

QoS sorgt dafür, dass zeitkritischer Datenverkehr (VoIP, Video) bevorzugt behandelt wird und nicht durch Best-Effort-Traffic (Dateiübertragungen) verdrängt wird.

## Warum QoS?

Ohne QoS behandelt ein Switch alle Frames gleich (FIFO). Probleme bei Überlast:
- **Delay (Latenz):** VoIP-Pakete warten hinter großen Downloads
- **Jitter:** Schwankende Latenzen → Sprachqualität bricht ein
- **Packet Loss:** Volle Queues verwerfen Pakete

## Layer-2 QoS: 802.1p (CoS)

802.1p ist Teil des **802.1Q VLAN-Tags** und nutzt 3 Bit für den **PCP** (Priority Code Point), auch „CoS" (Class of Service) genannt.

```
802.1Q Tag (4 Byte):
┌──────┬───┬──────────────────┐
│ TPID │PCP│ DEI │   VID      │
│0x8100│3bit│ 1bit│ 12 bit    │
└──────┴───┴──────────────────┘
         └─ CoS-Wert (0–7)
```

| CoS-Wert | Priorität | Typische Verwendung |
|---|---|---|
| 7 | höchste | Netzwerk-Control (reserviert) |
| 6 | sehr hoch | Netzwerk-Control |
| 5 | hoch | Video (EF – Expedited Forwarding) |
| **4** | hoch | **VoIP / Sprache** |
| 3 | mittel | Call Signaling |
| 2 | mittel | High Priority Data |
| 1 | niedrig | Background |
| **0** | niedrig | **Best Effort (Standard)** |

> [!tip] **Merksatz**
> CoS 4 = Voice, CoS 5 = Video, CoS 0 = Best Effort. Höher = bevorzugter.

## Layer-3 QoS: DSCP (Differentiated Services Code Point)

In IPv4/IPv6-Paketen gibt es das **ToS/DSCP-Feld** (6 Bit) – Layer 3 QoS, der bis zum Router durchgetragen wird.

| DSCP-Wert | Name | Verwendung |
|---|---|---|
| 46 (EF) | Expedited Forwarding | VoIP |
| 34 (AF41) | Assured Forwarding | Video |
| 0 (BE) | Best Effort | Standard-Traffic |

## QoS-Mechanismen am Switch

| Mechanismus | Beschreibung |
|---|---|
| **Classification** | Datenverkehr erkennen und markieren (CoS/DSCP) |
| **Queuing** | Mehrere Warteschlangen pro Port (z. B. 8 Queues) |
| **Scheduling** | Reihenfolge aus Queues bestimmen (z. B. Strict Priority, WRR) |
| **Shaping/Policing** | Bandbreite begrenzen oder überschüssige Pakete verwerfen |

**Scheduling-Verfahren:**
- **Strict Priority (SP):** Höchste Queue wird immer zuerst geleert → Risiko: niedrige Queues verhungern
- **Weighted Round Robin (WRR):** Queues bekommen anteilige Bandbreite → fair, kein Starvation
- **DWRR (Deficit WRR):** WRR mit Byte-Ausgleich für variable Frame-Größen

## Trust-Grenzen

```
Endgerät → Access-Port → Switch (Trust-Grenze) → Uplink → Core
```

Am Access-Port setzt der Switch CoS-Werte i. d. R. **zurück** oder markiert sie gemäß Policy – man vertraut Endgeräten nicht blind.

> [!warning] **Achtung Falle**
> CoS (802.1p) ist nur auf **Tagged Frames** verfügbar. Untagged Frames haben kein PCP-Feld – der Switch muss dann anhand von IP-DSCP oder Port-Nummern klassifizieren.
