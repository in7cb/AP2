Strukturierte Verkabelung ist ein genormtes, hierarchisches Kabelkonzept für Gebäude, das herstellerunabhängig und zukunftssicher ist. Normen: **EN 50173** (Europa), **TIA-568** (USA).

## Hierarchie – 3 Ebenen

```
Standortverteiler (SV)          ← Primärbereich (Campusnetz)
        │
   LWL  │  (Primärverkabelung)
        │
Gebäudeverteiler (GV)           ← Sekundärbereich (Gebäudenetz)
        │
   LWL  │  (Sekundärverkabelung)
        │
Etagenverteiler (EV)            ← Tertiärbereich (Etagen-/Stockwerknetz)
        │
    TP  │  (Tertiärverkabelung / Horizontale Verkabelung)
        │
 Anschlussdose (TAE/RJ45-Dose)
        │
    PC / Endgerät
```

| Ebene | Bezeichnung | Typisches Medium | Länge |
|---|---|---|---|
| **Primär** | Standort- / Campusverkabelung | LWL (SM) | bis mehrere km |
| **Sekundär** | Gebäudeverkabelung | LWL (MM/SM) | bis 500 m |
| **Tertiär** | Etagenverkabelung (horizontal) | TP Cat6a | max. 90 m Trasse |

## Komponenten

### Patchpanel (Rangierpanel)

- Schnittstelle zwischen fester Gebäudeverkabelung und flexiblen Patch-Kabeln
- 19"-Einbau im Rack, 24 oder 48 Ports üblich
- Rückseite: Punch-Down (Kabel eingeschnitten), Vorderseite: RJ45-Buchsen

```
Kabeltrasse ──→ Patchpanel (Punch-Down) ──→ Patch-Kabel ──→ Switch
```

### LWL-Patchpanel / LWL-Kassette

- LC oder SC Buchsen für LWL-Verbindungen
- MPO-Kassetten für High-Density (12/24-Faser-MPO auf LC)

### Kabeltrassen & Installationskanäle

| Typ | Beschreibung | Einsatz |
|---|---|---|
| **Kabelkanal** | Kunststoff/Metall, aufgeschraubt | Bürownde, sichtbar |
| **Kabelwanne** | Offen, im Doppelboden/Decke | große Kabelmengen |
| **Kabelpritschen** | Metall-Gitter | Rechenzentrum, schwere Kabel |
| **Leerrohr** | Schutzrohr im Boden/Wand | Unterputz-Installation |
| **Steigschacht** | vertikale Kabelführung | Stockwerk-zu-Stockwerk |

### Anschlussdose (TAE / RJ45)

- **TAE (Telekommunikations-Anschluss-Einheit):** älteres DE-Format für Telefon/DSL
- **RJ45-Dose (Keystone-Jack):** Standard für moderne Ethernet-Verkabelung

### Rangierverteiler / Rack

- 19"-Rack: Standard-Gehäuse für Switches, Patchpanel, Server
- 1 HE (Höheneinheit) = 44,45 mm
- Üblich: 42 HE, 24 HE Racks
- Belüftung: Kaltgang/Warmgang-Konzept im Rechenzentrum

### Splice-Box / LWL-Muffe

- Spleißverbindungen (Schweißen von LWL-Fasern) werden in Spleißboxen geschützt
- **Fusion Splice:** dauerhaft, sehr geringe Dämpfung (~0,05–0,1 dB)
- **Mechanical Splice:** lösbar, höhere Dämpfung, temporär

## Messkategorien & Klassen

| Klasse (EN 50173) | Kategorie (Komponenten) | Max. Frequenz |
|---|---|---|
| D | Cat5e | 100 MHz |
| E | Cat6 | 250 MHz |
| E_A | Cat6a | 500 MHz |
| F | Cat7 | 600 MHz |
| F_A | Cat7a | 1000 MHz |

> [!warning] **Achtung Falle**
> Die **Klasse** beschreibt die Kanalleistung (inkl. aller Komponenten), die **Kategorie** beschreibt nur das Kabel oder den Stecker. Ein Kanal der Klasse E_A erfordert alle Komponenten in Cat6a.

## Normkonforme Installation

Wichtige Anforderungen nach EN 50173 / DIN VDE 0800:

- Minimaler Biegeradius: 4× Außendurchmesser (TP), 15× (LWL)
- Keine starke Zugbelastung beim Einziehen (Kabelzugkraft beachten)
- Erdung und Schirmung: Schirmung einseitig erden bei Problemen mit Ausgleichsströmen
- Beschriftung: alle Kabel, Patchpanel-Ports und Dosen eindeutig kennzeichnen
- Testprotokoll: nach Installation Abnahmemessung (Wiremap, Länge, Dämpfung, NEXT, Return Loss)

## Schematische Übersicht

```
Standort A                               Standort B
  SV ─────────── LWL (SM Außen) ────────── SV
   │                                         │
  GV ─────────── LWL (MM/SM) ──────────── GV
   │                                         │
  EV ─── TP (Cat6a, ≤90m) ── Dose ── PC
```

> [!tip] **Merksatz**
> **S**tand → **G**ebäude → **E**tage → **D**ose. Von außen nach innen: SM-LWL → MM-LWL → TP. Von groß nach klein.
