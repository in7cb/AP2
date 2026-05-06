Wireshark ist ein Open-Source-**Netzwerksniffer** – er zeichnet Pakete auf einer Netzwerkschnittstelle auf und stellt sie nach OSI-Schichten aufgeschlüsselt dar. In der Prüfung: Frame-Mitschnitt interpretieren, Filter anwenden, IPv6-Fallen erkennen.

## Grundbedienung

```text
Capture Filter:  vor der Aufzeichnung – reduziert Datenmenge (z.B. nur Port 80)
Display Filter:  nach der Aufzeichnung – filtert Ansicht ohne Daten zu verlieren
```

**Typische Display-Filter:**

```text
ip.addr == 192.168.1.1          # alle Pakete von/an diese IP
ip.src == 10.0.0.5              # nur Pakete von dieser Quelle
tcp.port == 80                  # HTTP-Traffic
http                            # nur HTTP-Protokoll
dns                             # nur DNS-Anfragen/-Antworten
icmp                            # nur ICMP (ping, traceroute)
arp                             # nur ARP-Pakete
!arp && !icmp                   # ARP und ICMP ausblenden
eth.addr == 00:1a:2b:3c:4d:5e  # nach MAC-Adresse filtern
tcp.flags.syn == 1              # nur TCP-SYN-Pakete (Verbindungsaufbau)
frame.len > 1000                # große Pakete
```

## Frame-Aufbau in Wireshark lesen

Ein typischer HTTP-Frame in Wireshark zeigt folgende Schichten (von unten nach oben):

```text
Frame 42: 74 bytes on wire
├── Ethernet II
│   ├── Src: 00:1a:2b:3c:4d:5e (Absender-MAC)
│   ├── Dst: ff:ff:ff:ff:ff:ff (Ziel-MAC)
│   └── Type: IPv4 (0x0800)
├── Internet Protocol Version 4
│   ├── Src: 192.168.1.5
│   ├── Dst: 8.8.8.8
│   ├── TTL: 64
│   └── Protocol: TCP (6)
├── Transmission Control Protocol
│   ├── Src Port: 54321
│   ├── Dst Port: 80
│   ├── Seq: 1, Ack: 1
│   └── Flags: PSH, ACK
└── Hypertext Transfer Protocol
    └── GET /index.html HTTP/1.1
```

**OSI-Zuordnung der Wireshark-Ebenen:**

| Wireshark-Abschnitt | OSI-Schicht | Protokollbeispiele |
|---|---|---|
| Frame / Physical | 1 | Länge, Signallaufzeit |
| Ethernet II | 2 | MAC-Adressen, EtherType |
| IPv4 / IPv6 | 3 | IP-Adressen, TTL, Protokoll |
| TCP / UDP | 4 | Ports, Flags, Sequenznummern |
| HTTP, DNS, TLS … | 5–7 | Anwendungsdaten |

> [!tip] **Merksatz**
> Wireshark baut den Frame von innen nach außen auf – Ethernet ist der äußerste Rahmen, die Anwendungsdaten sind am tiefsten geschachtelt. In der Prüfung immer die **richtige Schicht** für die gefragte Information identifizieren.

## IPv6-Fallen in Wireshark

In Prüfungen wird oft ein Wireshark-Mitschnitt mit IPv6 gezeigt – typische Fallstricke:

**1. Link-Local vs. Global Unicast verwechseln**

```text
fe80::1                    ← Link-Local (immer! nicht routbar, nur im Segment)
2001:db8::1                ← Global Unicast (routbar)
::1                        ← Loopback
```

> [!warning] **Achtung Falle**
> `fe80::`-Adressen sind **immer Link-Local** – sie werden nie über einen Router weitergeleitet. Wenn ein Frame in Wireshark `fe80::` als Quelle zeigt, kommuniziert das Gerät nur im lokalen Segment.

**2. ICMPv6 – häufig gesehen, nicht mit ICMP verwechseln**

