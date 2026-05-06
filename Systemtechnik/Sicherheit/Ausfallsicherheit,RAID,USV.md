Ausfallsicherheit schützt das Schutzziel **Verfügbarkeit** – Systeme müssen auch bei Hardware-Fehlern, Stromausfällen oder Katastrophen funktionieren.

## Kennzahlen der Verfügbarkeit

| Begriff | Bedeutung |
|---|---|
| **RTO** (Recovery Time Objective) | Maximale tolerierbare Ausfallzeit |
| **RPO** (Recovery Point Objective) | Maximaler tolerierter Datenverlust (zeitlich) |
| **MTBF** (Mean Time Between Failures) | Durchschnittliche Zeit zwischen zwei Ausfällen |
| **MTTR** (Mean Time To Repair) | Durchschnittliche Zeit zur Wiederherstellung |

```text
Verfügbarkeit = MTBF / (MTBF + MTTR) × 100%

"Five Nines" = 99,999% = max. 5,26 Minuten Ausfall/Jahr
```

## RAID (Redundant Array of Independent Disks)

RAID kombiniert mehrere Festplatten zu einem logischen Volume für **Redundanz und/oder Performance**.

### RAID-Level im Vergleich

| RAID | Mindest-Festplatten | Redundanz | Kapazität | Lesen | Schreiben | Verlorene Disks |
|---|---|---|---|---|---|---|
| **RAID 0** | 2 | ❌ Keine | n × Disk | schnell | schnell | 0 |
| **RAID 1** | 2 | ✅ Spiegel | 1 × Disk | schnell | normal | n-1 |
| **RAID 5** | 3 | ✅ 1 Parity | (n-1) × Disk | schnell | langsamer | 1 |
| **RAID 6** | 4 | ✅ 2 Parity | (n-2) × Disk | schnell | langsamer | 2 |
| **RAID 10** | 4 | ✅ Spiegel+Stripe | n/2 × Disk | sehr schnell | schnell | 1 pro Spiegelpaar |

### Details

**RAID 0 (Striping):**
```text
Disk A: Block 1, 3, 5, ...
Disk B: Block 2, 4, 6, ...
→ Keine Redundanz! Fällt eine Disk aus → alles weg. Nur für Performance.
```

**RAID 1 (Mirroring):**
```text
Disk A: Block 1, 2, 3, ...  (Originaldaten)
Disk B: Block 1, 2, 3, ...  (identische Kopie)
→ Bei Ausfall einer Disk: Betrieb mit der anderen weiter
```

**RAID 5 (Striping mit verteilter Parity):**
```text
Disk A: D1  D4  D7  P3
Disk B: D2  D5  P2  D8
Disk C: D3  P1  D6  D9
→ Parity (P) auf alle Disks verteilt, 1 Disk-Ausfall tolerierbar
```

**RAID 10 (Stripe of Mirrors):**
```text
[Disk A + Disk B] gespiegelt  →  Striping über die Spiegelpaare
[Disk C + Disk D] gespiegelt  ↗
→ Gute Performance + gute Redundanz, teurer (50% Kapazität)
```

### Hot Spare

Eine ungenutzte Ersatz-Disk, die **automatisch** einspringt wenn eine Disk ausfällt und den Rebuild startet.

> [!warning] **Achtung Falle**
> **RAID ist kein Backup!** RAID schützt vor Hardware-Ausfall, nicht vor versehentlichem Löschen, Ransomware oder logischen Fehlern – diese werden sofort auf alle Disks repliziert.

## USV (Unterbrechungsfreie Stromversorgung)

USV schützt vor **Stromausfällen, Spannungsschwankungen und Transienten**.

### USV-Typen (IEC-Klassen)

| Typ | Kürzel | Funktionsweise | Umschaltzeit | Anwendung |
|---|---|---|---|---|
| **Offline / Standby** | VFD (Voltage and Frequency Dependent) | Normalbetrieb: Netz direkt, bei Ausfall → Batterie | 4–10 ms | Heimanwender, unkritisch |
| **Line-Interactive** | VI (Voltage Independent) | AVR (Spannungsregelung) im Normalbetrieb, Batterie bei Ausfall | 2–4 ms | KMU, Netzwerk |
| **Online / Double Conversion** | VFI (Voltage and Frequency Independent) | Immer über Batterie/Inverter, Netz lädt nur Batterie | 0 ms (kein Umschalten) | Rechenzentrum, kritische Server |

> [!tip] **Merksatz**
> **VFI = immer online** (kein Umschalten, kein Risiko), teuerste aber sicherste Variante für RZ.

### USV-Dimensionierung

```text
Benötigte USV-Leistung [VA]:
= Summe der angeschlossenen Geräte [Watt] / Power Factor (ca. 0,8)

Überbrückungszeit:
= Batteriekapazität [Wh] / Last [W]
```

## Backup-Strategien

| Strategie | Beschreibung | Vorteil | Nachteil |
|---|---|---|---|
| **Vollbackup** | Alle Daten komplett | Einfachste Wiederherstellung | Viel Speicher, langsam |
| **Differentiell** | Alle Änderungen seit letztem Vollbackup | Schnellere Wiederherstellung als inkrementell | Wächst mit der Zeit |
| **Inkrementell** | Alle Änderungen seit letztem Backup | Wenig Speicher, schnell | Wiederherstellung aufwändiger |

### 3-2-1-Backup-Regel

```text
3 Kopien der Daten
2 verschiedene Medien (z.B. HDD + Tape)
1 Kopie off-site (anderer Brandabschnitt / Cloud)
```

Erweiterung: **3-2-1-1-0** (1 × offline/air-gapped, 0 Fehler beim letzten Restore-Test)

> [!important] **Kernregel**
> Ausfallsicherheit hat drei Ebenen: **RAID** (Hardware-Redundanz), **USV** (Stromversorgung), **Backup** (Datensicherung). Alle drei sind nötig – keine ersetzt die andere.
