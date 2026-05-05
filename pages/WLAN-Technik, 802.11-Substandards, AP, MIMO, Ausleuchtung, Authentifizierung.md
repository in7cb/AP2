[[L2-Technologie|zurück]]

---

# WLAN-Technik

WLAN (Wireless LAN) nach IEEE 802.11 ermöglicht kabellose Netzwerkkommunikation im 2,4-GHz-, 5-GHz- und 6-GHz-Band.

## 802.11 Substandards im Überblick

| Standard | Frequenz | Max. Brutto-Rate | MIMO | Bezeichnung |
|---|---|---|---|---|
| 802.11b | 2,4 GHz | 11 Mbit/s | – | Wi-Fi 1 |
| 802.11a | 5 GHz | 54 Mbit/s | – | Wi-Fi 2 |
| 802.11g | 2,4 GHz | 54 Mbit/s | – | Wi-Fi 3 |
| **802.11n** | 2,4 + 5 GHz | 600 Mbit/s | 4×4 | **Wi-Fi 4** |
| **802.11ac** | 5 GHz | 6,9 Gbit/s | 8×8, MU-MIMO | **Wi-Fi 5** |
| **802.11ax** | 2,4 + 5 + 6 GHz | 9,6 Gbit/s | MU-MIMO, OFDMA | **Wi-Fi 6/6E** |
| 802.11be | 2,4 + 5 + 6 GHz | 46 Gbit/s | MU-MIMO | Wi-Fi 7 |

> [!tip] **Merksatz**
> n=4, ac=5, ax=6 → Wi-Fi-Generation = letztes Buchstabe im Alphabet minus offset. Einfacher: n→4, ac→5, ax→6.

## Frequenzbänder & Kanäle

### 2,4-GHz-Band

- 13 Kanäle (DE), je 22 MHz breit bei 5 MHz Abstand → massive **Überlappung**
- Nur **3 nicht-überlappende Kanäle:** 1, 6, 11
- Störquellen: Mikrowelle, Bluetooth, Zigbee, Nachbarn

### 5-GHz-Band

- Mehr nicht-überlappende Kanäle (bis zu 19 in DE)
- Kanalbreiten: 20, 40, 80, 160 MHz
- Kürzere Reichweite als 2,4 GHz (höhere Frequenz = stärkere Dämpfung)
- **DFS (Dynamic Frequency Selection):** Pflicht für Kanäle 52–144 (Radarvermeidung)

### 6-GHz-Band (Wi-Fi 6E)

- Nur für Wi-Fi 6E/7
- Kaum Störquellen (neues Band)
- 59 nicht-überlappende 20-MHz-Kanäle

> [!warning] **Achtung Falle**
> 2,4-GHz-Kanäle überlappen sich stark. Nur Kanäle 1, 6, 11 gleichzeitig verwenden – sonst gegenseitige Störung.

## MIMO & Antennen

| Begriff | Bedeutung |
|---|---|
| **MIMO** | Multiple Input Multiple Output – mehrere Antennen zum Senden und Empfangen |
| **SU-MIMO** | Single User MIMO – ein Endgerät gleichzeitig |
| **MU-MIMO** | Multi User MIMO – mehrere Endgeräte gleichzeitig bedienen (ab Wi-Fi 5 DL, ab Wi-Fi 6 DL+UL) |
| **Spatial Streams** | Gleichzeitige Datenströme; 2×2 MIMO = 2 Streams |
| **Beamforming** | Sendeenergie gezielt auf ein Gerät bündeln |
| **OFDMA** | Orthogonal Frequency Division Multiple Access – Kanal in Resource Units aufteilen (Wi-Fi 6) |

Notation: `4×4:4` = 4 Sende-, 4 Empfangsantennen, 4 Spatial Streams

## Betriebsmodi

| Modus | Beschreibung |
|---|---|
| **Infrastructure** | Client verbindet sich mit Access Point (AP) – Standard |
| **Ad-hoc (IBSS)** | Direkt Gerät-zu-Gerät ohne AP |
| **Monitor-Mode** | Passives Lauschen auf allen Kanälen (für Analyse) |
| **Mesh** | APs verbinden sich untereinander drahtlos |

## Access Point – Varianten

