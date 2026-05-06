Identifikationstechnologien ermöglichen die automatische, berührungslose oder optische Erfassung von Objekt-IDs – fundamental für Logistik, Zutrittskontrolle, Lagerhaltung und IoT-Tracking.

## RFID – Radio Frequency Identification

RFID überträgt Daten berührungslos per Funk zwischen einem **Tag (Transponder)** und einem **Reader (Lesegerät)**. Kein Sichtkontakt nötig.

### Komponenten

- **RFID-Tag:** enthält Mikrochip + Antenne. Speichert eine eindeutige ID (UID) und optional Nutzdaten.
- **RFID-Reader:** sendet Hochfrequenzfeld, Tag antwortet mit Daten.
- **Middleware/System:** verarbeitet die gelesenen IDs (Datenbank, ERP).

### RFID-Frequenzbänder

| Frequenz | Bezeichnung | Reichweite | Besonderheit |
|---|---|---|---|
| 125 kHz | LF (Low Frequency) | < 10 cm | ältere Systeme, Zutrittskontrolle (Chipkarten) |
| 13,56 MHz | HF (High Frequency) | < 1 m | **ISO 15693, ISO 14443** – NFC-Basis |
| 860–960 MHz | UHF (Ultra High Frequency) | 1–10 m | Logistik, Warenerfassung, ISO 18000-6C (EPC Gen2) |
| 2,45 GHz | Mikrowelle | bis 10+ m | spezielle Systeme, selten |

### Tag-Typen

| Typ | Energieversorgung | Kosten | Einsatz |
|---|---|---|---|
| **Passiv** | aus Leserfeld (induktiv) | sehr gering | Logistik, Zutrittskontrolle |
| **Semi-Passiv** | Batterie für Chip, Funkantwort aus Feld | mittel | Temperatur-Logging (mit Sensor) |
| **Aktiv** | eigene Batterie, sendet aktiv | hoch | Fahrzeugortung, große Reichweite |

> [!important] **Kernregel**
> Passive Tags brauchen **keine Batterie** – sie holen Energie aus dem Feld des Readers. Reichweite ist dadurch begrenzt. UHF-Tags erreichen trotzdem bis 10m.

### RFID in der Praxis

```text
Reader sendet Energie → Tag "wacht auf" → Tag antwortet mit UID
Reader → Middleware → Datenbank (z.B. "Palette A7 in Lager 3 angekommen")
```

Typische Einsätze: Warenannahme (automatisch ohne Scannen), Personenerfassung, Bibliothekssysteme, Zutrittskontrolle.

> [!warning] **Achtung Falle**
> RFID kann **nicht durch Metall** lesen – Metallumgebungen stark dämpfen das HF-Feld. UHF-Tags für Metalluntergründe gibt es als spezielle "On-Metal"-Ausführung.

## NFC – Near Field Communication

NFC ist ein **Subset von RFID** (13,56 MHz, ISO 14443/18092), maximal 4 cm Reichweite. In Smartphones integriert → ermöglicht Peer-to-Peer-Kommunikation, Bezahlen (Google Pay, Apple Pay), Tag-Lesen.

**NFC-Modi:**
- **Reader/Writer:** Smartphone liest NFC-Tag
- **Peer-to-Peer:** zwei Geräte tauschen Daten (Android Beam)
- **Card Emulation:** Smartphone verhält sich wie NFC-Karte (Bezahlen)

**NDEF (NFC Data Exchange Format):** standardisiertes Format für NFC-Tag-Inhalte (URLs, Text, vCards, etc.).

## Barcode

Eindimensionaler (1D) optischer Code – Strichbreiten kodieren Daten. Sichtkontakt + Beleuchtung nötig.

### Wichtige Barcode-Typen

| Typ | Anwendung | Besonderheit |
|---|---|---|
| **EAN-13** | Einzelhandel (Produkte) | 13 Ziffern, weltweiter Standard |
| **EAN-8** | kleine Produkte | 8 Ziffern, kompakt |
| **Code 128** | Logistik, GS1 | alphanumerisch, variabel lang |
| **Code 39** | Industrie, KFZ | alphanumerisch, selbstprüfend |
| **ITF-14** | Karton/Palette (Logistik) | 14 Ziffern, auf Wellpappe druckbar |
| **GS1-128** | Logistik (Charge, MHD, Menge) | Code 128 + Application Identifiers |

**EAN-13-Aufbau:**
```text
[3 Ländercode] [4–5 Firmencode] [4–5 Artikel] [1 Prüfziffer]
z.B. 4 00 12345 67890 5
```

## QR-Code

Zweidimensionaler (2D) Matrix-Code – speichert deutlich mehr Daten als Barcode (bis ~3 KB), lesbar aus allen Richtungen, fehlerkorrigierend.

### Aufbau

```text
┌─────────────────┐
│ ██  ██  ██      │ ← Positionsmarker (3 Ecken)
│ ██  ██  ██      │
│   Datenbits     │ ← eigentliche kodierte Daten
│    Timing       │ ← Ausrichtungsmuster
└─────────────────┘
```

**Fehlerkorrektur-Level:**
| Level | Wiederherstellung | Einsatz |
|---|---|---|
| L (Low) | 7% | kleine, unbeschädigte Tags |
| M (Medium) | 15% | Standard |
| Q (Quartile) | 25% | industrielle Umgebung |
| H (High) | 30% | Logo im QR möglich, schmutzige Umgebung |

**Versionen:** QR v1 (21×21 Pixel) bis v40 (177×177 Pixel) – mehr Daten = größere Matrix.

### QR-Einsatz
- URLs (Marketing, Produktverlinkung)
- Wi-Fi-Zugangsdaten einbetten
- Ticket- / Boarding-Pässe
- Gerätekonfiguration (Seriennummer scannen → DB abrufen)

## Vergleich aller Technologien

| | RFID (LF/HF) | RFID (UHF) | NFC | Barcode | QR-Code |
|---|---|---|---|---|---|
| Sichtkontakt | nein | nein | nein | ja | ja |
| Reichweite | < 1m | bis 10m | < 4cm | direkt | < 50cm |
| Dateninhalt | UID (+ Speicher) | UID (+ Speicher) | bis KB | fix kodiert | bis ~3 KB |
| Kosten Tag | ab 0,10 € | ab 0,05 € | ab 0,20 € | Druck | Druck |
| Gleichzeitig lesen | Anti-Collision (mehrere) | viele (100+) | nein (1:1) | nein | nein |
| Schreibbar | ja | ja | ja | nein | nein |
| Standard | ISO 14443/15693 | ISO 18000-6C | ISO 14443/18092 | ISO/IEC | ISO 18004 |

> [!tip] **Merksatz**
> **Barcode = billig, 1D, Sichtkontakt**. **QR = mehr Daten, 2D, robust**. **RFID HF/NFC = berührungslos, kurze Distanz, schreibbar**. **RFID UHF = Logistik, große Distanz, viele Tags gleichzeitig**.
