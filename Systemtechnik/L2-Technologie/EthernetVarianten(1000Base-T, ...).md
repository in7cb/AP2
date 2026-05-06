Ethernet-Standards folgen dem Namensschema: `<Geschwindigkeit><Signaltyp><Medium/Reichweite>`

## Namensschema

```
1000  Base  T
  │     │   └─ Medium: T=Twisted Pair, F/LX/SX=Fiber, CX=Koax
  │     └───── Base = Baseband (kein Multiplexing)
  └─────────── Geschwindigkeit in Mbit/s
```

## Übersicht der wichtigsten Varianten

| Standard | IEEE | Geschwindigkeit | Medium | Max. Länge |
|---|---|---|---|---|
| 10Base-T | 802.3i | 10 Mbit/s | Cat3 TP | 100 m |
| 100Base-TX | 802.3u | 100 Mbit/s | Cat5 TP | 100 m |
| **1000Base-T** | 802.3ab | **1 Gbit/s** | Cat5e TP | **100 m** |
| 1000Base-SX | 802.3z | 1 Gbit/s | MM-LWL (850 nm) | 550 m |
| 1000Base-LX | 802.3z | 1 Gbit/s | SM/MM-LWL (1310 nm) | 5 km (SM) |
| **10GBase-T** | 802.3an | **10 Gbit/s** | Cat6a/7 TP | **100 m** |
| 10GBase-SR | 802.3ae | 10 Gbit/s | MM-LWL (850 nm) | 300 m |
| 10GBase-LR | 802.3ae | 10 Gbit/s | SM-LWL (1310 nm) | 10 km |
| 10GBase-ER | 802.3ae | 10 Gbit/s | SM-LWL (1550 nm) | 40 km |
| 25GBase-T | 802.3bq | 25 Gbit/s | Cat8 TP | 30 m |
| 40GBase-SR4 | 802.3ba | 40 Gbit/s | MM-LWL | 150 m |
| 100GBase-SR4 | 802.3bm | 100 Gbit/s | MM-LWL | 100 m |

## Besonderheiten 1000Base-T

- Nutzt **alle 4 Adernpaare** gleichzeitig (je 250 Mbit/s bidirektional)
- Voll-Duplex über alle 4 Paare durch Echo-Cancellation
- Mindestkabel: **Cat5e** (Cat5 reicht theoretisch, aber Cat5e empfohlen)

## Kupfer vs. Glasfaser im Vergleich

| Merkmal | Twisted Pair (Kupfer) | LWL (Glasfaser) |
|---|---|---|
| Reichweite | bis 100 m | bis 80+ km (SM) |
| Störanfälligkeit | EMV-empfindlich | immunhierauf |
| Kosten | günstig | teurer |
| Typisch für | Endgeräte, Access-Layer | Uplinks, Backbone |
| Stecker | RJ45 | LC, SC, MPO |

> [!tip] **Merksatz**
> **S**X = **S**hort (Multimode, kurz), **L**X = **L**ong (Singlemode, weit), **E**R = **E**xtended **R**eich.

> [!warning] **Achtung Falle**
> 10GBase-T braucht **Cat6a** (oder Cat7) für 100 m. Mit Cat6 nur bis ~55 m.

## SFP / QSFP Transceiver

SFP (Small Form-factor Pluggable) Module machen Switches flexibel – statt festes Medium im Port wird der Transceiver gesteckt:

| Typ | Bandbreite |
|---|---|
| SFP | 1 Gbit/s |
| SFP+ | 10 Gbit/s |
| QSFP+ | 40 Gbit/s |
| QSFP28 | 100 Gbit/s |
| QSFP-DD | 400 Gbit/s |

DAC-Kabel (Direct Attach Copper) = günstiger SFP+-SFP+-Ersatz für kurze Distanzen (<7 m).
