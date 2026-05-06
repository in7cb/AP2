DSL (Digital Subscriber Line) nutzt die vorhandene Kupfer-Telefonleitung für breitbandige Datenübertragung durch Ausnutzung von Frequenzbereichen oberhalb der Telefonie.

## DSL-Varianten

| Variante | Downstream | Upstream | Symmetrie | Reichweite |
|---|---|---|---|---|
| ADSL | bis 8 Mbit/s | bis 1 Mbit/s | asymmetrisch | ~4–5 km |
| ADSL2+ | bis 24 Mbit/s | bis 3,5 Mbit/s | asymmetrisch | ~2–3 km |
| VDSL | bis 52 Mbit/s | bis 16 Mbit/s | asymmetrisch | ~1,3 km |
| VDSL2 | bis 100 Mbit/s | bis 40 Mbit/s | asymmetrisch | ~500 m |
| VDSL2 (Vectoring) | bis 100 Mbit/s | bis 40 Mbit/s | asymmetrisch | ~1,4 km |
| G.fast | bis 1 Gbit/s | bis 500 Mbit/s | (quasi-)symmetrisch | ~100 m |
| SDSL | gleich (~2 Mbit/s) | gleich (~2 Mbit/s) | symmetrisch | ~3 km |

> [!important] **Kernregel**
> Je kürzer die Kupfer-Leitung, desto höher die erreichbare Datenrate. VDSL2/G.fast benötigt daher Fiber-to-the-Curb/Building (FttC/FttB) als Zubringer.

## Vectoring

Gegenseitige elektromagnetische Einstreuung zwischen Kupfer-Paaren (Crosstalk) wird durch koordinierte Signalverarbeitung aller Leitungen am DSLAM aktiv kompensiert. Erhöht VDSL2-Reichweite erheblich – aber nur ein Anbieter pro Kabelverzweiger möglich.

## PPPoE – Point-to-Point Protocol over Ethernet

DSL-Internetzugänge in Deutschland nutzen fast ausschließlich PPPoE für die Einwahl:

```text
Client → PPPoE Discovery → AC (Access Concentrator) beim ISP
         ↓
    PPPoE Session (PPP über Ethernet-Frame)
         ↓
    IP-Adresse per IPCP (IPv4) / IPv6CP zugewiesen
```

- **PPPoE-Client** läuft auf dem Router (Fritz!Box, o.ä.)
- **MTU:** PPPoE-Overhead = 8 Byte → effektive MTU = 1492 statt 1500
- **Authentifizierung:** PAP oder CHAP mit Zugangsdaten vom ISP

> [!warning] **Achtung Falle**
> MTU 1492 statt 1500 bei PPPoE – bei falsch konfiguriertem MSS-Clamping können große Pakete (z.B. beim Abrufen von Webseiten) nicht ankommen (Black-Hole-Problem).

## DOCSIS – Breitband über Kabelnetz (HFC)

**DOCSIS** (Data Over Cable Service Interface Specification) überträgt Daten über TV-Koaxialkabelnetze (Hybrid Fiber-Coax).

| Version | Max. Down | Max. Up |
|---|---|---|
| DOCSIS 3.0 | ~1,2 Gbit/s | ~200 Mbit/s |
| DOCSIS 3.1 | ~10 Gbit/s | ~1–2 Gbit/s |
| DOCSIS 4.0 | ~10 Gbit/s | ~6 Gbit/s (Full Duplex) |

- Kabel ist ein **Shared Medium** – Bandbreite wird unter Teilnehmern im Segment geteilt
- CMTS (Cable Modem Termination System) steuert Upstream-Zugriff via TDMA/OFDMA

## Bonding / Line Bonding

Mehrere DSL-Leitungen werden zu einer logischen gebündelt (**G.Bond / DSL-Bonding**):

```text
2× VDSL2 à 100 Mbit/s → 200 Mbit/s Downstream
```

- Setzt voraus: mehrere physische Adernpaare zwischen Kunde und DSLAM
- In Deutschland als "DSL-Bonding" bei Geschäftskunden verfügbar (z.B. Telekom Speed Home)

> [!tip] **Merksatz**
> **A**DSL = **A**symmetrisch (Download >> Upload) – typisch für Heimnutzer. **S**DSL = **S**ymmetrisch – typisch für Gewerbe.
