[[Netzwerkmanagement|zurück]]

---

# SNMP – Simple Network Management Protocol

SNMP ist das Standardprotokoll zur **Überwachung und Steuerung** von Netzwerkgeräten (Router, Switches, Server, Drucker) – läuft über **UDP 161** (Polling) und **UDP 162** (Traps).

## Architektur

```text
[Manager / NMS]  <---UDP 161--->  [Agent auf Gerät]
                 <---UDP 162---   [Trap bei Event]
                                       |
                                    [MIB]
```

- **Manager (NMS):** Netzwerkmanagementsystem (z.B. Zabbix, PRTG, LibreNMS) – stellt Anfragen, wertet aus
- **Agent:** Läuft auf dem Gerät, antwortet auf Anfragen, sendet Traps
- **MIB (Management Information Base):** Strukturierte Datenbank aller verwaltbaren Objekte – als Baum (OID-Baum) organisiert
- **OID (Object Identifier):** Eindeutige Adresse eines Datenpunkts im MIB-Baum, z.B. `1.3.6.1.2.1.1.1.0` = sysDescr

## SNMP-Operationen

| Operation | Richtung | Beschreibung |
|-----------|----------|-------------|
| GET | Manager → Agent | Einzelnen Wert abfragen |
| GET-NEXT | Manager → Agent | Nächsten Wert im MIB-Baum |
| GET-BULK | Manager → Agent | Mehrere Werte auf einmal (SNMPv2c+) |
| SET | Manager → Agent | Wert setzen (Konfiguration) |
| TRAP | Agent → Manager | Unaufgefordertes Event-Meldung (kein ACK) |
| INFORM | Agent → Manager | Trap mit Bestätigung (SNMPv2c+) |

## Versionen im Vergleich

| Feature | SNMPv1 | SNMPv2c | SNMPv3 |
|---------|--------|---------|--------|
| Authentifizierung | Community String | Community String | Benutzername + Passwort |
| Verschlüsselung | ❌ | ❌ | ✅ (AES/DES) |
| GetBulk | ❌ | ✅ | ✅ |
| Inform | ❌ | ✅ | ✅ |
| Sicherheit | sehr schwach | schwach | stark |
| Praxiseinsatz | Legacy | weit verbreitet | empfohlen |

> [!warning] **Achtung Falle**
> SNMPv1 und v2c senden den **Community String im Klartext** – bei Netzwerk-Sniffing komplett auslesbar. Im Produktivbetrieb immer SNMPv3 mit Auth + Privacy.

## Community Strings (v1/v2c)

Funktionieren wie ein shared Passwort:
- `public` → read-only (Standard, sollte geändert werden!)
- `private` → read-write

> [!important] **Kernregel**
> Default Community Strings (`public`/`private`) immer ändern – sie sind allgemein bekannt und ein häufiges Angriffsziel.

## SNMPv3 Security-Modelle

- **noAuthNoPriv:** kein Passwort, keine Verschlüsselung (wie v2c)
- **authNoPriv:** MD5/SHA Authentifizierung, kein Encrypt
- **authPriv:** Auth + AES/DES Verschlüsselung → produktiv nutzen

## MIB & OID-Baum (Ausschnitt)

```text
iso(1)
 └─ org(3)
     └─ dod(6)
         └─ internet(1)
             └─ mgmt(2)
                 └─ mib-2(1)
                     ├─ system(1)      → Gerätebeschreibung
                     ├─ interfaces(2)  → Interface-Statistiken
                     └─ ip(4)          → IP-Statistiken
```

Hersteller pflegen eigene MIBs unter `enterprises(1.3.6.1.4.1)`.

> [!tip] **Merksatz**
> SNMP: **S**ammle **N**etz-**M**etadaten **P**eriodisch – Manager pollt Agent via UDP 161, Alerts kommen als Trap via UDP 162.
