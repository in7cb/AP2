[[AP2/MainPage|zurück]]

---

# Projektmanagement

Ein Projekt ist ein einmaliges, zeitlich begrenztes Vorhaben zur Lösung einer komplexen Aufgabe mit definiertem Ziel, eigenem Budget und eigenem Team.

**Nach DIN 69901:** „Ein Projekt ist ein Vorhaben, das im Wesentlichen durch die Einmaligkeit der Bedingungen in ihrer Gesamtheit gekennzeichnet ist."

| Merkmal | Bedeutung |
|---|---|
| **Einmaligkeit** | Keine Wiederholung des exakt gleichen Vorhabens |
| **Zeitliche Begrenzung** | Klar definierter Anfang und Ende, ggf. Meilensteine |
| **Finanzielle/personelle Begrenzung** | Festes Budget, begrenztes Team |
| **Zielorientierung** | Projektspezifische Zielvorgaben |
| **Abgrenzung** | Keine standardisierten betrieblichen Routineabläufe |

## Projektarten

| Merkmal | Internes Projekt | Externes Projekt |
|---|---|---|
| **Initiator** | Das Unternehmen selbst | Auftraggeber / Kunde |
| **Mittel** | Kommen aus dem Unternehmen | Werden vom Kunden bereitgestellt / beauftragt |
| **Zweck** | Optimierung interner Abläufe | Kunde hat nicht die Kapazität/Kompetenz selbst |
| **Auslöser** | GF, Fachabteilungen, Führungskräfte | Kundenauftrag |

## Projektphasen (klassisch)

| Phase | Inhalt |
|---|---|
| **1. Analyse** | Problemanalyse, Entscheidung über Projektstart, Machbarkeit |
| **2. Planung** | Ziele, Projektstrukturplan (PSP), Zeitplan (Netzplan), Ressourcen, Risiken |
| **3. Durchführung** | Arbeitspakete umsetzen, Team koordinieren, Fortschritt messen |
| **4. Abschluss** | Abnahme, Präsentation, Dokumentation, Teamauflösung, Lessons Learned |

> [!important] **Kernregel**
> Fehler in der **Planungsphase** sind am günstigsten zu korrigieren – je später ein Fehler erkannt wird, desto teurer die Korrektur. Basis der Planung: PSP → Vorgangsliste → Netzplan → Gantt.

## Projektstrukturplan (PSP / WBS)

Der **Projektstrukturplan** (PSP, engl. Work Breakdown Structure WBS) gliedert ein Projekt hierarchisch in **Teilprojekte** und **Arbeitspakete**.

```
Projekt (Hauptaufgabe)
├── Teilaufgabe 1
│   ├── Arbeitspaket 1.1
│   ├── Arbeitspaket 1.2
│   └── Arbeitspaket 1.3
├── Teilaufgabe 2
│   ├── Arbeitspaket 2.1
│   └── Arbeitspaket 2.2
└── Teilaufgabe 3
    └── Arbeitspaket 3.1
```

**Gliederungsarten:**

| Art | Beschreibung | Beispiel |
|---|---|---|
| **Objektorientiert** | Nach Ergebnissen/Produkten | Hardware, Software, Dokumentation |
| **Funktionsorientiert** | Nach Tätigkeiten/Phasen | Planung, Entwicklung, Test |
| **Gemischt** | Kombiniert, in der Praxis häufig | Höhere Ebenen objekt-, tiefere funktionsorientiert |

**Arbeitspaket:** Kleinste Einheit im PSP – klar abgegrenzte Teilaufgabe mit definiertem Ergebnis und abschätzbarem Aufwand. Fördert Eigenverantwortung.

> [!tip] **Merksatz**
> PSP beantwortet **WAS** zu tun ist – Netzplan beantwortet **WANN** und **WIE LANGE**.

## Netzplan (CPM – Critical Path Method)

Der Netzplan stellt Vorgänge und ihre zeitlichen Abhängigkeiten grafisch dar. Basis ist die **Vorgangsknotentechnik**: jeder Vorgang ist ein Knoten mit diesen Feldern:

