[[Netzwerk-Client|zurück]]

---

# Mobilgeräte

Smartphones und Tablets sind vollwertige Netzwerk-Clients. Im Unternehmenskontext stehen Mobile Device Management (MDM) und Deployment-Modelle im Vordergrund.

## Gerätekategorien

| Typ | Besonderheiten | Typische OS |
|---|---|---|
| Smartphone | Telefonie, Sensoren, immer dabei | iOS, Android |
| Tablet | Größeres Display, oft kein Mobilfunk | iOS/iPadOS, Android |
| Ruggedized Device | Schutzklasse IP67+, Drop-Schutz | Android, Windows |
| Wearable | Smartwatch, Headset | WearOS, watchOS |

## iOS vs. Android

| Kriterium | iOS (Apple) | Android (Google/OEM) |
|---|---|---|
| Ökosystem | Geschlossen, nur App Store | Offen, Google Play + Sideloading |
| Updates | Einheitlich, 5–7 Jahre | Hersteller-abhängig, oft 2–4 Jahre |
| MDM-Integration | DEP (Apple Business Manager) | Android Enterprise / Zero-Touch |
| Sicherheit | Sehr restriktiv (Sandbox) | Offener, mehr Angriffsfläche |
| Verbreitung | ~27% global, >50% DE | ~72% global |

## Konnektivität

| Technologie | Standard | Typische Nutzung |
|---|---|---|
| Wi-Fi | 802.11 a/b/g/n/ac/ax | WLAN (Details: [[WLAN-Technik, 802.11-Substandards, AP, MIMO, Ausleuchtung, Authentifizierung]]) |
| Bluetooth | 5.x | Peripherie (Headset, Tastatur), kurze Distanz |
| NFC | ISO 14443 | Bezahlen (Apple Pay, Google Pay), Zugangskarten |
| LTE (4G) | 3GPP Rel. 8+ | Mobiles Internet, bis ~150 Mbit/s typisch |
| 5G | 3GPP Rel. 15+ | Bis 10 Gbit/s, niedrige Latenz |
| USB / Lightning / USB-C | — | Laden, Datentransfer, Tethering |

## Deployment-Modelle (BYOD, COPE, COBO)

| Modell | Name | Gerät gehört | Nutzung | MDM-Kontrolle |
|---|---|---|---|---|
| **BYOD** | Bring Your Own Device | Mitarbeiter | Privat + Geschäftlich | Eingeschränkt (Container) |
| **COPE** | Corporate Owned, Personally Enabled | Unternehmen | Privat + Geschäftlich | Hoch |
| **COBO** | Corporate Owned, Business Only | Unternehmen | Nur geschäftlich | Vollständig |
| **CYOD** | Choose Your Own Device | Unternehmen | Auswahl durch MA | Hoch |

> [!tip] **Merksatz**
> BYOD = MA bringt eigenes Gerät. COPE = Firma gibt Gerät, MA darf auch privat. COBO = rein geschäftlich.

## MDM – Mobile Device Management

MDM-Systeme (z.B. Microsoft Intune, Jamf, VMware Workspace ONE) ermöglichen zentrale Steuerung aller Mobilgeräte.

### MDM-Funktionen

| Funktion | Beschreibung |
|---|---|
| **Enrollment** | Gerät wird ins MDM aufgenommen (manuell, Zero-Touch, QR-Code) |
| **Profil-Deployment** | WLAN, VPN, Mail, Zertifikate automatisch verteilen |
| **App Management** | Apps verteilen, Updates erzwingen, Apps entfernen |
| **Compliance-Prüfung** | Gerät mit zu altem OS? → Zugriff gesperrt |
| **Remote Wipe** | Gerät aus der Ferne löschen (ganz oder nur Unternehmensbereich) |
| **Geofencing** | Zugriff nur in bestimmten geografischen Bereichen |
| **Inventory** | Hardware/Software-Inventar aller Geräte |

### Work Profile (Android Enterprise)

```
Gerät
├── Privater Bereich (keine MDM-Kontrolle)
└── Work Profile (MDM-verwaltet)
    ├── Unternehmens-Apps
    ├── Verschlüsselter Container
    └── VPN nur für Work-Traffic möglich
```

> [!important] **Kernregel**
> MDM trennt private und geschäftliche Daten auf dem Gerät (Container-Prinzip). Remote Wipe kann auf den Unternehmensbereich beschränkt werden – privat bleibt privat.

> [!warning] **Achtung Falle**
> BYOD ≠ kein MDM. Auch BYOD-Geräte können/sollten per MDM verwaltet werden, aber nur der Unternehmens-Container wird kontrolliert – nicht das gesamte Gerät.

## VPN auf Mobilgeräten

- **Per-App VPN:** Nur bestimmte Apps tunneln durch VPN (z.B. nur Firmen-Apps)
- **Always-On VPN:** Gesamter Traffic geht durch Unternehmens-VPN
- **Split Tunneling:** Nur Firmen-Traffic über VPN, Rest direkt ins Internet