| ICMPv6-Typ | Funktion |
|---|---|
| Typ 133 (RS) | Router Solicitation – Client sucht Router |
| Typ 134 (RA) | Router Advertisement – Router sendet Präfix |
| Typ 135 (NS) | Neighbor Solicitation – wie ARP-Request in IPv4 |
| Typ 136 (NA) | Neighbor Advertisement – wie ARP-Reply in IPv4 |
| Typ 128 (Echo Request) | Ping |
| Typ 129 (Echo Reply) | Pong |

**3. Multicast-Adressen in IPv6**

```text
ff02::1    alle Knoten im Segment
ff02::2    alle Router im Segment
ff02::fb   mDNS
```

Pakete an `ff02::` sind normale Multicast-Übertragungen – kein Fehler.

**4. Erweiterungsheader**

IPv6 hat keine Optionen im Hauptheader – Zusatzinformationen kommen in Erweiterungsheadern (z.B. Hop-by-Hop, Routing, Fragment). Wireshark zeigt diese als eigene Zeile zwischen IPv6 und dem Transportprotokoll.

## ARP in Wireshark

```text
ARP Request (Broadcast):
  Sender MAC: aa:bb:cc:dd:ee:ff
  Sender IP:  192.168.1.5
  Target MAC: 00:00:00:00:00:00  ← unbekannt, daher Anfrage
  Target IP:  192.168.1.1

ARP Reply (Unicast):
  Sender MAC: 11:22:33:44:55:66  ← das ist die gesuchte MAC
  Sender IP:  192.168.1.1
  Target MAC: aa:bb:cc:dd:ee:ff
  Target IP:  192.168.1.5
```

**ARP Poisoning/Spoofing erkennen:**
- Mehrere ARP-Replies für dieselbe IP mit verschiedenen MACs → Angreifer überschreibt ARP-Cache
- In Wireshark: Filter `arp.opcode == 2` (nur Replies) → doppelte IPs mit verschiedenen MACs = Verdacht

> [!warning] **Achtung Falle**
> ARP hat **keine Authentifizierung** – jeder kann eine ARP-Reply senden ohne vorherige Anfrage (Gratuitous ARP). Dynamic ARP Inspection (DAI) auf Switches schützt dagegen.

## TCP-Handshake in Wireshark

```text
Frame 1: Client → Server  SYN       (Seq=0)
Frame 2: Server → Client  SYN, ACK  (Seq=0, Ack=1)
Frame 3: Client → Server  ACK       (Seq=1, Ack=1)
```

**TCP-Flags:**

| Flag | Bedeutung |
|---|---|
| `SYN` | Verbindungsaufbau |
| `ACK` | Bestätigung |
| `FIN` | Verbindungsabbau (geordnet) |
| `RST` | Verbindungsabbruch (sofort) |
| `PSH` | Daten sofort weiterleiten (kein Puffer) |

> [!important] **Kernregel**
> TCP-Verbindung = immer mit **SYN → SYN/ACK → ACK** (3-Way-Handshake). Wenn in einem Mitschnitt direkt Daten kommen ohne vorherigen Handshake, wurde der Anfang der Aufzeichnung verpasst.

## Diagnose-Workflow (Bottom-Up)

In Prüfungen wird oft ein Netzwerkproblem per Wireshark diagnostiziert. Vorgehensweise nach OSI-Schichten von unten:

| Schicht | Prüfung | Anzeichen für Fehler |
|---|---|---|
| L1 Physical | Kabel, Link-LED | kein Frame empfangen |
| L2 Ethernet | ARP-Antwort vorhanden? MAC korrekt? | kein ARP-Reply, falsche MAC |
| L3 IP | IP-Adresse, Subnetz, TTL > 0 | ICMP Unreachable, TTL=0 |
| L4 TCP/UDP | Port erreichbar, Verbindung aufgebaut? | RST statt SYN/ACK, kein ACK |
| L7 Anwendung | HTTP-Statuscode, DNS-Antwort | 404, NXDOMAIN, Timeout |