```
┌───────┬───────────────┬───────┐
│  FAZ  │               │  FEZ  │
├───────┤  Nr. Vorgang  ├───────┤
│  D    │               │  GP   │  FP
├───────┤               ├───────┤
│  SAZ  │               │  SEZ  │
└───────┴───────────────┴───────┘
```

| Kürzel | Begriff | Formel / Bedeutung |
|---|---|---|
| **FAZ** | Frühester Anfangszeitpunkt | max(FEZ aller Vorgänger) |
| **FEZ** | Frühester Endzeitpunkt | FAZ + D |
| **SAZ** | Spätester Anfangszeitpunkt | SEZ − D |
| **SEZ** | Spätester Endzeitpunkt | min(SAZ aller Nachfolger) |
| **D** | Dauer | Länge des Vorgangs |
| **GP** | Gesamtpuffer | SAZ − FAZ = SEZ − FEZ |
| **FP** | Freier Puffer | min(FAZ Nachfolger) − FEZ |

### Berechnungsregeln

**Schritt 1 – Vorwärtsrechnung** (links → rechts, früheste Zeitpunkte):
```text
FAZ(erster Vorgang) = 0
FEZ = FAZ + D
FAZ(Nachfolger) = max(FEZ aller direkten Vorgänger)   ← bei mehreren Vorgängern: MAXIMUM
```

**Schritt 2 – Rückwärtsrechnung** (rechts → links, späteste Zeitpunkte):
```text
SEZ(letzter Vorgang) = FEZ(letzter Vorgang)
SAZ = SEZ − D
SEZ(Vorgänger) = min(SAZ aller direkten Nachfolger)   ← bei mehreren Nachfolgern: MINIMUM
```

**Schritt 3 – Puffer:**
```text
GP = SAZ − FAZ   (= SEZ − FEZ)
FP = min(FAZ aller direkten Nachfolger) − FEZ
```

> [!warning] **Achtung Falle: GP ≠ FP**
> GP zeigt, wie lange ein Vorgang verzögert werden darf, **ohne das Projektende zu verschieben** – aber er kann den Puffer nachfolgender Vorgänge aufbrauchen!
> FP zeigt, wie lange ein Vorgang verzögert werden darf, **ohne den FAZ des direkten Nachfolgers zu beeinflussen**.
> Beispiel: Vorgang B hat GP=5, FP=0 → B kann nicht verzögert werden, ohne D's Starttermin zu verschieben (auch wenn das Projektende gleich bleibt, solange D seinen eigenen GP nutzt).

### Beispiel-Netzplan (vollständig mit GP und FP)

```text
Vorgangsliste:
  A (D=2), kein Vorgänger
  B (D=1), Vorgänger: A
  C (D=6), Vorgänger: A
  D (D=5), Vorgänger: B
  E (D=7), Vorgänger: B, C
  G (D=2), Vorgänger: D, E

Vorwärtsrechnung:
  A: FAZ=0,  FEZ=2
  B: FAZ=2,  FEZ=3
  C: FAZ=2,  FEZ=8
  D: FAZ=3,  FEZ=8
  E: FAZ=max(3,8)=8, FEZ=15
  G: FAZ=max(8,15)=15, FEZ=17  → Projektdauer = 17 Tage

Rückwärtsrechnung:
  G: SEZ=17, SAZ=15
  E: SEZ=15, SAZ=8
  D: SEZ=15, SAZ=10
  C: SEZ=8,  SAZ=2
  B: SEZ=min(SAZ_D=10, SAZ_E=8)=8, SAZ=7
  A: SEZ=min(SAZ_B=7,  SAZ_C=2)=2, SAZ=0

Puffer:
  Vorgang | GP         | FP                                  | Kritisch?
  A       | 0-0=0      | min(FAZ_B=2, FAZ_C=2)-2 = 0        | ✅
  B       | 7-2=5      | min(FAZ_D=3, FAZ_E=8)-3 = 0        | ❌ (GP>0, aber FP=0!)
  C       | 2-2=0      | FAZ_E-FEZ_C = 8-8 = 0              | ✅
  D       | 10-3=7     | FAZ_G-FEZ_D = 15-8 = 7             | ❌
  E       | 8-8=0      | FAZ_G-FEZ_E = 15-15 = 0            | ✅
  G       | 15-15=0    | 0 (letzter Vorgang)                 | ✅
```

