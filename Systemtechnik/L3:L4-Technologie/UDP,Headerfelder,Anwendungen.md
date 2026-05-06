UDP ist ein verbindungsloses, unzuverlässiges Transportprotokoll auf Layer 4 (RFC 768). Es bietet minimalen Overhead und wird dort eingesetzt, wo Geschwindigkeit wichtiger ist als Zuverlässigkeit.

## UDP Header (8 Byte – fix)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├───────────────────────────────┬───────────────────────────────────┤
│      Source Port (16 Bit)     │   Destination Port (16 Bit)       │
├───────────────────────────────┬───────────────────────────────────┤
│        Length (16 Bit)        │      Checksum (16 Bit)            │
└───────────────────────────────┴───────────────────────────────────┘
```

| Feld | Größe | Beschreibung |
|---|---|---|
| **Source Port** | 16 Bit | Absenderport (0–65535), kann 0 sein wenn keine Antwort erwartet |
| **Destination Port** | 16 Bit | Zielport – identifiziert den Dienst |
| **Length** | 16 Bit | Gesamtlänge UDP-Header + Payload in Byte (min. 8) |
| **Checksum** | 16 Bit | Prüfsumme über Pseudo-Header + UDP-Header + Payload; bei IPv4 optional, bei IPv6 Pflicht |

> [!tip] **Merksatz**
> UDP-Header = **8 Byte, 4 Felder**. Kleiner als ein TCP-Header geht nicht.

## Eigenschaften von UDP

| Eigenschaft | UDP | TCP |
|---|---|---|
| Verbindungsaufbau | ❌ verbindungslos | ✅ 3-Way-Handshake |
| Zuverlässigkeit | ❌ keine Garantie | ✅ ACK, Retransmission |
| Reihenfolge | ❌ nicht garantiert | ✅ Sequenznummern |
| Flusskontrolle | ❌ | ✅ Window |
| Stauvermeidung | ❌ | ✅ Congestion Control |
| Overhead | **8 Byte Header** | 20 Byte Header |
| Latenz | sehr gering | höher |
| Anwendung | Echtzeit, DNS, DHCP | Dateitransfer, HTTP, E-Mail |

## Wann UDP statt TCP?

UDP ist sinnvoll wenn:
- **Latenz kritischer ist als Verlustfreiheit** (VoIP, Gaming, Videostreaming)
- Die Anwendung selbst Fehlerkorrektur macht (QUIC, DTLS)
- Broadcasts oder Multicasts nötig sind (TCP ist Unicast-only)
- Kleine, häufige Anfragen gestellt werden (DNS – ein Request, eine Antwort)

> [!warning] **Achtung Falle**
> UDP bietet **kein** Retransmit. Ein verlorenes DNS-Paket → der Resolver schickt die Anfrage nach Timeout erneut. Die Anwendung, nicht UDP, regelt das.

## Wichtige UDP-Anwendungen

| Protokoll | Port | Beschreibung |
|---|---|---|
| **DNS** | 53/UDP | Namensauflösung (Antworten <512 Byte; größere nutzen TCP) |
| **DHCP** | 67/UDP (Server), 68/UDP (Client) | IP-Adressvergabe |
| **TFTP** | 69/UDP | Trivialer Dateitransfer (z. B. Switch-Boot-Image) |
| **SNMP** | 161/UDP (Agent), 162/UDP (Trap) | Netzwerkmanagement |
| **NTP** | 123/UDP | Zeitsynchronisation |
| **Syslog** | 514/UDP | Log-Nachrichten |
| **RIP** | 520/UDP | Routing-Protokoll |
| **VoIP (RTP)** | dynamisch/UDP | Sprachdaten (Real-time Transport Protocol) |
| **QUIC** | 443/UDP | HTTP/3-Basis (UDP + eigene Zuverlässigkeit) |
| **RADIUS** | 1812/UDP (Auth), 1813/UDP (Accounting) | AAA |

## Pseudo-Header für Checksum

Die UDP-Checksum wird über einen Pseudo-Header berechnet (nicht übertragen):

```
IPv4 Pseudo-Header:
┌──────────────────────────────────────┐
│ Source IP (32 Bit)                   │
│ Destination IP (32 Bit)              │
│ 00000000 │ Protocol=17 │ UDP Length  │
└──────────────────────────────────────┘
```

→ Bindet die Prüfsumme an die IP-Adressen – verhindert, dass ein Paket an falsche IP geliefert wird ohne Fehler zu melden.

## UDP-basierte Echtzeit-Übertragung

```
Sender                              Empfänger
  │── UDP-Frame 1 ──────────────────→│
  │── UDP-Frame 2 ──────────────────→│
  │── UDP-Frame 3 (verloren) ─── ✗   │
  │── UDP-Frame 4 ──────────────────→│
  │                                   │ Frame 3 fehlt → Lücke (Stille / Artefakt)
  │                                   │ kein Retransmit → akzeptable Qualitätseinbuße
```

Bei VoIP ist ein kurzer Aussetzer tolerabler als eine 200-ms-Verzögerung durch TCP-Retransmit.
