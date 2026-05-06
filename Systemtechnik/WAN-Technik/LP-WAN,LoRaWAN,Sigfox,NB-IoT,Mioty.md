**LP-WAN** (Low-Power Wide Area Network) sind Funknetzwerke, die auf maximale Reichweite und minimalen Energieverbrauch optimiert sind – zu Lasten von Datenrate und Latenz. Typisch für IoT-Anwendungen mit Batterie-Sensoren.

## Gemeinsame Eigenschaften aller LP-WAN-Technologien

- **Geringe Datenrate:** wenige Byte bis max. einige kBit/s
- **Lange Batterielaufzeit:** Monate bis Jahre
- **Weite Reichweite:** 2–15 km urban, bis 50 km ländlich (LoS)
- **Lizenzfreies oder lizenziertes Sub-GHz-Band:** weniger Absorption, bessere Ausbreitung
- **Geringe Kosten** für Endgeräte und Betrieb

## Technologie-Vergleich

| Merkmal | LoRaWAN | Sigfox | NB-IoT | Mioty |
|---|---|---|---|---|
| Frequenz | 868 MHz (EU) | 868 MHz (EU) | LTE-Bänder | 868 MHz (EU) |
| Lizenz | frei (ISM) | frei (ISM) | lizenziert | frei (ISM) |
| Reichweite | 5–15 km | 10–50 km | 1–10 km | 5–15 km |
| DL-Rate | 0,3–50 kbit/s | 100 bit/s | 200 kbit/s | 100 kbit/s |
| UL-Rate | 0,3–50 kbit/s | 100 bit/s | 200 kbit/s | 100 kbit/s |
| Nachrichten/Tag | unbegrenzt | 140 UL / 4 DL | unbegrenzt | unbegrenzt |
| Betreiber | Community/eigen | Sigfox (proprietär) | Mobilfunkanbieter | Community/eigen |
| Standard | offener Standard | proprietär | 3GPP (Rel. 13+) | offener Standard (ETSI) |
| Topologie | Stern (Gateway) | Stern (BS) | Stern (eNB) | Stern (AP) |

## LoRaWAN

**LoRa** = Modulationsverfahren (Chirp Spread Spectrum, proprietär von Semtech).
**LoRaWAN** = das Netzwerkprotokoll darüber (offener Standard, betrieben von der LoRa Alliance).

### Spreading Factor (SF)

Der SF steuert den Trade-off zwischen Reichweite und Datenrate:

| SF | Datenrate | Reichweite | Airtime |
|---|---|---|---|
| SF7 | ~5,5 kbit/s | gering | kurz |
| SF9 | ~1,8 kbit/s | mittel | mittel |
| SF12 | ~250 bit/s | max. | sehr lang |

> [!important] **Kernregel**
> Höherer SF = mehr Reichweite, aber weniger Datenrate und längere Sendedauer (→ mehr Energieverbrauch und mehr Duty-Cycle-Belastung).

### Geräteklassen

| Klasse | Empfangsfenster | Energieverbrauch | Anwendung |
|---|---|---|---|
| Class A | Nur nach eigenem Uplink | minimal | Standard (Sensoren) |
| Class B | Geplante Empfangsfenster | mittel | Aktoren mit Zeitplan |
| Class C | Dauerhaft empfangsbereit | hoch | permanent versorgte Geräte |

### Adressierung und Aktivierung

**OTAA** (Over-the-Air Activation): Gerät joined das Netz dynamisch, erhält DevAddr, Session-Keys.
**ABP** (Activation by Personalization): Keys werden fest einprogrammiert – unsicherer, aber einfacher.

```text
OTAA Join-Prozess:
1. Gerät sendet Join-Request (DevEUI, AppEUI, AppKey)
2. Network Server antwortet mit Join-Accept
3. Session-Keys (NwkSKey, AppSKey) werden abgeleitet
```

## Sigfox

Vollständig proprietäres, betreibergeführtes Netz. Ultraschmalbandige Übertragung (100 Hz Bandbreite) erlaubt extreme Reichweiten, aber stark beschränkte Nachrichtenanzahl.

- Keine eigene Infrastruktur nötig → Roaming über Sigfox-Netz
- 12 Byte Payload pro Nachricht (UL), 8 Byte (DL)
- Nachrichten werden an 3 verschiedene Basisstationen gesendet (Diversity)

> [!warning] **Achtung Falle**
> Sigfox: nur **140 Uplink-Nachrichten pro Tag** erlaubt (technische + regulatorische Grenze). DL ist extrem limitiert – kein Firmware-Update over-the-air möglich.

## NB-IoT (Narrowband IoT)

NB-IoT ist ein **3GPP-Standard**, der im bestehenden LTE-Frequenzband läuft – betrieben von Mobilfunkanbietern. Bietet höhere Zuverlässigkeit und Downlink-Kapazität als LoRaWAN/Sigfox.

**Deployment-Modi:**
- **In-Band:** innerhalb eines LTE-Resource-Blocks
- **Guard-Band:** im Schutzband zwischen LTE-Kanälen
- **Stand-alone:** auf eigenem Spektrum (z.B. GSM-Refarming)

**PSM (Power Saving Mode) & eDRX:** Ermöglichen lange Schlafzeiten zwischen Übertragungen → Batterielaufzeit Jahre.

## Mioty

Mioty (ETSI TS 103 357) nutzt **Telegram Splitting** – eine Nachricht wird in viele kleine Sub-Pakete aufgeteilt und über Zeit und Frequenz verteilt gesendet. Dadurch sehr robust gegen Interferenzen.

- Entwickelt von Fraunhofer IIS
- Besonders geeignet für dichte Deployments (viele Geräte, hohe Interferenz)
- Offener ETSI-Standard, wird als "LoRaWAN-Alternative made in Germany" vermarktet

> [!tip] **Merksatz**
> **Lo**RaWAN = **Lo**ng Range + offener Standard + flexibel. **NB-IoT** = Mobilfunknetz + zuverlässig + teurer. **Sigfox** = einfach + proprietär + limitiert. **Mioty** = interferenzresistent + Telegram Splitting.