**Kritischer Pfad: A → C → E → G** (alle Vorgänge mit GP = 0)

> [!tip] **Merksatz**
> Vorwärtsrechnung → MAXIMUM. Rückwärtsrechnung → MINIMUM. Puffer = SAZ − FAZ. Kritisch = Puffer 0.

## Kritischer Pfad

Der **kritische Pfad** verbindet alle Vorgänge mit **GP = 0** vom Projektstart bis zum Projektende.

- Jede Verzögerung auf dem kritischen Pfad verzögert das **gesamte Projekt** um denselben Betrag.
- Vorgänge mit GP > 0 haben Spielraum, ohne das Projektende zu verschieben.
- Vorgänge mit FP > 0 haben Spielraum, ohne den Nachfolger zu beeinflussen.

> [!important] **Kernregel**
> Kritischer Pfad = alle Vorgänge mit GP = 0. Projektdauer = FEZ des letzten Vorgangs. Auf dem kritischen Pfad gilt immer auch FP = 0.

### Praxisbeispiel Bürogebäude (Lehrermaterial)

```text
Vorgangsliste:
  1 Aushub              D=10, kein Vorgänger
  2 Mauern              D=25, Vorgänger: 1
  3 Elektroinstallation D=14, Vorgänger: 2
  4 Sanitärinstallation D=10, Vorgänger: 2
  5 Heizungsinstallation D=6, Vorgänger: 2
  6 Innenputz           D=15, Vorgänger: 3, 4, 5
  7 Tapezieren          D=5,  Vorgänger: 6

Ergebnis (Projektdauer: 69 Tage):
  Nr. | Vorgang     | FAZ | FEZ | SAZ | SEZ | GP | FP | krit.?
   1  | Aushub      |  0  |  10 |  0  |  10 |  0 |  0 | ✅
   2  | Mauern      | 10  |  35 | 10  |  35 |  0 |  0 | ✅
   3  | Elektro     | 35  |  49 | 35  |  49 |  0 |  0 | ✅
   4  | Sanitär     | 35  |  45 | 39  |  49 |  4 |  4 | ❌
   5  | Heizung     | 35  |  41 | 43  |  49 |  8 |  8 | ❌
   6  | Innenputz   | 49  |  64 | 49  |  64 |  0 |  0 | ✅
   7  | Tapezieren  | 64  |  69 | 64  |  69 |  0 |  0 | ✅

Kritischer Pfad: 1 → 2 → 3 → 6 → 7
```

## Gantt-Diagramm (Balkendiagramm)

Balkendiagramm: Vorgänge als Zeilen, Zeit auf der X-Achse. Zeigt Start, Ende und Parallelarbeit auf einen Blick.

```
         Tag: 0   10   20   30   40   50   60   69
1 Aushub:     ██████████
2 Mauern:               █████████████████████████
3 Elektro:                                        ██████████████
4 Sanitär:                                        ██████████
5 Heizung:                                        ██████
6 Innenputz:                                                    ███████████████
7 Tapezieren:                                                                   █████
```

| Merkmal | Netzplan | Gantt |
|---|---|---|
| Abhängigkeiten | ✅ explizit dargestellt | ⚠️ nur begrenzt |
| Zeitlicher Überblick | ⚠️ komplex | ✅ intuitiv |
| Kritischer Pfad | ✅ direkt ableitbar | ✅ markierbar |
| Ressourcen | ❌ nicht direkt | ✅ erweiterbar |
| Puffer (GP/FP) | ✅ exakt berechnet | ❌ nicht sichtbar |

