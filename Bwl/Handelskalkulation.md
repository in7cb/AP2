Systematische Preiskalkulation im Handel: Ausgehend vom Einkaufspreis wird der Verkaufspreis ermittelt (oder umgekehrt), sodass alle Kosten und der Gewinn gedeckt sind.

---

## Das Kalkulationsschema

```text
Listeneinkaufspreis netto (LEP)         100 %
– Liefererrabatt                         xx %
= Zieleinkaufspreis                      xx %   100 %
– Liefererskonto                                  xx %
= Bareinkaufspreis                                xx %
+ Bezugskosten                               (€-Betrag)
= Einstandspreis (EP / Bezugspreis)      100 %
+ Handlungskosten (HK-Zuschlag auf EP)    xx %
= Selbstkosten                            xx %
+ Gewinnzuschlag                          xx %
= Barverkaufspreis (BVP)                          xx %  ← verminderter Grundwert!
+ Kundenskonto                                     xx %
= Zielverkaufspreis (ZVP)                        100 %  ← verminderter Grundwert!
+ Kundenrabatt                                     xx %
(+ Vertreterprovision)                             xx %
= Listenverkaufspreis netto (LVP)                100 %
```

> [!important] **Kernregel: Zwei Prozentbasen!**
> Der Lieferer-Teil (LEP → EP) hat den **Listeneinkaufspreis** als 100 %-Basis.  
> Der Kunden-Teil (BVP → LVP) hat den **Listenverkaufspreis** als 100 %-Basis – **nicht** den BVP oder ZVP.  
> Das ist der "verminderter Grundwert"-Trick (siehe unten).

---

## Verminderter Grundwert (Prüfungsfalle!)

Beim Liefererrabatt bekommst **du** den Rabatt → der LEP ist 100 %, du zahlst weniger.  
Beim Kundenrabatt gibt **dein Kunde** seinen Preis vor → der **LVP** ist 100 %, der ZVP ist nur (100 − Rabatt %).

**Problem:** Wenn du vom BVP zum LVP vorwärts rechnest, ist der BVP **nicht** 100 % – er ist der bereits geminderte Grundwert.

### Formel: Vorwärts vom verminderten Grundwert

$$\text{ZVP} = \text{BVP} \times \frac{100}{100 - \text{Kundenskonto\%}}$$

$$\text{LVP} = \text{ZVP} \times \frac{100}{100 - \text{Kundenrabatt\%}}$$

### Beispiel (Smartphone)

Selbstkosten 1.000 €, Gewinn 8 % → BVP = 1.080 €. Der Verkäufer gewährt 15 % Kundenrabatt.

| | Falsch (BVP = 100 %) | Richtig (LVP = 100 %) |
|---|---|---|
| BVP | 1.080 € (100 %) | 1.060,80 € (85 %) |
| + Rabatt 15 % | 162 € auf BVP | 187,20 € auf LVP |
| = LVP | **1.242 €** ❌ | **1.248 €** ✓ |

Richtig: LVP = 1.080 € × 100/85 = **1.270,59 €** *(korrekte Variante mit BVP=1080)*

> [!warning] **Achtung Falle**
> Niemals Skonto oder Kundenrabatt einfach auf den Barverkaufspreis aufschlagen (× 1,15). Immer durch den verminderten Grundwert teilen: BVP ÷ (1 − Skonto/100).

---

## Die drei Kalkulationsarten

### 1. Vorwärtskalkulation

**Situation:** Einkaufspreis steht fest → Verkaufspreis berechnen.

**Beispiel – Bohrmaschine** (Maschinengroßhandlung):

| Position | % | € |
|---|---|---|
| Listeneinkaufspreis netto | 100 % | 450,00 |
| – Liefererrabatt | 20 % | 90,00 |
| **= Zieleinkaufspreis** | 80 % | **360,00** |
| – Liefererskonto | 3 % | 10,80 |
| **= Bareinkaufspreis** | | **349,20** |
| + Bezugskosten | | 5,40 |
| **= Einstandspreis** | 100 % | **354,60** |
| + Handlungskosten | 32,15 % | 114,00 |
| **= Selbstkosten** | | **468,60** |
| + Gewinnzuschlag | 15 % | 70,29 |
| **= Barverkaufspreis** | | **538,89** |
| + Kundenskonto (verm. GW) | 2 % → ÷ 0,98 | 11,00 |
| **= Zielverkaufspreis** | | **549,89** |
| + Kundenrabatt (verm. GW) | 10 % → ÷ 0,90 | 61,10 |
| **= Listenverkaufspreis netto** | | **610,99** |

*(Zusatz: + Vertreterprovision 5 % → weitere Variante: LVP = 643,84 €)*

---

### 2. Rückwärtskalkulation

**Situation:** Marktpreis (LVP) ist vorgegeben (z. B. UVP, regulierter Preis) → maximal tragbaren Einkaufspreis berechnen.  
Schema wird **von unten nach oben** durchlaufen. Skonto/Rabatt werden dabei normal subtrahiert (LVP × Rabatt%).

**Beispiel – Küchenmaschine** (Katalogpreis 320,00 €):

