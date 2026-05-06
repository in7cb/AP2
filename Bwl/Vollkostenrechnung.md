Die Kosten- und Leistungsrechnung (KLR) beantwortet, **welche** Kosten entstehen, **wo** sie entstehen und **wofür** sie anfallen – in drei aufeinanderfolgenden Stufen: Kostenartenrechnung → Kostenstellenrechnung → Kostenträgerrechnung.

---

## 1. Kostenartenrechnung – Was kostet es?

### Abgrenzung: Kosten vs. Aufwand vs. Ausgabe

| Begriff | Definition |
|---|---|
| **Ausgabe** | Jeder Geldabgang |
| **Aufwand** | Gesamtverbrauch von Gütern/Diensten (GuV-relevant) |
| **Kosten** | Verbrauch von Gütern/Diensten zur Leistungserstellung (betrieblich, periode-bezogen) |

### Einzelkosten vs. Gemeinkosten

| | Einzelkosten (EK) | Gemeinkosten (GK) |
|---|---|---|
| Zurechenbarkeit | direkt auf Kostenträger | nur indirekt über Schlüssel |
| Beispiele | Fertigungslöhne (FEK), Fertigungsmaterial (MEK), SEK | Gehälter, Miete, Steuern, Heizung |
| Verrechnung | → direkt in Kalkulation | → über BAB auf Kostenstellen |

**Sondereinzelkosten:**
- **SEK der Fertigung:** Modelle, Spezialwerkzeug, Patentgebühren – direkt zurechenbar, aber auftragsbezogen
- **SEK des Vertriebs (SEV):** Spezialverpackung, Vertreterprovision

### Fixe vs. variable Kosten

| | Fixe Kosten | Variable Kosten |
|---|---|---|
| Abhängigkeit | unabhängig vom Beschäftigungsgrad | steigen/sinken mit Auslastung |
| Beispiele | Miete, Gehälter, Fremdkapitalzinsen | Fertigungslöhne (Akkord), Rohstoffe |

> [!tip] **Merksatz**
> Gemeinkosten ≠ fixe Kosten. Gemeinkosten sind nicht direkt zurechenbar – sie können trotzdem variabel sein (z.B. Hilfslöhne steigen mit Produktion).

---

## 2. Kostenstellenrechnung – Wo entstehen die Kosten?

**Kostenstellen** = organisatorische Einheiten, denen Kosten zugeordnet werden (= Orte der Kostenentstehung).

**4 Hauptkostenstellen** im einstufigen BAB:

| Kostenstelle | Beispielbereich |
|---|---|
| **Material** | Einkauf, Eingangslager, Wareneingang |
| **Fertigung** | Produktion, Werkstatt |
| **Verwaltung** | Buchhaltung, Geschäftsführung |
| **Vertrieb** | Verkauf, Außendienst, Versand |

### Betriebsabrechnungsbogen (BAB)

Der BAB verteilt die **Gemeinkosten** über Umlageschlüssel auf die Kostenstellen, um anschließend **Gemeinkostenzuschlagssätze** zu berechnen.

> [!warning] **Achtung Falle**
> Im BAB werden **nur Gemeinkosten** verteilt! Einzelkosten gehen direkt in die Kalkulation – nie in den BAB!

**Umlageschlüssel** (verursachungsgerecht wählen):
- **Mengenschlüssel:** z.B. Miete nach m², Strom nach kWh
- **Wertschlüssel:** z.B. Sozialleistungen nach Lohnsumme der Kostenstelle

**Beispiel BAB:**

```text
Gemeinkostenart    Summe    Material   Fertigung  Verwaltung  Vertrieb
----------------------------------------------------------------------
Gehälter           50.000    10.000      20.000      10.000     10.000
Miete              20.000     4.000      10.000       4.000      2.000
Versicherungen     10.000     2.000       4.000       2.000      2.000
----------------------------------------------------------------------
Summe GK           80.000    16.000      34.000      16.000     14.000

Zuschlagsbasis             100.000     170.000     320.000    320.000
Zuschlagssatz                 16 %        20 %         5 %     4,375 %
```

