IP ist das Protokoll auf Layer 3, das für Adressierung und Routing von Paketen zwischen Netzen verantwortlich ist. IPv4 und IPv6 unterscheiden sich erheblich im Header-Aufbau.

## IPv4 Header (20 Byte Minimum)

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
├─────────┬─────────┬──────────────────────────────────────────────┤
│ Version │   IHL   │   DSCP / ToS          │   Total Length       │
│  (4)    │ (4 Bit) │   (8 Bit)             │   (16 Bit)           │
├─────────────────────────────────┬──┬───────────────────────────────┤
│   Identification (16 Bit)       │Fl│   Fragment Offset (13 Bit)   │
├─────────────────────────────────┴──┴───────────────────────────────┤
│   TTL (8 Bit)   │  Protocol (8 Bit)  │  Header Checksum (16 Bit)  │
├────────────────────────────────────────────────────────────────────┤
│                    Source IP Address (32 Bit)                      │
├────────────────────────────────────────────────────────────────────┤
│                 Destination IP Address (32 Bit)                    │
├────────────────────────────────────────────────────────────────────┤
│                    Options (0–40 Byte, optional)                   │
└────────────────────────────────────────────────────────────────────┘
```

### Wichtige Felder

| Feld | Größe | Beschreibung |
|---|---|---|
| **Version** | 4 Bit | `4` für IPv4 |
| **IHL** | 4 Bit | Internet Header Length in 32-Bit-Wörtern (min. 5 = 20 Byte) |
| **DSCP / ToS** | 8 Bit | Differentiated Services Code Point – QoS-Markierung |
| **Total Length** | 16 Bit | Gesamtgröße Paket (Header + Payload) in Byte; max. 65535 Byte |
| **Identification** | 16 Bit | Eindeutige ID für Fragmentierung |
| **Flags** | 3 Bit | DF (Don't Fragment), MF (More Fragments) |
| **Fragment Offset** | 13 Bit | Position des Fragments im Original-Paket (in 8-Byte-Einheiten) |
| **TTL** | 8 Bit | Time to Live – Hop-Zähler |
| **Protocol** | 8 Bit | Nächstes Protokoll: TCP=6, UDP=17, ICMP=1, OSPF=89 |
| **Header Checksum** | 16 Bit | Prüfsumme nur über Header (nicht Payload) |
| **Source / Destination IP** | je 32 Bit | Quell- und Zieladresse |

## TTL – Time to Live

TTL verhindert, dass Pakete endlos im Netz zirkulieren:
- Sender setzt TTL (Standard: 64 oder 128)
- Jeder Router **dekrementiert TTL um 1**
- Bei TTL = 0: Router verwirft Paket und sendet **ICMP Time Exceeded** zurück

| OS | Default TTL |
|---|---|
| Linux | 64 |
| Windows | 128 |
| Cisco IOS | 255 |
| macOS | 64 |

> [!tip] **Merksatz**
> TTL erkennen lässt sich am Wert: Nächste Zweierpotenz über dem Messwert = Start-TTL. Messwert 57 → Startwert 64 (Linux), 7 Hops entfernt.

## Protocol-Feld – wichtige Werte

| Wert (dezimal) | Protokoll |
|---|---|
| 1 | ICMP |
| 6 | TCP |
| 17 | UDP |
| 41 | IPv6 in IPv4 (6in4-Tunnel) |
| 47 | GRE |
| 89 | OSPF |
| 132 | SCTP |

## Fragmentierung (IPv4)

IPv4 erlaubt Fragmentierung unterwegs (durch Router). Wenn ein Paket größer als die MTU des nächsten Links ist:

```
Paket (1500 Byte) → Link MTU 576 Byte
→ Fragment 1: Bytes 0–575, MF=1, Offset=0
→ Fragment 2: Bytes 576–1151, MF=1, Offset=72 (=576/8)
→ Fragment 3: Bytes 1152–1499, MF=0, Offset=144
```

- **DF-Bit (Don't Fragment):** Paket darf nicht fragmentiert werden → Router sendet ICMP "Fragmentation Needed" zurück (Grundlage für Path MTU Discovery)
- IPv6 fragmentiert **nicht** unterwegs – nur Sender fragmentiert, via Extension Header

## IPv6 Header (40 Byte, fix)

```
 0                   1                   2                   3
├────────┬───────────────────────────────────────────────────────────┤
│Version │ Traffic Class (8 Bit) │      Flow Label (20 Bit)         │
│  (4)   │                                                           │
├────────┴───────────────────────────────────────────────────────────┤
│   Payload Length (16 Bit)       │ Next Header (8)│ Hop Limit (8)  │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│                  Source Address (128 Bit)                          │
│                                                                    │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│                Destination Address (128 Bit)                       │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

### Wichtige Felder IPv6

| Feld | Größe | Beschreibung |
|---|---|---|
| **Version** | 4 Bit | `6` für IPv6 |
| **Traffic Class** | 8 Bit | wie DSCP in IPv4 – QoS |
| **Flow Label** | 20 Bit | Kennzeichnung von Flows (z. B. für QoS, Load Balancing) |
| **Payload Length** | 16 Bit | Länge der Nutzdaten (ohne den 40-Byte-Header) |
| **Next Header** | 8 Bit | wie Protocol in IPv4; zeigt auf nächsten Header oder L4-Protokoll |
| **Hop Limit** | 8 Bit | wie TTL in IPv4 |
| **Source / Dest Address** | je 128 Bit | IPv6-Adressen |

## IPv4 vs. IPv6 im Vergleich

| Merkmal | IPv4 | IPv6 |
|---|---|---|
| Adresslänge | 32 Bit | 128 Bit |
| Header-Größe | 20–60 Byte (variabel) | **40 Byte (fix)** |
| Fragmentierung | Router und Sender | nur Sender |
| Checksum im Header | ✅ | ❌ (Aufgabe an L4) |
| Broadcast | ✅ | ❌ (nur Multicast) |
| NAT notwendig | häufig | selten |
| Konfiguration | manuell / DHCP | SLAAC / DHCPv6 / manuell |
| IPsec | optional | integriert (Pflicht in Spec) |
| Extension Headers | Options-Feld | separate Extension Headers |

> [!important] **Kernregel**
> IPv6-Header ist **immer 40 Byte** – kein IHL-Feld nötig. Optionen werden über Extension Headers realisiert, die in der Next-Header-Kette eingehängt werden.

## Extension Headers (IPv6)

Extension Headers werden zwischen dem Basisheader und dem L4-Header eingefügt:

| Extension Header | Next Header Wert | Zweck |
|---|---|---|
| Hop-by-Hop Options | 0 | Optionen für jeden Router auf dem Weg |
| Routing | 43 | Source Routing |
| Fragment | 44 | Fragmentierung durch Sender |
| ESP | 50 | IPsec Verschlüsselung |
| AH | 51 | IPsec Authentifizierung |
| Destination Options | 60 | Optionen nur für Ziel |
