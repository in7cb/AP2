[[L3, L4-Technologie|zurück]]

---

# TCP – Transmission Control Protocol

TCP ist ein verbindungsorientiertes, zuverlässiges Transportprotokoll auf Layer 4 (RFC 793). Es garantiert vollständige, fehlerfreie Übertragung in korrekter Reihenfolge.

## TCP Header (20 Byte Minimum)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├───────────────────────────────┬───────────────────────────────────┤
│      Source Port (16 Bit)     │   Destination Port (16 Bit)       │
├───────────────────────────────┴───────────────────────────────────┤
│                    Sequence Number (32 Bit)                       │
├───────────────────────────────────────────────────────────────────┤
│                 Acknowledgment Number (32 Bit)                    │
├──────────┬──────────────┬─────────────────────────────────────────┤
│Data Off. │   Reserved   │  Flags (9 Bit)  │   Window Size (16 Bit)│
│ (4 Bit)  │   (3 Bit)    │ URG ACK PSH RST SYN FIN               │
├──────────┴──────────────┴─────────────────────────────────────────┤
│      Checksum (16 Bit)        │   Urgent Pointer (16 Bit)         │
├───────────────────────────────┴───────────────────────────────────┤
│                    Options (0–40 Byte, optional)                  │
└───────────────────────────────────────────────────────────────────┘
```

### Felder im Detail

| Feld | Größe | Beschreibung |
|---|---|---|
| **Source Port** | 16 Bit | Absenderport |
| **Destination Port** | 16 Bit | Zielport / Dienst |
| **Sequence Number** | 32 Bit | Byte-Position des ersten Datenbytes in diesem Segment |
| **Acknowledgment Number** | 32 Bit | Nächste erwartete Byte-Nummer vom Gegner (nur wenn ACK=1) |
| **Data Offset** | 4 Bit | TCP-Header-Länge in 32-Bit-Wörtern (min. 5 = 20 Byte) |
| **Flags** | 9 Bit | Steuer-Bits (siehe unten) |
| **Window Size** | 16 Bit | Empfangspuffergröße in Byte → Flusskontrolle |
| **Checksum** | 16 Bit | Pflicht (über Pseudo-Header + TCP-Header + Payload) |
| **Urgent Pointer** | 16 Bit | Offset zu dringenden Daten (nur wenn URG=1, selten genutzt) |

### TCP-Flags

| Flag | Bedeutung |
|---|---|
| **SYN** | Verbindungsaufbau – Synchronisierung von Sequenznummern |
| **ACK** | Acknowledgment – bestätigt empfangene Daten |
| **FIN** | Verbindungsabbau (graceful) |
| **RST** | Verbindungsabbruch (sofort) |
| **PSH** | Push – Daten sofort an Anwendung übergeben, nicht puffern |
| **URG** | Urgent – dringende Daten vorhanden |
| **ECE / CWR** | Explicit Congestion Notification (ECN) |

## Sequenznummern & Acknowledgment

```
Sender                               Empfänger
SEQ=100, 500 Byte Daten ──────────→
                         ←────────── ACK=600 (nächste erwartete Byte-Nr.)
SEQ=600, 500 Byte Daten ──────────→
                         ←────────── ACK=1100
```

- **SEQ** gibt an, ab welchem Byte dieses Segment beginnt
- **ACK** gibt an, welche Byte-Nummer als nächste erwartet wird (cumulative ACK)
- Initiale Sequenznummer (ISN) ist zufällig gewählt (Sicherheit)

> [!tip] **Merksatz**
> ACK = SEQ des letzten empfangenen Segments + Länge der Daten. "Ich habe bis Byte X bekommen, schick jetzt X+1."

## 3-Way-Handshake (Verbindungsaufbau)

```
Client                              Server
  │── SYN (SEQ=1000) ─────────────→ │
  │← SYN-ACK (SEQ=5000, ACK=1001) ──│
  │── ACK (ACK=5001) ───────────────→│
  │         Verbindung offen          │
```

1. Client sendet **SYN** mit zufälliger ISN
2. Server antwortet mit **SYN-ACK** (eigene ISN + ACK für Client-ISN+1)
3. Client bestätigt mit **ACK**

## 4-Way-Handshake (Verbindungsabbau)

```
Client                              Server
  │── FIN ────────────────────────→ │
  │← ACK ───────────────────────── │
  │← FIN ───────────────────────── │  (Server kann noch Daten senden)
  │── ACK ────────────────────────→ │
  │         TIME_WAIT (2×MSL)        │
```

- **TIME_WAIT:** Client wartet 2×MSL (Maximum Segment Lifetime, ~60–120 s) bevor Port freigegeben wird
- Verhindert, dass verspätete Pakete in neue Verbindung gelangen

## Flusskontrolle (Flow Control)

Das **Window-Feld** gibt an, wie viele Bytes der Empfänger noch puffern kann:

```
Sender darf maximal Window-Size Bytes unbestätigt senden:
Window=4000 → Sender kann 4 × 1000-Byte-Segmente senden ohne ACK abzuwarten
```

- **Window Scaling (TCP-Option):** Window-Wert × 2^Skalierungsfaktor → bis ~1 GB Window
- **Zero Window:** Empfänger ist voll → Sender muss warten

## Stauvermeidung (Congestion Control)

| Phase | Beschreibung |
|---|---|
| **Slow Start** | CWND (Congestion Window) startet bei 1 MSS, verdoppelt sich pro RTT |
| **Congestion Avoidance** | Ab Threshold: CWND wächst um 1 MSS pro RTT (linear) |
| **Fast Retransmit** | 3 Duplikat-ACKs → sofort Retransmit, ohne auf Timeout zu warten |
| **Fast Recovery** | Nach Fast Retransmit: CWND halbieren, weiter in Congestion Avoidance |

## Retransmission

- **RTO (Retransmission Timeout):** Kein ACK nach Timeout → Segment neu senden
- **Duplikat-ACK:** Zeigt Lücke im Empfang → Fast Retransmit möglich

## Wichtige TCP-Anwendungen

| Protokoll | Port | Beschreibung |
|---|---|---|
| HTTP | 80/TCP | Webseiten unverschlüsselt |
| HTTPS | 443/TCP | Webseiten verschlüsselt (TLS) |
| SSH | 22/TCP | Sichere Remote-Verwaltung |
| Telnet | 23/TCP | Remote-Verwaltung (unverschlüsselt) |
| FTP | 20/TCP (Daten), 21/TCP (Control) | Dateitransfer |
| SMTP | 25/TCP | Mail-Versand |
| IMAP | 143/TCP, 993/TCP (TLS) | Mail-Empfang mit Sync |
| POP3 | 110/TCP, 995/TCP (TLS) | Mail-Empfang |
| DNS | 53/TCP | Zone Transfers, große Antworten |
| BGP | 179/TCP | Interdomain-Routing |
| LDAP | 389/TCP | Verzeichnisdienst |

> [!warning] **Achtung Falle**
> FTP nutzt **zwei** TCP-Verbindungen: Port 21 für Steuerung (Befehle), Port 20 für Datenübertragung (Active Mode). Im Passive Mode wählt der Server einen dynamischen Port für Daten → Firewall-Problem bei Active FTP.
