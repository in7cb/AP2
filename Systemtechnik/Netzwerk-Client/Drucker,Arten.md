Drucker unterscheiden sich fundamental in Drucktechnologie, Betriebskosten und Einsatzgebiet. Für die Prüfung relevant sind vor allem Laser, Tintenstrahl und Nadel.

## Druckertypen im Vergleich

| Typ | Funktionsprinzip | Stärken | Schwächen |
|---|---|---|---|
| **Laserdrucker** | Toner auf Trommel, Hitzefixierung | Schnell, günstig/Seite, trocken | Hoher Anschaffungspreis, warm-up |
| **Tintenstrahldrucker** | Tinte durch Düsen auf Papier | Farbtreu, günstig in Anschaffung, Foto | Tinte teuer, Düsen verkleben |
| **Nadeldrucker** | Nadeln schlagen Farbband auf Papier | Durchschlagpapier, robust, günstig | Laut, niedrige Auflösung |
| **Thermodrucker** | Wärme aktiviert beschichtetes Papier | Wartungsarm (kein Verbrauchsmaterial) | Spezialrollen, verblasst UV-Licht |
| **Tintentank** | Tinte aus Nachfülltank | Niedrige Seitenkosten | Langsamer als Laser |
| **3D-Drucker** | FDM/SLA (Schicht für Schicht) | Prototypen, individuell | Nicht Netzwerkdrucker-relevant |

## Laserdrucker – Funktionsprinzip (6 Schritte)

```text
1. Laden    → Trommel wird gleichmäßig negativ geladen
2. Belichten → Laser entlädt Stellen entsprechend Druckbild
3. Entwickeln → Toner (positiv geladen) haftet an entladenen Stellen
4. Übertragen → Toner wird auf Papier übertragen
5. Fixieren  → Heizwalze schmilzt Toner dauerhaft ein
6. Reinigen  → Resttoner von Trommel entfernt
```

> [!tip] **Merksatz LBLÜFR**
> **L**aden → **B**elichten → **L**asern → **Ü**bertragen → **F**ixieren → **R**einigen

## Netzwerkdrucker & Druckserver

**Netzwerkdrucker** haben eine eigene NIC und IP-Adresse, sind direkt ins Netzwerk eingebunden.

**Druckserver** (Printserver) vermittelt zwischen Clients und einem oder mehreren Druckern:
- Spooling: Druckaufträge werden in Warteschlange gepuffert
- Zentrale Verwaltung von Treibern
- Kann als Dienst (Windows Print Spooler) oder dediziertes Gerät laufen

```text
Client → Druckserver (Spooler) → Drucker
         [Treiber + Warteschlange]
```

### Druckprotokolle & Ports

| Protokoll | Port | Beschreibung |
|---|---|---|
| LPD/LPR | TCP 515 | Unix-Druckprotokoll, legacy |
| IPP | TCP 631 | Internet Printing Protocol, modern |
| RAW / JetDirect | TCP 9100 | HP-Standard, direkte Datenübertragung |
| SMB | TCP 445 | Windows-Netzwerkdrucker (Freigabe) |

## Seitensprachen

| Sprache | Hersteller | Beschreibung |
|---|---|---|
| **PCL** (Printer Command Language) | HP | Weit verbreitet, einfach, weniger präzise |
| **PostScript** | Adobe | Vektorbasiert, druckerunabhängig, teurer |
| **PDF** | Adobe | Direktdruck moderner Drucker |
| **GDI/XPS** | Microsoft | Windows-native Darstellung |

> [!important] **Kernregel**
> PostScript = qualitativ besser, druckerunabhängig → Profi-/Verlagsdruck.  
> PCL = effizienter, weiter verbreitet im Büroumfeld.

## Drucker-Kenngrößen

| Kennzahl | Einheit | Bedeutung |
|---|---|---|
| ppm | Seiten/Minute | Druckgeschwindigkeit |
| dpi | Punkte/Zoll | Druckauflösung |
| Druckkosten | Cent/Seite | Toner/Tintenkosten |
| Erstdruckzeit | Sekunden | Zeit bis erste Seite aus Standby |
| Duty Cycle | Seiten/Monat | Maximale monatliche Drucklast |

> [!warning] **Achtung Falle**
> Drucker-Treiber müssen zum **Betriebssystem** passen (32/64-Bit, Windows/Linux). Bei Netzwerkdruckern über Druckserver werden Treiber oft zentral verteilt (GPO unter Windows).
