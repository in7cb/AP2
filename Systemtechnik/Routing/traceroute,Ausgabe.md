traceroute (Linux/macOS) bzw. tracert (Windows) zeigt den Pfad, den Pakete durch das Netz nehmen, und misst die RTT (Round Trip Time) zu jedem Hop.

## Funktionsprinzip

traceroute nutzt das **TTL-Mechanismus** des IP-Headers:

```
1. Sende Paket mit TTL=1 → Router 1 verwirft, sendet ICMP Time Exceeded zurück
2. Sende Paket mit TTL=2 → Router 2 verwirft, sendet ICMP Time Exceeded zurück
3. …
n. Sende Paket mit TTL=n → Ziel erreicht → ICMP Echo Reply (oder Port Unreachable)
```

Pro TTL-Wert werden **3 Pakete** gesendet → 3 RTT-Messwerte pro Hop.

### Protokoll-Unterschiede

| Tool | Protokoll | Zielport |
|---|---|---|
| `tracert` (Windows) | ICMP Echo Request | – |
| `traceroute` (Linux) | UDP | 33434+ (hochnummeriert) |
| `traceroute -I` (Linux) | ICMP Echo Request | – |
| `traceroute -T -p 80` (Linux) | TCP | 80 |

## Ausgabe lesen – Linux

```
$ traceroute 8.8.8.8

traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)    1.234 ms  1.198 ms  1.211 ms
 2  10.0.0.1 (10.0.0.1)          5.432 ms  5.401 ms  5.398 ms
 3  * * *
 4  72.14.215.165 (72.14.215.165) 14.321 ms  14.290 ms  14.277 ms
 5  8.8.8.8 (8.8.8.8)            14.891 ms  14.843 ms  14.862 ms
```

| Spalte | Bedeutung |
|---|---|
| `1`, `2`, … | Hop-Nummer (TTL-Wert) |
| `192.168.1.1` | IP des Routers, der ICMP Time Exceeded gesendet hat |
| `1.234 ms` | RTT für Paket 1 (von 3) |
| `* * *` | Hop 3 hat **nicht geantwortet** – Firewall blockt ICMP oder Router sendet keine Time Exceeded |

## Ausgabe lesen – Windows (tracert)

```
C:\> tracert 8.8.8.8

Routenverfolgung zu 8.8.8.8 über höchstens 30 Hops

  1    <1 ms    <1 ms    <1 ms  192.168.1.1
  2     5 ms     5 ms     5 ms  10.0.0.1
  3     *        *        *     Zeitüberschreitung der Anforderung.
  4    14 ms    14 ms    14 ms  72.14.215.165
  5    15 ms    14 ms    15 ms  dns.google [8.8.8.8]
```

## Häufige Muster & Interpretation

| Muster | Bedeutung |
|---|---|
| `* * *` | Router antwortet nicht auf ICMP/UDP – Firewall, kein ICMP-Support, ICMP rate-limited |
| Latenz springt stark | Netzwerkproblem, langsamer WAN-Link, oder geografisch weite Strecke |
| RTT sinkt wieder | Asymmetrisches Routing – ICMP-Antwort kommt anderen Weg zurück |
| `!H` | Host Unreachable |
| `!N` | Network Unreachable |
| `!P` | Protocol Unreachable |
| `!X` | Communication Administratively Prohibited (Firewall) |

> [!warning] **Achtung Falle**
> `* * *` bedeutet **nicht** zwingend, dass der Router oder der Weg dahinter ausgefallen ist. Viele Router droppen ICMP/UDP für traceroute gezielt (ICMP rate limiting, Firewall). Wenn Hop n+1 antwortet, ist Hop n nur "unsichtbar".

## Asymmetrisches Routing

Traceroute misst Hin- **und** Rückweg. Wenn der Rückweg eine andere Route nimmt, können RTT-Werte scheinbar sinken:

```
Hop 4:  14 ms  (kürzerer Rückweg als Hop 5)
Hop 5:  30 ms
Hop 6:  12 ms  ← Rückpfad kürzer als der für Hop 5
```

→ Kein Fehler, nur asymmetrisches Routing.

## traceroute-Optionen (Linux)

```bash
traceroute 8.8.8.8              # Standard (UDP)
traceroute -I 8.8.8.8           # ICMP (wie tracert)
traceroute -T -p 443 8.8.8.8    # TCP SYN auf Port 443
traceroute -n 8.8.8.8           # keine DNS-Auflösung (schneller)
traceroute -m 20 8.8.8.8        # max. 20 Hops (default: 30)
traceroute -w 2 8.8.8.8         # 2 Sekunden Timeout pro Hop
traceroute -s 192.168.1.10 8.8.8.8  # Source-IP wählen
```

## MTR – kombiniertes ping + traceroute

`mtr` (My Traceroute) zeigt kontinuierlich Pfad + Paketverlust + RTT:

```bash
mtr 8.8.8.8          # interaktiv
mtr --report 8.8.8.8 # einmalig als Report
```

> [!tip] **Merksatz**
> traceroute = Pfad finden. ping = Erreichbarkeit testen. mtr = beides zusammen, kontinuierlich.