| Position | % | € |
|---|---|---|
| Listenverkaufspreis netto | | 320,00 |
| – Kundenrabatt 5 % | 5 % | 16,00 |
| **= Zielverkaufspreis** | | **304,00** |
| – Kundenskonto 2 % | 2 % | 6,08 |
| **= Barverkaufspreis** | | **297,92** |
| – Gewinnzuschlag 10 % | 10 % | 27,08 |
| **= Selbstkosten** | | **270,84** |
| – Handlungskosten 12,5 % | 12,5 % | 30,09 |
| **= Einstandspreis** | | **240,74** |
| – Bezugskosten 6 % | 6 % | 13,63 |
| **= Bareinkaufspreis** | | **227,12** |
| + Liefererskonto 2 % | 2 % | 4,64 |
| **= Zieleinkaufspreis** | | **231,75** |
| + Liefererrabatt 10 % | 10 % | 25,75 |
| **= Listeneinkaufspreis netto** | | **257,50** |

> [!tip] **Merksatz Rückwärtskalkulation**
> Bei der Rückwärtskalkulation: was vorwärts addiert wurde, wird rückwärts subtrahiert – und umgekehrt. Skonto und Rabatt des Lieferers werden wieder draufgerechnet.

---

### 3. Differenzkalkulation

**Situation:** Sowohl Einkaufs- als auch Verkaufspreis sind vorgegeben → erzielbaren Gewinn berechnen.  
Das Schema wird vollständig durchlaufen; der Gewinn ergibt sich als Differenz zwischen Barverkaufspreis und Selbstkosten.

**Beispiel – Staubsauger** (Elektrogroßhändler KURZ):

| Position | % | € |
|---|---|---|
| Listeneinkaufspreis netto | 100 % | 160,00 |
| – Liefererrabatt | 10 % | 16,00 |
| **= Zieleinkaufspreis** | 90 % | **144,00** |
| – Liefererskonto | 2 % | 2,88 |
| **= Bareinkaufspreis** | | **141,12** |
| + Bezugskosten | | 4,88 |
| **= Einstandspreis** | 100 % | **146,00** |
| + Handlungskosten | 30 % | 43,80 |
| **= Selbstkosten** | | **189,80** |
| + **Gewinnzuschlag** (gesucht!) | | **9,56** |
| **= Barverkaufspreis** | 97 % | **199,36** |
| + Kundenskonto | 3 % | 6,17 |
| **= Zielverkaufspreis** | 100 % | **205,53** |
| + Kundenrabatt | 7 % | 15,47 |
| **= Listenverkaufspreis netto** | | **221,00** |

Gewinn: 9,56 € → **5,04 %** auf Selbstkosten (bzw. auf den Barverkaufspreis: 9,56 / 199,36 × 100 ≈ 4,80 %)

---

## Kalkulationskennzahlen

Diese drei Kennzahlen werden oft in Prüfungsaufgaben abgefragt:

| Kennzahl | Formel | Basis |
|---|---|---|
| **Kalkulationsfaktor (KF)** | LVP ÷ Einstandspreis | – |
| **Kalkulationszuschlag (KZ)** | (LVP − EP) × 100 ÷ EP | % vom EK |
| **Handelsspanne (HS)** | (LVP − EP) × 100 ÷ LVP | % vom VK |

**Beispiel** mit Bohrmaschine (EP = 354,60 €, LVP = 610,99 €):

```text
KF = 610,99 / 354,60 = 1,723
KZ = (610,99 – 354,60) × 100 / 354,60 = 72,3 %
HS = (610,99 – 354,60) × 100 / 610,99 = 41,9 %
```

> [!tip] **Merksatz**
> KZ und HS beschreiben dieselbe Spanne, nur mit unterschiedlicher Basis.  
> KF × EP = LVP – der Faktor beschleunigt die Kalkulation bei gleichen Margen.

---

## Prüfungsaufgabe: Switch-Kalkulation

Gegeben: LEP = 2.567,09 €, Liefererrabatt 5 %, Liefererskonto 2 %, HK-Zuschlag 20 %, Gewinnaufschlag 6 %, Kundenrabatt 5 %, Kundenskonto 3 %.

```text
LEP:            2.567,09 €
– Liefererrabatt 5 %:  128,35 €
= ZieleKP:      2.438,73 €
– Liefererskonto 2 %:   48,77 €
= BareKP:       2.389,96 €
(+ Bezugskosten:    0,00 €)
= Einstandspreis:   2.389,96 €
+ HK 20 %:        477,99 €
= Selbstkosten:    2.867,95 €
+ Gewinn 6 %:      172,08 €
= BVP:           3.040,03 €
+ Kundenskonto 3 % (÷ 0,97):   94,02 €
= ZVP:           3.134,05 €
+ Kundenrabatt 5 % (÷ 0,95):  164,95 €
= LVP:           3.299,00 €
```

KF = 3.299,00 / 2.389,96 = **1,38**

Kann der Switch auch zu 3.199,00 € angeboten werden? Nein, denn 3.199 € < LVP 3.299 € → **Verlust**, sofern kein Preisnachlass bei EK verhandelt wird.
