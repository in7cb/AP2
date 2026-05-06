Netzwerkgeräte können über verschiedene Wege verwaltet werden – entscheidend ist, ob der Management-Kanal **dasselbe Netz nutzt** wie der Produktivtraffic oder ein **separates Netz**.

## In-Band vs. Out-of-Band

| | In-Band Management | Out-of-Band Management |
|-|-------------------|----------------------|
| **Kanal** | Produktionsnetzwerk | Separates Managementnetz / physische Konsole |
| **Verfügbarkeit** | Abhängig vom Produktionsnetz | Unabhängig (auch bei Netzausfall nutzbar) |
| **Protokolle** | SSH, Telnet, HTTPS, SNMP | Konsolenkabel (RS-232), IPMI, iDRAC, iLO |
| **Sicherheit** | schlechter (shared medium) | besser (isoliert) |
| **Aufwand** | gering | höher (dedizierte Infrastruktur) |

> [!important] **Kernregel**
> Out-of-Band ist der **Rettungsanker** – wenn das Produktionsnetz tot ist, kommst du trotzdem ans Gerät. Kritische Infrastruktur immer mit OOB absichern.

## Management-Interfaces

### CLI (Command Line Interface)

Textbasierte Konfiguration direkt auf dem Gerät – der präziseste und mächtigste Weg.

**Zugang via:**
- **Konsolenkabel** (RS-232 / RJ45-auf-DB9) → direkt, out-of-band, kein Netz nötig
- **SSH** (Port 22) → in-band, verschlüsselt ✅
- **Telnet** (Port 23) → in-band, **unverschlüsselt** ❌ (nur Legacy/Lab)

```bash
# SSH-Verbindung zu Cisco-Switch
ssh admin@192.168.1.1

# Typische Cisco IOS CLI-Ebenen:
> User Mode         (show-Befehle)
# Privileged Mode   (enable)
(config)# Global Config Mode
(config-if)# Interface Config Mode
```

### WBM (Web-Based Management / Weboberfläche)

Browser-basierte GUI – komfortabler als CLI, aber weniger mächtig.

- **HTTP** → unverschlüsselt ❌ (nur isolierte Netze)
- **HTTPS** (Port 443) → verschlüsselt ✅
- Typisch für: Consumer-Router, managed Switches, Firewalls (Webfrontend)
- Einschränkungen: oft nicht alle Features verfügbar, langsamer bei Bulk-Config

### Console-Port (Konsolenzugang)

Physischer Direktzugang – out-of-band, unabhängig vom Netzwerk.

- **Verbindungsart:** RS-232 seriell (klassisch) oder USB-to-Console
- **Emulator:** PuTTY, minicom, screen
- **Einstellungen:** 9600 Baud, 8N1, kein Flow Control (Cisco-Standard)
- Genutzt bei: Erstinbetriebnahme, Passwort-Recovery, Netzwerkausfall

```bash
# Linux: serielle Konsolenverbindung
screen /dev/ttyUSB0 9600
# oder
minicom -D /dev/ttyUSB0 -b 9600
```

## Dediziertes Managementnetz (OOB-Infrastruktur)

```text
[Geräte Prod]    [Prod-Netz]
     |
[Mgmt-Port]  ---[OOB-Switch]---[Mgmt-Server / Jump Host]
                                        |
                              [Admin-PC / VPN]
```

Typische OOB-Komponenten:
- **Konsolen-Server (Terminal Server):** aggregiert viele seriell Verbindungen → SSH-erreichbar
- **IPMI / iDRAC / iLO:** Remote-KVM und Power-Management für Server (Board Management Controller)
- **Out-of-Band LTE:** Mobilfunk-Router als Backup-Mgmt

> [!tip] **Merksatz**
> **In-Band** = durch die Leitung, **Out-of-Band** = um die Leitung herum – bei Leitungsausfall rettet nur OOB.

> [!warning] **Achtung Falle**
> Telnet und HTTP im Produktionsnetz sind **absolutes No-Go** – Credentials und Konfiguration laufen im Klartext. Immer SSH + HTTPS.
