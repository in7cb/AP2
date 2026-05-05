[[L2-Technologie|zurück]]

---

# Physical Layer & Längenbeschränkungen

Der Physical Layer (OSI-Layer 1) definiert die elektrischen, optischen und mechanischen Eigenschaften der Übertragung. Hier gelten konkrete Längenbeschränkungen je nach Medium und Standard.

## Übertragungsmedien im Vergleich

| Medium | Typ | Max. Länge (Standard) | Störanfälligkeit | Kosten |
|---|---|---|---|---|
| Twisted Pair (Cat5e) | Kupfer | 100 m | mittel (EMV) | günstig |
| Twisted Pair (Cat6a) | Kupfer | 100 m | mittel | günstig |
| Koaxialkabel | Kupfer | variabel (veraltet) | mittel | – |
| Multimode-LWL (OM3/OM4) | Glas | bis 300 m / 550 m | keine EMV | mittel |
| Singlemode-LWL | Glas | bis 80 km+ | keine EMV | höher |

## Längenbeschränkungen Ethernet (Twisted Pair)

Die 100-m-Grenze gilt für das **Gesamtsegment** (Patch-Panel + Kabeltrasse + Patch-Kabel):

```
Patchfeld ──────────────── Dose ── Patch
  5 m          90 m max.        5 m
└───────────── gesamt max. 100 m ────────┘
```

| Kategorie | Max. Netto-Trasse | Gesamtlänge | Empfohlener Standard |
|---|---|---|---|
| Cat5e | 90 m | 100 m | bis 1GBase-T |
| Cat6 | 90 m | 100 m | bis 10GBase-T (~55 m) |
| Cat6a | 90 m | 100 m | bis 10GBase-T (voll) |
| Cat7 | 90 m | 100 m | bis 10GBase-T |
| Cat8 | 30 m | 30 m | bis 40GBase-T |

> [!warning] **Achtung Falle**
> Die 100 m sind **nicht** der reine Kabelweg, sondern inkl. aller Patch-Kabel. Netto-Trasse = max. 90 m.

## Längenbeschränkungen LWL

| Standard | Fasertyp | Wellenlänge | Max. Reichweite |
|---|---|---|---|
| 1000Base-SX | Multimode (OM1) | 850 nm | 275 m |
| 1000Base-SX | Multimode (OM3) | 850 nm | 550 m |
| 1000Base-LX | Singlemode | 1310 nm | 5 km |
| 1000Base-ZX | Singlemode | 1550 nm | 70 km |
| 10GBase-SR | Multimode (OM3) | 850 nm | 300 m |
| 10GBase-LR | Singlemode | 1310 nm | 10 km |
| 10GBase-ER | Singlemode | 1550 nm | 40 km |

## Signaldämpfung & Störungen

### Kupfer

| Störeffekt | Beschreibung | Gegenmaßnahme |
|---|---|---|
| **Attenuation** | Signalabschwächung mit Länge | Kürzere Kabel, Repeater |
| **NEXT** (Near-End Crosstalk) | Übersprechen zwischen Adernpaaren | Schirmung (S/FTP) |
| **EMI** (Electromagnetic Interference) | Störung durch externe Felder | Schirmung, Abstand zu Störquellen |
| **Impedance Mismatch** | Reflexion bei falschen Steckern/Kabeln | Korrekte Kategorie verwenden |

### LWL

| Störeffekt | Beschreibung |
|---|---|
| **Attenuation** | Glasabsorption (~0,2 dB/km bei SM, 1310 nm) |
| **Dispersion** | Pulsverbreiterung – bei Multimode: Modal Dispersion |
| **Connector Loss** | Verschmutzung am Stecker → Reinigung zwingend |

> [!important] **Kernregel**
> LWL-Stecker vor Verbindung reinigen – ein verschmutzter SC/LC-Stecker kann mehr dämpfen als 10 km Glasfaser.

## Repeater, Hub, Switch (Layer-1 vs. Layer-2)

| Gerät | Layer | Funktion |
|---|---|---|
| **Repeater** | L1 | Signal verstärken und regenerieren – verlängert Kollisionsdomäne |
| **Hub** | L1 | Multiport-Repeater – flutet alle Frames an alle Ports |
| **Switch** | L2 | Trennt Kollisionsdomänen, leitet gezielt weiter |
| **Bridge** | L2 | Wie Switch, aber software-basiert und weniger Ports |

## Structured Cabling – 10/90er Regel

Trassen-Aufteilung nach EN 50173 / TIA-568:
- **90 m** permanente Verkabelung (Trasse + Patches am Patchfeld und an der Dose)
- **10 m** für flexible Patch-Kabel (je max. 5 m auf jeder Seite)

## Dämpfungsbudget berechnen

```
Budget = Sendeleistung (dBm) - Empfänger-Sensitivität (dBm)
Verluste = Faserdämpfung + Steckerpaare × Verlust/Stecker + Spleißpaare × Verlust/Spleiß
→ Budget muss > Verluste sein (Sicherheitsmarge: 3 dB)
```

Typische Werte LWL:
- Faserverlust Multimode (850 nm): ~3,5 dB/km
- Faserverlust Singlemode (1310 nm): ~0,4 dB/km
- Stecker (LC/SC): ~0,5 dB pro Paar
- Spleiß: ~0,1 dB