### Gemeinkostenzuschlagssätze (Formeln)

| Zuschlagssatz | Formel |
|---|---|
| **MGKZ** (Material-GK) | MGK / MEK × 100 |
| **FGKZ** (Fertigungs-GK) | FGK / FEK × 100 |
| **VwGKZ** (Verwaltungs-GK) | VwGK / HK × 100 |
| **VtGKZ** (Vertriebs-GK) | VtGK / HK × 100 |

> [!important] **Kernregel**
> Material- und Fertigungs-GK: Basis = **Einzelkosten der Kostenstelle** (MEK bzw. FEK).
> Verwaltungs- und Vertriebs-GK: Basis = **Herstellkosten (HK)**.

---

## 3. Kostenträgerrechnung – Wofür entstehen die Kosten?

### Kalkulationsschema (Zuschlagskalkulation)

```text
  Materialeinzelkosten (MEK)
+ Materialgemeinkosten (MGK = MEK × MGKZ)
= Materialkosten

+ Fertigungseinzelkosten / Fertigungslöhne (FEK)
+ Fertigungsgemeinkosten (FGK = FEK × FGKZ)
+ Sondereinzelkosten der Fertigung (SEK)
= Fertigungskosten
─────────────────────────────────────────
= Herstellkosten (HK)

+ Verwaltungsgemeinkosten (VwGK = HK × VwGKZ)
+ Vertriebsgemeinkosten (VtGK = HK × VtGKZ)
+ Sondereinzelkosten des Vertriebs (SEV)
= Selbstkosten (SK)
```

### Von Selbstkosten zum Listenverkaufspreis (Vorwärtskalkulation)

```text
  Selbstkosten (SK)
+ Gewinn
= Barverkaufspreis (BVP)
+ Kundenskonto          (÷ (1 − Skontosatz))
= Zielverkaufspreis (ZVP)
+ Kundenrabatt          (÷ (1 − Rabattsatz))
= Listenverkaufspreis netto (LVP netto)
+ MwSt. (19 %)
= Listenverkaufspreis brutto (LVP brutto)
```

> [!warning] **Achtung Falle**
> Skonto und Rabatt werden **aufaddiert**, nicht abgezogen – man rechnet rückwärts: BVP ist der Preis *nach* Skonto, also muss ZVP *höher* sein. Formel: ZVP = BVP ÷ (1 − Skontosatz).

### Differenzkalkulation

Wenn ein Marktpreis (z.B. Konkurrenzangebot) vorgegeben ist → prüfen ob Auftrag noch rentabel:

```text
  Listenverkaufspreis brutto (vorgegeben)
− MwSt.
= LVP netto
− Kundenrabatt
= ZVP
− Kundenskonto
= BVP
− Gewinn
= max. Selbstkosten

  Vergleich: Selbstkosten ≤ max. SK? → rentabel
```

---

## Abkürzungsübersicht

| Kürzel | Bedeutung |
|---|---|
| KLR | Kosten- und Leistungsrechnung |
| BAB | Betriebsabrechnungsbogen |
| EK / GK | Einzel- / Gemeinkosten |
| MEK | Materialeinzelkosten |
| FEK | Fertigungseinzelkosten (= Fertigungslöhne) |
| MGK / FGK | Material- / Fertigungsgemeinkosten |
| VwGK / VtGK | Verwaltungs- / Vertriebsgemeinkosten |
| SEK / SEV | Sondereinzelkosten Fertigung / Vertrieb |
| HK | Herstellkosten |
| SK | Selbstkosten |
| BVP / ZVP / LVP | Bar- / Ziel- / Listenverkaufspreis |
| MGKZ / FGKZ | Materialgemeinkostenzuschlagssatz / Fertigungsgemeinkostenzuschlagssatz |
| VwGKZ / VtGKZ | Verwaltungs- / Vertriebsgemeinkostenzuschlagssatz |