## Belastungsdiagramm (Ressourcenplan)

Zeigt, wie viele Mitarbeiter (oder andere Ressourcen) pro Zeiteinheit benötigt werden. Basis: FAZ aus dem Netzplan.

**Vorgehen:**
1. Vorgänge entsprechend FAZ einplanen (frühestmöglich)
2. Pro Zeiteinheit: alle gleichzeitig aktiven Vorgänge summieren
3. Spitzenlast identifizieren → ggf. umplanen (Ressourcenausgleich)

```text
Beispiel (vereinfacht):
A (Tag 1-2):  2 MA
B (Tag 3):    3 MA
C (Tag 3-8):  2 MA
D (Tag 3-7):  6 MA
E (Tag 9-15): 8 MA
G (Tag 16-17):3 MA

Belastung:
Tag 1-2:   A=2               →  2 MA
Tag 3:     B=3, C=2, D=6    → 11 MA  ← Spitze
Tag 4-7:   C=2, D=6         →  8 MA
Tag 8:     C=2               →  2 MA
Tag 9-15:  E=8               →  8 MA
Tag 16-17: G=3               →  3 MA
```

> [!tip] **Merksatz**
> Im Belastungsdiagramm immer alle gleichzeitig aktiven Vorgänge addieren. Basis ist der FAZ aus dem Netzplan. Vorgänge mit FP > 0 lassen sich nach hinten verschieben, ohne den Nachfolger zu beeinflussen – gut für Ressourcenausgleich.

## Nutzwertanalyse (Entscheidungsmatrix)

Die Nutzwertanalyse bewertet mehrere Alternativen nach gewichteten Kriterien – z.B. Cloud vs. On-Premise, Make-or-Buy, Produktauswahl. Ergebnis: eine dimensionslose Kennzahl zum Vergleich.

**Vorgehen:**

1. **Kriterien** festlegen (z.B. Kosten, Skalierbarkeit, Sicherheit, Wartungsaufwand)
2. **Gewichtung (G)** vergeben: höhere Zahl = wichtigeres Kriterium (Summe der Gewichtungen oft = 100 % oder 10)
3. **Punkte (P)** je Alternative und Kriterium vergeben: z.B. 0–5 oder 0–3
4. **Nutzwert (N = G × P)** berechnen pro Zeile
5. **Summe** der Nutzwerte je Alternative → höchste Summe gewinnt

**Beispiel: Sommer 2024 – Cloud vs. On-Premise (vereinfacht)**

| Kriterium | Gewichtung (G) | Cloud (P) | Cloud (N=G×P) | On-Prem (P) | On-Prem (N=G×P) |
|---|---|---|---|---|---|
| Kosten | 4 | 3 | 12 | 2 | 8 |
| Skalierbarkeit | 3 | 5 | 15 | 2 | 6 |
| Datensicherheit | 5 | 2 | 10 | 5 | 25 |
| Wartungsaufwand | 3 | 4 | 12 | 2 | 6 |
| **Gesamt** | | | **49** | | **45** |

→ Cloud gewinnt mit 49 Punkten.

> [!important] **Kernregel**
> **N = G × P** pro Kriterium, dann alles summieren. Entscheidend ist nicht der höchste Einzelpunkt, sondern die **gewichtete Gesamtsumme**. Ein schlecht bewertetes Kriterium mit hoher Gewichtung kann die Entscheidung kippen.

> [!warning] **Achtung Falle**
> In der Prüfung wird oft eine unvollständige Tabelle ausgeteilt. Gewichtungen und Punkte müssen plausibel und begründbar sein – nicht willkürlich. Die Summe der Gewichtungen muss konsistent bleiben.

> [!tip] **Merksatz**
> Nutzwertanalyse ≠ einfachster Vergleich. Sie macht **qualitative Faktoren quantifizierbar**. Kritisch: Wer die Gewichtung festlegt, beeinflusst das Ergebnis – daher muss die Gewichtung fachlich begründet sein.
