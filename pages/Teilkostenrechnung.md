[[AP2/MainPage|zurück]]

---

# Teilkostenrechnung (Deckungsbeitragsrechnung)

Die Teilkostenrechnung ordnet einem Produkt nur die **variablen Kosten** direkt zu. Die Fixkosten werden nicht aufgeteilt, sondern als Block aus dem Gesamtdeckungsbeitrag aller Produkte gedeckt. Synonym: **Deckungsbeitragsrechnung**.

## Abgrenzung zur Vollkostenrechnung

| Merkmal | Vollkostenrechnung | Teilkostenrechnung |
|---|---|---|
| Kostenzurechnung | alle Kosten (inkl. Gemeinkosten per Schlüssel) | nur variable Kosten |
| Fixkosten | auf Produkte umgelegt (Schlüsselung) | als Gesamtblock behandelt |
| Risiko | Fehlentscheidungen durch willkürliche Schlüsselung | keine Verzerrung |
| Einsatz | Kalkulation, Preisfindung | kurzfristige Entscheidungen, BEP |

> [!tip] **Merksatz**
> Teilkosten = nur der Teil der Kosten, der sich mit der Menge ändert. Der Rest (fix) bleibt außen vor.

## Kostenarten

**Variable Kosten (Kv / kv):** verändern sich proportional zur Produktionsmenge.
- Beispiele: Materialverbrauch, verbrauchsabhängige Energie, Akkordlöhne, Verpackung

**Fixe Kosten (Kf / kf):** fallen unabhängig von der Menge an.
- Beispiele: Mieten, Abschreibungen, Gehälter, Versicherungen

| Kostenart | Formel | Verhalten bei steigender Menge |
|---|---|---|
| Variable Gesamtkosten | `Kv = kv × x` | steigt proportional |
| Fixkosten | `Kf` | konstant |
| Gesamtkosten | `Kg = kv × x + Kf` | steigt, aber flacher als Erlös |
| Variable Stückkosten | `kv = Kv / x` | konstant |
| Fixe Stückkosten | `kf = Kf / x` | sinken mit steigender Menge (Fixkostendegression) |

## Formeln im Überblick

```text
I   Gesamtkosten:        K(x) = Kf + kv × x
II  Erlös:               E(x) = p × x
III Gewinn:              G(x) = E(x) − K(x)
IV  Stückdeckungsbeitrag: db  = p − kv
V   Gesamtdeckungsbeitrag: DB = db × x
VI  Break-Even-Point:    BEP  = Kf / db
VII Gewinn (alternativ): G    = DB − Kf
```

**Variablen:**
- `x` = Menge (Stück, kg, Teilnehmer …)
- `p` = Nettoverkaufspreis / Stückerlös
- `kv` = variable Stückkosten
- `Kf` = Fixkosten gesamt
- `db` = Stückdeckungsbeitrag
- `DB` = Gesamtdeckungsbeitrag

## Deckungsbeitrag

Der **Deckungsbeitrag (db)** ist die Differenz zwischen Erlös und variablen Kosten je Einheit – der Betrag, der zur Deckung der Fixkosten und zum Gewinn beiträgt.

```text
db = p − kv
DB = db × x = E − Kv
G  = DB − Kf
```

Solange `db > 0`, lohnt sich die Produktion kurzfristig – das Produkt trägt zur Fixkostendeckung bei, auch wenn noch kein Gewinn entsteht.

> [!important] **Kernregel**
> Positiver Deckungsbeitrag → produzieren (auch wenn Gesamtverlust). Negativer Deckungsbeitrag → Produkt abbauen (macht jeden Verlust größer).

## Kurzfristige Preisuntergrenze (kPUG)

Die **kurzfristige Preisuntergrenze** liegt bei den variablen Stückkosten:

```text
kPUG = kv
```

Unter diesem Preis verliert man pro Einheit Geld – selbst die variablen Kosten werden nicht gedeckt. Kurzfristig kann man trotzdem unterhalb der Vollkosten anbieten, solange `p ≥ kv`.

**Gründe, trotz Unterschreitung der Vollkosten zu produzieren:**
- Kundenbindung / laufende Geschäftsbeziehung erhalten
- Auslastung der Kapazitäten / Beschäftigung des Personals
- Einführungsphase am Markt (Marktanteile sichern)
- Konkurrenz vom Markt drängen

## Break-Even-Point (BEP)

Der Break-Even-Point ist die Menge, bei der Erlöse = Gesamtkosten: weder Gewinn noch Verlust.

```text
E(x) = K(x)
p × x = kv × x + Kf
(p − kv) × x = Kf
db × x = Kf
x_BEP = Kf / db
```

> [!warning] **Achtung Falle**
> Der BEP ist eine gebrochene Zahl → immer **aufrunden** (erste Menge mit echtem Gewinn, nicht auf der Schwelle).

## Rechenbeispiele

### Beispiel 1 – Schulungskurs (Fixkosten pro Kurs)

| Position | Wert |
|---|---|
| Kursgebühr | 3.000 € / Teilnehmer |
| Variable Kosten | 110 € / Stunde / Teilnehmer |
| Kursdauer | 3 Tage × 8 Stunden = 24 h |
| Fixkosten gesamt | 25.200 € / Monat (30 Tage) |

```text
kv pro Teilnehmer = 24 h × 110 €/h              = 2.640 €
db pro Teilnehmer = 3.000 € − 2.640 €           =   360 €

Fixkosten pro Kurs = 25.200 € / 30 Tage × 3 Tage = 2.520 €

BEP = 2.520 € / 360 € = 7 Teilnehmer
```

→ Ab **7 Teilnehmern** deckt der Kurs seine Kosten.

### Beispiel 2 – Saisonkarte (großes BEP-Beispiel)

```text
Verkaufspreis:          900 €
Fixkosten gesamt:   1.485.000 € (IT, Räume, Gehälter, AfA …)
Variable Stückkosten:     90 € (Material 60 + Energie 30)

db  = 900 − 90 = 810 €
BEP = 1.485.000 / 810 = 1.833,33 → ab 1.834 Stück
```

### Beispiel 3 – Präsenzseminar vs. Online

| | Präsenz | Online |
|---|---|---|
| Gebühr/TN | 380 € | 250 € |
| Fixkosten (Kf) | 1.830 € | 1.170 € |
| Variable Kosten/TN (kv) | 220 € | 35 € |
| db | 160 € | 215 € |
| BEP | 11,44 → **12 TN** | 5,44 → **6 TN** |

Kf Präsenz: Raummiete/IT 780 + Dozent 800 + Betreuung 250 = 1.830 €  
kv Präsenz: Verpflegung 120 + Mappe/Urkunde 75 + Parken 25 = 220 €

→ Online hat höheren db und niedrigeren BEP → wirtschaftlicher bei kleinen Gruppen.
