Der **Stundenverrechnungssatz (SVS)** ist der Preis, den ein Dienstleistungsunternehmen pro produktiver Arbeitsstunde beim Kunden berechnet – so dass alle Kosten (inkl. Gemeinkosten) gedeckt und ein Gewinn erzielt werden.

> [!important] **Kernprinzip**
> Nur die Mitarbeiter, die direkt beim Kunden abrechenbar sind (produktive Stunden), müssen **alle** Gemeinkosten des Betriebes mittragen – inklusive der Stunden, die nicht abrechenbar sind (Verwaltung, Fortbildung, Leerlauf).

---

## Schritt 1 – Produktive Stunden ermitteln

Von den Kalendertagen werden alle nicht-produktiven Tage abgezogen:

```text
  365  Kalendertage
- 104  Wochenenden (Sa + So)
-  xx  Feiertage an Werktagen
-  xx  Urlaubstage
-  xx  Krankheitstage
-  xx  Fortbildungstage
-  xx  Unproduktive Tage (kein Kundeneinsatz)
─────────────────────────────────────────────
=  xx  Verrechenbare Tage
×   8  Arbeitsstunden/Tag
─────────────────────────────────────────────
=  xx  Produktive Stunden pro Mitarbeiter/Jahr
```

**Variante mit Korrekturfaktor** (häufig in Prüfungsaufgaben):

```text
Anwesenheitstage = Arbeitstage − Urlaub − Feiertage − Krankheit
Anwesenheitsstunden = Anwesenheitstage × h/Tag
Produktivstunden = Anwesenheitsstunden × Produktivfaktor (z. B. 95 %)
```

> [!tip] **Merksatz**
> Anwesenheit ≠ Produktivität. Ein Korrekturfaktor von 95 % bedeutet: 5 % der Anwesenheitszeit ist nicht abrechenbar (Fahrten, Nacharbeit, Leerlauf).

---

## Schritt 2 – Selbstkosten ermitteln

```text
Löhne/Gehälter (alle produktiven MA) + Gemeinkosten = Selbstkosten
```

**Gemeinkosten** = alle Kosten, die nicht direkt einem Auftrag zugeordnet werden können:
Miete, Versicherungen, Verwaltungsgehälter, Marketing, Finanzierungskosten, sonstige Fixkosten.

Wenn nur ein **Teil der Gemeinkosten** auf den SVS umgelegt wird (Rest via Handelskalkulation):

```text
Umzulegende Gemeinkosten = Gesamtgemeinkosten × Umlageanteil (%)
```

---

## Schritt 3 – Stundenverrechnungssatz berechnen

```text
Selbstkostenstundensatz = Selbstkosten gesamt ÷ Produktivstunden gesamt

Stundensatz netto      = Selbstkostenstundensatz × (1 + Gewinnaufschlag %)

Stundensatz brutto     = Stundensatz netto × 1,19  (bei 19 % MwSt.)
```

---

## Alternative: Zuschlagssatz-Methode

Statt absoluter Gemeinkosten kann ein **prozentualer Zuschlag auf die Löhne** berechnet werden:

```text
Gemeinkostenzuschlagssatz = Gemeinkosten ÷ Löhne × 100
```

**Probe:** Zuschlagssatz × Löhne = Gemeinkosten ✓

> [!tip] **Merksatz**
> Zuschlagssatz = Gemeinkosten / Löhne × 100 — gleiche Logik wie beim Gemeinkostenzuschlag in der Vollkostenrechnung.

---

## Durchgerechnetes Beispiel (Lehrerbeispiel)

**Gegeben:** 5 Mitarbeiter, Bruttolohn je 50.000 €/Jahr, Gemeinkosten 180.000 €/Jahr, Gewinnaufschlag 14 %, MwSt. 19 %

### Produktive Stunden pro Mitarbeiter

| Position | Tage |
|---|---|
| Kalendertage | 365 |
| − Wochenenden | − 104 |
| − Feiertage | − 12 |
| − Urlaub | − 28 |
| − Krankheit | − 5 |
| − Fortbildung | − 7 |
| − Unproduktive Tage | − 20 |
| **Verrechenbare Tage** | **189** |
| × 8 h/Tag | |
| **Produktive Stunden** | **1.512 h** |

### Kalkulation

| Position | Betrag |
|---|---|
| Bruttolöhne (5 × 50.000 €) | 250.000 € |
| + Gemeinkosten | 180.000 € |
| = **Selbstkosten** | **430.000 €** |
| Produktivstunden gesamt (5 × 1.512) | 7.560 h |
| **Selbstkostenstundensatz** | **56,88 €** |
| + Gewinnaufschlag 14 % | 7,96 € |
| **Stundensatz netto** | **64,84 €** |
| + MwSt. 19 % | 12,32 € |
| **Stundensatz brutto** | **77,16 €** |

**Zuschlagssatz-Variante:**

```text
Zuschlagssatz = 180.000 € ÷ 250.000 € × 100 = 72 %
Probe: 72 % × 250.000 € = 180.000 € ✓
```

---

## Prüfungsaufgabe-Muster

**Aufgabe:** Ein Unternehmen kalkuliert mit Gesamtgemeinkosten von 1.350.000 €. Davon werden 30 % auf die produktiven Löhne umgelegt. 6 Techniker, Jahreslohn je 56.000 € (260 Arbeitstage). 30 Urlaubstage, 10 Feiertage, 5 Krankheitstage. 7,8 h/Tag. 5 % der Anwesenheitsstunden sind unproduktiv. Gewinnzuschlag 10 %. SVS berechnen.

**Lösung:**

```text
Schritt 1 – Produktivstunden
Anwesenheitstage     = 260 − 30 − 10 − 5 = 215 Tage
Anwesenheitsstunden  = 215 × 7,8 h = 1.677 h/Techniker
Produktivstunden     = 1.677 × 0,95 = 1.593,15 h/Techniker
Gesamt (6 Techniker) = 6 × 1.593,15 = 9.558,9 h

Schritt 2 – Selbstkosten
Umzulegende Gemeinkosten  = 1.350.000 × 30 % = 405.000 €
Lohnkosten gesamt          = 6 × 56.000 = 336.000 €
Selbstkosten               = 336.000 + 405.000 = 741.000 €

Schritt 3 – SVS
SVS (Selbstkosten)  = 741.000 ÷ 9.558,9 = 77,52 €
+ Gewinnzuschlag 10 % → 77,52 × 1,10 = 85,27 € (netto)
```

> [!warning] **Achtung Falle**
> In manchen Aufgaben werden **nicht alle Gemeinkosten** auf den SVS umgelegt (Rest geht in den Handelskalkulations-Zuschlag). Immer prüfen, welcher **Prozentsatz** der Gemeinkosten auf die Löhne umgelegt wird!

> [!warning] **Achtung Falle**
> Produktivfaktor vs. unproduktive Tage: Manchmal werden unproduktive Tage **direkt** abgezogen (Schritt 1 oben), manchmal als **Prozentwert** von den Anwesenheitsstunden (Korrekturfaktor). Aufgabenstellung genau lesen!

---

## Praxistipp

In der Praxis wird heute oft in **15-Minuten-Einheiten** abgerechnet (nicht mehr ganze Stunden). SVS ÷ 4 = Verrechnungssatz je Viertelstunde.
