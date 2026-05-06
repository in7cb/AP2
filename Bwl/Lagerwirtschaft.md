Lagerwirtschaft umfasst die systematische Planung, Steuerung und Kontrolle von Lagerbeständen – Ziel: minimale Lagerkosten bei gleichzeitiger Lieferfähigkeit.

## ABC-Analyse

Die ABC-Analyse klassifiziert Lagerartikel nach ihrem **Wertanteil am Gesamtlagerbestand**, um Prioritäten in der Beschaffung und Überwachung zu setzen.

**Vorgehensweise:**
1. Verbrauchswert je Artikel berechnen: `Menge × Einstandspreis`
2. Nach Verbrauchswert absteigend sortieren
3. Kumulierten Wertanteil berechnen
4. Klassen zuordnen

| Klasse | Kumulierter Wertanteil | Mengenanteil | Strategie |
|--------|------------------------|--------------|-----------|
| **A** | 0 – ~70 % | ~5–10 % | Intensiv überwachen, Bestellpunktverfahren, JIT |
| **B** | ~70 – ~90 % | ~20–30 % | Mittlere Überwachung |
| **C** | ~90 – 100 % | ~60–80 % | Vereinfachte Bestellung (Sammelbestellung, Rahmenvertrag) |

**Beispielrechnung:**

| ArtNr | Menge | EP (€) | Verbrauchswert | Wertanteil | kum. | Klasse |
|-------|-------|--------|----------------|------------|------|--------|
| 02581 | 750 | 160,00 | 120.000,00 | 56,52 % | 56,52 % | A |
| 02583 | 500 | 79,00 | 39.500,00 | 18,60 % | 75,12 % | B |
| 02584 | 600 | 36,00 | 21.600,00 | 10,17 % | 85,30 % | B |
| 02586 | 4000 | 4,00 | 16.000,00 | 7,54 % | 92,83 % | C |
| … | … | … | … | … | … | C |

> [!tip] **Merksatz ABC**
> **A**uf **B**eobachtung **C**ommt es an – A-Güter brauchen die meiste Aufmerksamkeit, obwohl sie mengenmäßig selten sind.

**Maßnahmen für C-Güter** (Kosteneinsparung):
- Sammelbestellungen / Rahmenverträge → weniger Bestellvorgänge
- Höhere Bestellmengen → Mengenrabatte, seltene Bestellungen

## Bestellverfahren

### Bestellpunktverfahren (Bestellzeitpunktverfahren)

Bestellung wird ausgelöst, sobald der Lagerbestand den **Meldebestand** erreicht oder unterschreitet. Bestellmenge ist oft fest.

```text
Meldebestand = Tagesverbrauch × Lieferzeit + Sicherheitsbestand
```

**Beispiel:** Tagesverbrauch = 50 Stk, Lieferzeit = 7 Tage, Sicherheitsbestand = 100 Stk
```text
Meldebestand = 50 × 7 + 100 = 450 Stück
```

| | Vorteile | Nachteile |
|---|---|---|
| **Bestellpunktverfahren** | Bedarfsgerecht, flexible Reaktion auf Schwankungen, geringere Lagerkosten | Hoher Überwachungsaufwand (kontinuierliche Bestandskontrolle), Planungsaufwand |

### Bestellrhythmusverfahren

Bestellung erfolgt in **festen Zeitintervallen**, unabhängig vom aktuellen Lagerbestand. Die Bestellmenge kann variieren.

| | Vorteile | Nachteile |
|---|---|---|
| **Bestellrhythmusverfahren** | Einfachere Planung, geringerer Verwaltungsaufwand, bessere Lieferantenkonditionen | Höhere Lagerkosten möglich, weniger flexibel bei Bedarfsschwankungen |

> [!important] **Kernregel Bestellverfahren**
> Bestellpunktverfahren = **wann** bestellen (Meldebestand), Menge fix.
> Bestellrhythmusverfahren = **wie oft** bestellen (Intervall), Menge variabel.

> [!warning] **Achtung Falle**
> Beim Meldebestand wird der **Sicherheitsbestand addiert** – nicht vergessen! Ohne Sicherheitsbestand riskiert man Lieferengpässe bei Verzögerungen.

## Just-in-Time (JIT)

Produktions- und Logistiksystem: Materialien werden **fertigungssynchron** geliefert – genau zum richtigen Zeitpunkt, in der richtigen Menge und Qualität. Lagerhaltung wird auf ein Minimum reduziert (Lagerung „auf die Straße verlagert").

Ursprung: Japanische Automobilindustrie (1970er), bekannt auch als **Kanban-Prinzip**.

**Voraussetzungen:**
- Enge informationstechnische Verknüpfung zwischen Abnehmer und Lieferant
- Langfristige Lieferverträge und hohes gegenseitiges Vertrauen
- Große Umschlaghäufigkeiten (lohnt sich nur bei großen, regelmäßigen Mengen)
- Hohe Pünktlichkeit und Qualitätszuverlässigkeit des Lieferanten
- Gute Infrastruktur (kurze Lieferzeiten, Ausweichmöglichkeiten)

| | JIT |
|---|---|
| **Vorteile** | Geringere Lagerkosten, kein gebundenes Kapital im Lager, keine Bestandsrisiken, schnellere Reaktion auf Kundenwünsche, Risikoverlagerung auf Lieferanten |
| **Nachteile** | Höhere Bestellkosten, höhere Transportkosten (häufige Kleinlieferungen), hohe Abhängigkeit vom Lieferanten, Anfälligkeit für Streiks/Staus, kein Puffer bei Produktionsausfall |

**Zielkonflikt:**

```text
Sinkende Lagerkosten  ←→  Steigende Bestell- & Transportkosten
```

> [!tip] **Merksatz JIT**
> JIT = „Keine Ware auf Vorrat" – die Straße ist das Lager. Funktioniert nur mit zuverlässigen Partnern.

> [!warning] **Achtung Falle**
> JIT senkt **Lagerkosten**, erhöht aber **Bestell- und Transportkosten** – in der Prüfung beide Seiten des Zielkonflikts nennen!
