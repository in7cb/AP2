[[Netzwerkmanagement|zurück]]

---

# Logging & Remote Syslog Server

Logging erfasst Systemereignisse zeitgestempelt in Logdateien oder sendet sie zentral an einen Syslog-Server – unverzichtbar für Monitoring, Fehlerdiagnose und Security-Auditing.

## Syslog-Grundlagen

Syslog ist das standardisierte Protokoll für Systemmeldungen (RFC 5424). Geräte (Router, Switches, Server) senden Logs an einen zentralen **Syslog-Server** (z.B. rsyslog, syslog-ng, Graylog).

- **Transport:** UDP 514 (klassisch, kein ACK) oder TCP 514 / TLS 6514 (sicher, zuverlässig)
- **Format:** `<Priorität> Timestamp Hostname Prozess[PID]: Nachricht`

## Severity Levels (RFC 5424)

| Level | Name | Bedeutung | Beispiel |
|-------|------|-----------|---------|
| 0 | Emergency | System nicht nutzbar | Kernel Panic |
| 1 | Alert | Sofortiger Handlungsbedarf | RAID-Ausfall |
| 2 | Critical | Kritischer Fehler | HW-Fehler |
| 3 | Error | Fehlerzustand | Interface down |
| 4 | Warning | Warnung | Speicher > 90% |
| 5 | Notice | Normales, aber relevantes Ereignis | Neustart |
| 6 | Informational | Informationsmeldung | Login erfolgreich |
| 7 | Debug | Debug-Ausgaben | Packet trace |

> [!tip] **Merksatz**
> **E**very **A**dmin **C**ares **E**normously **W**hen **N**etworks **I**gnite **D**own → Emergency, Alert, Critical, Error, Warning, Notice, Informational, Debug (0–7)

## Facility-Codes

Klassifizierung der Quelle einer Meldung (z.B. `kern`, `auth`, `daemon`, `local0`–`local7`). Letztere werden für Netzwerkgeräte wie Router/Switches genutzt.

**Priorität** = Facility × 8 + Severity → wird als `<PRI>` ins Paket kodiert.

## Zentrales Logging (Remote Syslog)

```text
[Router/Switch] --UDP 514--> [Syslog-Server] --> [Logdatei / SIEM]
```

Vorteile zentraler Logs:
- Korrelation von Ereignissen über mehrere Geräte
- Manipulationssicherheit (Logs nicht lokal auf dem kompromittierten Gerät)
- Alerting & Dashboards (Graylog, ELK Stack, Splunk)

**Cisco IOS Konfiguration:**
```bash
logging host 192.168.1.100
logging trap warnings        # Severity 4 und kritischer senden
logging facility local6
logging on
```

## Log-Rotation

Lokale Logdateien wachsen unbegrenzt → `logrotate` (Linux) rotiert automatisch:
- tägliche/wöchentliche Rotation
- komprimieren & archivieren (`.gz`)
- maximale Anzahl Versionen behalten

> [!warning] **Achtung Falle**
> UDP-Syslog liefert keine Bestätigung – bei Netzwerkproblemen gehen Logs verloren. Für sicherheitsrelevante Logs immer **TCP oder TLS** nutzen.

> [!important] **Kernregel**
> Severity 0–4 = aktiv reagieren, 5–6 = aufzeichnen, 7 = nur im Debugging aktivieren.
