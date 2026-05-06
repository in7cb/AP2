PoE (Power over Ethernet) ermöglicht die Stromversorgung von Netzwerkgeräten über das Ethernet-Kabel – kein separates Netzteil nötig. Standardisiert in IEEE 802.3af, 802.3at und 802.3bt.

## Standards im Vergleich

| Standard | Name | Max. Leistung (Port) | Adernpaare | Typische Anwendung |
|---|---|---|---|---|
| **IEEE 802.3af** | PoE | **15,4 W** (12,95 W beim Gerät) | 2 Paare | IP-Telefone, einfache APs |
| **IEEE 802.3at** | PoE+ | **30 W** (25,5 W beim Gerät) | 2 Paare | Video-APs, PTZ-Kameras |
| **IEEE 802.3bt (Typ 3)** | PoE++ / 4PPoE | **60 W** (51 W beim Gerät) | 4 Paare | Thin Clients, APs Wi-Fi 6 |
| **IEEE 802.3bt (Typ 4)** | PoE++ | **100 W** (71,3 W beim Gerät) | 4 Paare | Laptops, Monitore |

> [!tip] **Merksatz**
> af = 15 W, at = 30 W, bt = 60/100 W. Jede Generation verdoppelt (ungefähr) die Leistung.

## Funktionsprinzip

### Erkennung (Detection)

Der PSE (Power Sourcing Equipment = Switch) erkennt ob das angeschlossene Gerät (PD = Powered Device) PoE unterstützt:

```
1. PSE legt kleine Spannung an (2,7–10 V)
2. PD antwortet mit 25-kΩ-Widerstand (Erkennungssignatur)
3. PSE erkennt PoE-fähiges Gerät
4. PSE klassifiziert Leistungsbedarf (Klasse 0–8)
5. PSE schaltet Versorgungsspannung (44–57 V DC) frei
```

### Klassifizierung (Classification)

| Klasse | Standard | Max. Leistung PD |
|---|---|---|
| 0 | 802.3af | 12,95 W (default) |
| 1 | 802.3af | 3,84 W |
| 2 | 802.3af | 6,49 W |
| 3 | 802.3af | 12,95 W |
| 4 | 802.3at | 25,5 W |
| 5–8 | 802.3bt | 40–71,3 W |

### Stromübertragung

| Modus | Adernpaare | Beschreibung |
|---|---|---|
| Alternative A | Paare 1,2 + 3,6 | Strom auf Datenleitungen (Phantom Power) |
| Alternative B | Paare 4,5 + 7,8 | Strom auf ungenutzten Paaren (bei 10/100) |
| 4-Pair (802.3bt) | alle 4 Paare | Für hohe Leistungen nötig |

## Typische Anwendungen

| Gerät | Typische Leistungsaufnahme | PoE-Standard |
|---|---|---|
| IP-Telefon | 3–6 W | 802.3af |
| IP-Kamera (einfach) | 5–10 W | 802.3af |
| WLAN Access Point (Wi-Fi 5) | 12–15 W | 802.3af/at |
| WLAN Access Point (Wi-Fi 6) | 20–25 W | 802.3at |
| PTZ-Kamera | 25–30 W | 802.3at |
| Thin Client | 45–60 W | 802.3bt |
| Laptop | 60–100 W | 802.3bt Typ 4 |

## PSE – Power Sourcing Equipment

- **PoE-Switch:** Direkt integrierte PoE-Versorgung (intern budgetiert, z. B. 370 W gesamt)
- **PoE-Injector (Midspan):** Fügt PoE auf einem bestimmten Port eines Non-PoE-Switches hinzu
- **PoE-Splitter:** Trennt Strom und Daten am Ende – für Geräte ohne PoE-Support

```
Switch (PoE) ──────────────────────────── IP-Telefon
Switch (kein PoE) ──── Injector ────────── IP-Kamera
Switch (kein PoE) ──── Splitter ── Strom → älteres Gerät
                                 └─ Daten ─┘
```

## PoE Budget

Das **PoE-Budget** eines Switches ist die maximale Gesamtleistung für alle PoE-Ports zusammen:

```
Beispiel:
Switch: 24x PoE+ (30 W/Port), Budget: 370 W
→ maximal ~12 Ports gleichzeitig mit 30 W vollständig laden
```

> [!warning] **Achtung Falle**
> PoE-Budget ≠ Ports × max. Leistung/Port. Echtes Budget immer im Datenblatt prüfen – sonst Überlast → Ports werden abgeregelt.

## Kabelbeschränkungen bei PoE

- Länge: Standard 100 m (wie Ethernet) gilt auch für PoE
- Höhere Leistung (bt) → mehr Wärmeverlust im Kabel → **Cat6 oder besser empfohlen**
- Dünne Kabel (AWG 26) erhöhen Widerstand → größere Verluste