| Typ | Beschreibung |
|---|---|
| **Standalone AP** | Eigenständig konfiguriert, kein Controller nötig |
| **Lightweight AP (LAP)** | Konfiguration zentral über WLAN-Controller (WLC) via CAPWAP-Protokoll |
| **Cloud-Managed AP** | Verwaltung über Cloud-Dashboard (z. B. Meraki, Ubiquiti) |

**CAPWAP:** Control and Provisioning of Wireless Access Points – Tunnel zwischen AP und WLC (UDP 5246/5247)

## Ausleuchtung (Site Survey)

| Begriff | Bedeutung |
|---|---|
| **Site Survey** | Messung der WLAN-Abdeckung und Störquellen vor/nach Installation |
| **Roaming** | Nahtloser Wechsel zwischen APs (selbe SSID, gleiches VLAN) |
| **Overlap** | APs müssen sich überlappen (15–20 % Signalstärke) für lückenloses Roaming |
| **Co-Channel Interference** | Störung durch benachbarte APs auf gleichem Kanal |
| **RSSI** | Received Signal Strength Indicator – Signalstärke in dBm (−50 dBm gut, −80 dBm schwach) |

Faustregel Kanalplanung in Gebäuden:
```
         Kanal 1       Kanal 6       Kanal 11
AP ──────●─────────────●─────────────●
         |← Overlap →|← Overlap →|
```

## Authentifizierung & Sicherheit

| Verfahren | Sicherheit | Beschreibung |
|---|---|---|
| **Open** | ❌ keine | Kein Passwort, kein Schutz |
| **WEP** | ❌ gebrochen | RC4, 40/104-Bit Key – in Sekunden knackbar |
| **WPA (TKIP)** | ⚠️ veraltet | Übergangslösung zu WPA2, auch kompromittiert |
| **WPA2-Personal (PSK)** | ✅ gut | AES-CCMP, Pre-Shared Key – Standard zu Hause/KMU |
| **WPA2-Enterprise** | ✅✅ | 802.1X + RADIUS, individuelle Nutzer-Credentials |
| **WPA3-Personal (SAE)** | ✅✅ | Simultaneous Authentication of Equals – kein PSK-Brute-Force |
| **WPA3-Enterprise** | ✅✅✅ | 192-Bit-Sicherheitsmodus, Perfect Forward Secrecy |
| **Captive Portal** | ⚠️ situativ | Browser-basierte Login-Seite vor Internetzugang |

### Captive Portal

Der Client verbindet sich zunächst **ohne Passwort** (Open WLAN). Bei der ersten HTTP-Anfrage leitet der AP/Controller auf eine **Login-Webseite** um. Erst nach Authentifizierung (Zugangsdaten, AGB-Akzeptanz, Gutscheincode) wird der Internetzugang freigeschaltet.

**Typische Einsatzszenarien:**
- Gast-WLAN mit **zeitbegrenzten Zugangsdaten** (z. B. Zugangscode auf Kassenbon)
- Hotspots (Hotel, Café): AGB-Akzeptanz vor Nutzung
- Wenn Endgeräte kein WPA3 unterstützen und individuelle Credentials nötig sind

> [!warning] **Achtung Falle**
> Captive Portal verschlüsselt den WLAN-Kanal **nicht** – der Datenverkehr ist bis zur HTTPS-Verbindung abfangbar. Kein Ersatz für WPA2/WPA3 in sicherheitskritischen Umgebungen.

> [!important] **Kernregel**
> WPA2-Enterprise = jeder User hat eigene Credentials (RADIUS). WPA2-Personal = alle teilen ein Passwort → bei Austritt muss das Passwort geändert werden. Captive Portal = individuelle Zugangsdaten ohne WLAN-Verschlüsselung, für Gast-Szenarien.

## SSID, BSSID, ESSID

| Begriff | Bedeutung |
|---|---|
| **SSID** | Service Set ID – Netzwerkname (max. 32 Zeichen) |
| **BSSID** | MAC-Adresse des APs (identifiziert einzelnen AP) |
| **ESSID** | Extended SSID – gleiches SSID über mehrere APs hinweg |
| **Hidden SSID** | SSID wird nicht in Beacons gesendet – kein echter Schutz |
