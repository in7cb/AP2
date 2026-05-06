Twisted-Pair-Kabel (TP) sind das dominierende Medium für LAN-Verkabelung. Sie bestehen aus verdrillten Adernpaaren, die Störungen durch Gegentaktsignale (Differentielle Übertragung) reduzieren.

## Warum verdrillt?

Durch die Verdrillung heben sich elektromagnetische Interferenzen (EMI) gegenseitig auf – jede Halbwindung hat umgekehrte Orientierung zum Störfeld. Engere Verdrillung = bessere Störunterdrückung.

## Schirmungsaufbau (Nomenklatur nach IEC 61156)

Format: **XX/YZZ**
- **XX:** Gesamtschirm (U = unshielded, F = Folie, SF = Geflecht + Folie, S = Geflecht)
- **Y:** Adernpaarschirm (U = keiner, F = Folie, S = Geflecht)
- **ZZ:** TP = Twisted Pair

| Bezeichnung | Gesamtschirm | Adernpaarschirm | Volksmund |
|---|---|---|---|
| **U/UTP** | keiner | keiner | UTP (Unshielded) |
| **F/UTP** | Folie | keiner | FTP |
| **U/FTP** | keiner | Folie pro Paar | STP (misleading) |
| **F/FTP** | Folie | Folie pro Paar | SSTP / SFTP |
| **S/FTP** | Geflecht | Folie pro Paar | SSTP / SFTP |
| **SF/FTP** | Geflecht + Folie | Folie pro Paar | S/STP |

> [!tip] **Merksatz**
> U = Unshielded (kein Schirm), F = Folie (Alu-Folie), S = Schirm (Geflecht). Vor dem Schrägstrich: Gesamtschirm. Nach dem Schrägstrich: Adernpaarschirm.

> [!warning] **Achtung Falle**
> „STP" und „FTP" werden im Volksmund uneinheitlich verwendet. In der Norm ist die korrekte Schreibweise U/FTP oder F/UTP. Im Prüfungskontext auf den Kontext achten.

## Kategorien (Cat)

| Kategorie | Max. Frequenz | Max. Datenrate | Schirmung | Typischer Einsatz |
|---|---|---|---|---|
| Cat5 | 100 MHz | 1 Gbit/s (begrenzt) | U/UTP | veraltet |
| **Cat5e** | 100 MHz | **1 Gbit/s** | U/UTP | Standard Access-Layer |
| **Cat6** | 250 MHz | **10 Gbit/s** (~55 m) | U/UTP oder F/UTP | Büro, kurze 10G-Strecken |
| **Cat6a** | 500 MHz | **10 Gbit/s** (100 m) | U/FTP oder F/FTP | 10G über volle Distanz |
| Cat7 | 600 MHz | 10 Gbit/s | S/FTP | proprietärer Stecker (GG45/TERA) |
| Cat7a | 1000 MHz | 40 Gbit/s | S/FTP | Rechenzentrum |
| Cat8 | 2000 MHz | 40 Gbit/s | S/FTP | RZ, max. 30 m |

> [!important] **Kernregel**
> Für 10GBase-T über 100 m: **mindestens Cat6a** verwenden. Cat6 schafft 10G nur bis ~55 m.

## Adernbelegung – T568A vs. T568B

```
T568B (in DE am häufigsten):
Pin: 1       2       3       4       5       6       7       8
     or/w    or      grün/w  blau    blau/w  grün    braun/w braun
     (TX+)  (TX-)   (RX+)          (      (RX-)
```

| Standard | Farbreihenfolge (Pin 1–8) |
|---|---|
| **T568A** | grün/w, grün, or/w, blau, blau/w, or, braun/w, braun |
| **T568B** | or/w, or, grün/w, blau, blau/w, grün, braun/w, braun |

- **Straight-Through:** beide Seiten gleiche Belegung (PC → Switch)
- **Crossover:** eine Seite T568A, andere T568B (Switch → Switch, früher)
- **Heute:** Auto-MDI/MDIX macht Crossover-Kabel überflüssig

## Steckverbinder

| Stecker | Norm | Pins | Verwendung |
|---|---|---|---|
| **RJ45** | IEC 60603-7 | 8P8C | Standard Ethernet-Stecker |
| **GG45** | IEC 61076-3-110 | abwärtskompatibel RJ45 | Cat7 (selten) |
| **TERA** | proprietär Siemon | – | Cat7 (selten) |

**RJ45-Varianten:**
- Standard: 8P8C (8 Positions, 8 Contacts)
- Feldkonfektionierbar vs. vorkonf. Patchkabel
- **Keystone-Jack:** Dose/Patchfeld-Buchse – Kabel wird eingeschnitten (110-Punch-Down)

## Adernaufbau

| Aufbau | Beschreibung | Verwendung |
|---|---|---|
| **Massiv (solid)** | Einzeldraht pro Ader | Festinstallation (Trasse) |
| **Litze (stranded)** | Mehrere dünne Drähte pro Ader | Patch-Kabel (flexibel) |

> [!warning] **Achtung Falle**
> Massivdraht nicht für Patchkabel verwenden – bricht bei häufigem Biegen. Litzenkabel nicht als Festinstallation – höhere Dämpfung, nicht normkonform.

## Außendurchmesser & Kanalkapazität

Cat6a ist durch den Schirm deutlich dicker als Cat5e/6 → weniger Kabel passen in Kabelkanäle und Rohre. Bei Planung Platzbedarf beachten.
