ICMP (Internet Control Message Protocol) ist ein Hilfsprotokoll auf Layer 3 zur Übermittlung von Fehler- und Statusinformationen. Es hat kein eigenes Transport-Layer – es läuft direkt über IP (Protocol-Feld = 1 für ICMP, 58 für ICMPv6).

## ICMP (IPv4) – RFC 792

### Header-Aufbau

```
 0                   1                   2                   3
├────────────────┬───────────────────┬──────────────────────────────┤
│  Type (8 Bit)  │  Code (8 Bit)     │      Checksum (16 Bit)       │
├────────────────┴───────────────────┴──────────────────────────────┤
│                    Rest of Header (32 Bit, typabhängig)           │
└───────────────────────────────────────────────────────────────────┘
```

### Wichtige ICMP-Typen (IPv4)

| Type | Code | Nachricht | Bedeutung |
|---|---|---|---|
| **0** | 0 | Echo Reply | Antwort auf Ping |
| **3** | 0 | Dest. Unreachable – Net | Zielnetz nicht erreichbar |
| **3** | 1 | Dest. Unreachable – Host | Zielhost nicht erreichbar |
| **3** | 3 | Dest. Unreachable – Port | Zielport nicht offen |
| **3** | 4 | Fragmentation Needed | MTU zu klein, DF-Bit gesetzt |
| **3** | 13 | Admin Prohibited | Firewall blockiert |
| **5** | – | Redirect | besserer Next-Hop verfügbar |
| **8** | 0 | Echo Request | Ping-Anfrage |
| **11** | 0 | Time Exceeded – TTL | TTL abgelaufen im Transit |
| **11** | 1 | Time Exceeded – Fragment | Reassembly-Timeout |
| **12** | – | Parameter Problem | Header-Fehler |

> [!tip] **Merksatz**
> Type 8 = Echo Request (Ping hin), Type 0 = Echo Reply (Ping zurück). Type 11 = TTL Exceeded (traceroute-Grundlage).

## ping – Funktionsweise

```
PC-A                              Ziel
  │── ICMP Echo Request (Type 8) →│
  │← ICMP Echo Reply (Type 0) ───│

Ausgabe:
Reply from 8.8.8.8: bytes=32 time=14ms TTL=117
                                  └── 128 - 11 Hops = 117 (Windows-Start-TTL 128)
```

```bash
ping 8.8.8.8              # Windows: 4 Pakete, Linux: endlos
ping -c 4 8.8.8.8         # Linux: 4 Pakete
ping -l 1400 8.8.8.8      # Windows: Paketgröße 1400 Byte (MTU-Test)
ping -M do -s 1472 8.8.8.8 # Linux: DF-Bit, Payload 1472 Byte (=1500 MTU - 20 IP - 8 ICMP)
```

## traceroute / tracert – Funktionsweise

Sendet Pakete mit TTL 1, 2, 3, … – jeder Router, der ein Paket mit TTL=0 verwirft, sendet ein ICMP Time Exceeded zurück:

```
PC               R1 (1 Hop)      R2 (2 Hops)     Ziel (3 Hops)
│ TTL=1 ────────→│
│← Time Exceeded │
│ TTL=2 ────────────────────────→│
│← Time Exceeded                 │
│ TTL=3 ────────────────────────────────────────→│
│← Echo Reply                                    │
```

```bash
tracert 8.8.8.8           # Windows (ICMP)
traceroute 8.8.8.8        # Linux (UDP Default, oder -I für ICMP)
```

> [!warning] **Achtung Falle**
> Firewalls blockieren oft ICMP oder UDP – traceroute zeigt dann `* * *` für diesen Hop, obwohl der Router existiert. Das bedeutet nicht zwingend einen Fehler.

## Path MTU Discovery (PMTUD)

Nutzt ICMP Typ 3, Code 4 (Fragmentation Needed):

```
1. Sender setzt DF-Bit im IP-Header
2. Router mit kleiner MTU wirft Paket weg
3. Router sendet ICMP Type 3 Code 4 mit seiner MTU
4. Sender reduziert Paketgröße entsprechend
```

> [!warning] **Achtung Falle**
> Wenn ICMP gefiltert wird (z. B. durch Firewall), funktioniert PMTUD nicht → **PMTUD Black Hole**. TCP-Verbindungen hängen mit großen Paketen. Lösung: TCP MSS Clamping am Router/Firewall.

## ICMPv6 (IPv6) – RFC 4443

ICMPv6 ist deutlich umfangreicher als ICMPv4. Es übernimmt zusätzlich Funktionen von ARP, IGMP und Router Discovery.

### Wichtige ICMPv6-Typen

| Type | Nachricht | Funktion |
|---|---|---|
| **1** | Destination Unreachable | wie IPv4 Type 3 |
| **2** | Packet Too Big | wie IPv4 Type 3 Code 4 (PMTUD) |
| **3** | Time Exceeded | wie IPv4 Type 11 |
| **4** | Parameter Problem | Header-Fehler |
| **128** | Echo Request | Ping |
| **129** | Echo Reply | Ping-Antwort |
| **133** | Router Solicitation (RS) | Host sucht Router |
| **134** | Router Advertisement (RA) | Router kündigt sich an (SLAAC) |
| **135** | Neighbor Solicitation (NS) | ARP-Äquivalent |
| **136** | Neighbor Advertisement (NA) | ARP-Reply-Äquivalent |
| **137** | Redirect | besserer Next-Hop |
| **143** | MLDv2 Report | Multicast Listener Discovery (≙ IGMPv3) |

> [!important] **Kernregel**
> ICMPv6 darf **nicht** von Firewalls vollständig geblockt werden! RS/RA (133/134) für SLAAC und NS/NA (135/136) für Neighbor Discovery sind essenziell für IPv6-Betrieb. Nur ICMPv6-Typen gezielt filtern.

## MLD – Multicast Listener Discovery

MLD ist in ICMPv6 integriert und entspricht IGMP für IPv4. Hosts melden sich bei Routern für IPv6-Multicast-Gruppen an:

| MLD-Version | ICMPv6 Typ | Entsprechung |
|---|---|---|
| MLDv1 | 130–132 | IGMPv2 |
| MLDv2 | 143 | IGMPv3 |

## ICMP-Fehlermeldungen enthalten Original-Paket

ICMP-Fehlernachrichten enthalten immer den **IP-Header + erste 8 Byte des Original-Pakets** (=TCP/UDP-Header-Anfang), damit der Sender erkennt, welche Verbindung betroffen ist.
