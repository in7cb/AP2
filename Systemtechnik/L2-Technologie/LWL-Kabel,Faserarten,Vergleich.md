Lichtwellenleiter (LWL) übertragen Daten als Lichtimpulse durch Glasfasern. Kein elektromagnetischer Einfluss, hohe Reichweiten, hohe Bandbreite.

## Funktionsprinzip

Licht wird durch **Totalreflexion** im Glasfaserkern geführt. Der Kern hat einen höheren Brechungsindex als der Mantel (Cladding) → Licht kann nicht austreten.

```
        Kern (Core)
Mantel ─────────────────── Mantel
       Licht →→→→→→→→→→→
Mantel ─────────────────── Mantel
        Cladding
```

## Singlemode vs. Multimode

| Merkmal | Singlemode (SM) | Multimode (MM) |
|---|---|---|
| Kerndurchmesser | **9 µm** | **50 µm** (OM2–4) / 62,5 µm (OM1) |
| Mantel | 125 µm | 125 µm |
| Lichtquelle | **Laser** (teuer) | **LED / VCSEL** (günstiger) |
| Reichweite | **bis 80+ km** | **bis 550 m** (OM3) |
| Dispersion | sehr gering | Modale Dispersion (begrenzend) |
| Wellenlänge | 1310 nm / 1550 nm | 850 nm / 1300 nm |
| Kosten Transceiver | höher | günstiger |
| Farbe Kabel | **gelb** | **orange** (OM1/2), **aqua** (OM3/4), **lila** (OM5) |

> [!tip] **Merksatz**
> **S**inglemode = **S**chmaler Kern (9 µm) = **S**ehr weit. **M**ultimode = **M**ehre Moden = **M**ax. 550 m.

## Multimode-Klassen (OM)

| Klasse | Kerndurchmesser | Wellenlänge | Max. 1G | Max. 10G | Max. 100G | Farbe |
|---|---|---|---|---|---|---|
| OM1 | 62,5 µm | 850 nm | 275 m | 33 m | – | orange |
| OM2 | 50 µm | 850 nm | 550 m | 82 m | – | orange |
| **OM3** | 50 µm | 850 nm | 1000 m | **300 m** | 100 m | **aqua** |
| **OM4** | 50 µm | 850 nm | 1000 m | **550 m** | 150 m | **aqua** |
| OM5 | 50 µm | 850–953 nm | 1000 m | 550 m | 150 m+ | lila |

> [!important] **Kernregel**
> Für neue Installationen: mindestens **OM3** verwenden. OM1/OM2 sind für 10G kaum geeignet.

## Singlemode-Klassen (OS)

| Klasse | Beschreibung | Anwendung |
|---|---|---|
| OS1 | Tight-buffered, Innenbereich | gebäudeinterner SM |
| **OS2** | Low-water-peak, Außenbereich | Weitstrecke, Außeninstallation |

## Steckverbinder

| Stecker | Merkmal | Einsatz |
|---|---|---|
| **SC** (Subscriber Connector) | quadratisch, Push-Pull, Duplex-Clip | ältere Installationen, FTTH |
| **LC** (Lucent Connector) | klein, Push-Pull, Standard heute | Switches, Transceiver, SFP |
| **ST** | Bajonett-Drehverschluss, rund | veraltet (Hubs, alte Switches) |
| **MPO/MTP** | 8/12/24 Fasern in einem Stecker | High-Density RZ, 40G/100G |
| **E2000** | mit Schutzklappe | SM-Installationen in DE |

> [!tip] **Merksatz**
> **LC** = klein und aktuell. **SC** = groß und quadratisch. **ST** = rund mit Bajonett (alt).

## Polieren & Schliff-Typen

| Schliff | Rückreflektion | Beschreibung |
|---|---|---|
| **PC** (Physical Contact) | -40 dB | leicht gewölbt, Standard |
| **UPC** (Ultra PC) | -55 dB | stärker poliert, weniger Reflexion |
| **APC** (Angled PC) | **-65 dB** | 8°-Winkelschliff, **grüne Farbe**, SM-Standard |

> [!warning] **Achtung Falle**
> APC (grün) und UPC (blau) Stecker **nicht** direkt zusammenstecken – 8°-Winkelunterschied erzeugt hohe Dämpfung und kann Geräte beschädigen.

## Kabelaufbau

| Typ | Aufbau | Einsatz |
|---|---|---|
| **Simplex** | 1 Faser | unidirektional |
| **Duplex** | 2 Fasern | bidirektional (TX + RX) |
| **Breakout** | Fasern einzeln in Röhrchen | direkte Konfektionierung |
| **Loose Tube** | Fasern lose in Gel-Röhrchen | Außeninstallation, Schutz |
| **Tight Buffered** | Faser direkt gepuffert | Inneninstallation |
| **Armored** | Stahlpanzerung | Nagetier-Schutz, Erdverlegung |

## Bidirektionales LWL (BiDi / WDM)

| Technik | Beschreibung |
|---|---|
| **BiDi SFP** | TX und RX auf **einer** Faser über verschiedene Wellenlängen (1310/1490 nm) |
| **CWDM** | Coarse WDM – bis zu 18 Kanäle, 20-nm-Raster |
| **DWDM** | Dense WDM – bis zu 80+ Kanäle, 0,8-nm-Raster, sehr hohe Kapazität |

## LWL vs. Kupfer – Wann was?

| Kriterium | Kupfer (Cat6a) | LWL |
|---|---|---|
| Distanz < 100 m | ✅ ideal | ✅ möglich aber überdimensioniert |
| Distanz > 100 m | ❌ | ✅ |
| Kosten Kabel | günstig | teurer |
| Kosten Transceiver | entfällt (RJ45) | SFP erforderlich |
| EMV-Probleme | möglich | ❌ immune |
| Brandlast | höher | sehr gering |
| Gebäude-Backbone | selten | ✅ Standard |
