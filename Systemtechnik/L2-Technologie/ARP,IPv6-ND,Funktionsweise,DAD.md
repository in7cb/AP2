ARP (IPv4) und Neighbor Discovery (IPv6) lösen das gleiche Problem: **IP-Adresse → MAC-Adresse** auflösen, damit Frames auf Layer 2 zugestellt werden können.

## ARP (Address Resolution Protocol) – IPv4

ARP arbeitet auf Layer 2/3 und ist in RFC 826 definiert.

### Ablauf

```
PC-A (192.168.1.10) will PC-B (192.168.1.20) erreichen:

1. PC-A prüft ARP-Cache → kein Eintrag für 192.168.1.20
2. PC-A sendet ARP Request (Broadcast):
   "Wer hat 192.168.1.20? Sag es 192.168.1.10 (AA:BB:CC:11:22:33)"
3. PC-B antwortet mit ARP Reply (Unicast):
   "192.168.1.20 hat MAC BB:CC:DD:44:55:66"
4. PC-A speichert Eintrag im ARP-Cache
5. PC-A sendet IP-Paket direkt an BB:CC:DD:44:55:66
```

| Paket | Typ | Ziel-MAC | Ziel-IP |
|---|---|---|---|
| ARP Request | Broadcast | FF:FF:FF:FF:FF:FF | gesuchte IP |
| ARP Reply | Unicast | anfragende MAC | anfragende IP |

### ARP-Cache

```bash
# Windows
arp -a

# Linux
ip neigh show
arp -n
```

- Einträge veralten nach einigen Minuten (typ. 2–20 Min.)
- **Gratuitous ARP:** Gerät kündigt eigene IP/MAC an (z. B. nach IP-Änderung, VRRP Failover)

> [!warning] **Achtung Falle**
> **ARP-Spoofing:** Angreifer sendet gefälschte ARP Replies → vergiftet ARP-Cache anderer Hosts → Man-in-the-Middle. Gegenmaßnahme: Dynamic ARP Inspection (DAI) am Switch.

## IPv6 Neighbor Discovery Protocol (NDP)

NDP ersetzt ARP in IPv6 (RFC 4861). Nutzt **ICMPv6** Nachrichten und IPv6 **Multicast** statt Broadcast.

### NDP-Nachrichtentypen

| Typ | ICMPv6-Code | Funktion |
|---|---|---|
| **NS (Neighbor Solicitation)** | 135 | ARP-Request-Äquivalent: "Wer hat diese IPv6?" |
| **NA (Neighbor Advertisement)** | 136 | ARP-Reply-Äquivalent: Antwort mit MAC |
| **RS (Router Solicitation)** | 133 | Host fragt nach Router |
| **RA (Router Advertisement)** | 134 | Router kündigt Präfix, Gateway an (SLAAC) |
| **Redirect** | 137 | Router weist auf besseren Next-Hop hin |

### Ablauf NS/NA

```
PC-A (2001:db8::1) will PC-B (2001:db8::2) erreichen:

1. PC-A sendet NS an Solicited-Node Multicast (ff02::1:ff00:0002)
   "Wer hat 2001:db8::2?"
2. PC-B antwortet mit NA (Unicast an PC-A)
   "2001:db8::2 bin ich, MAC = CC:DD:EE:77:88:99"
3. PC-A speichert im Neighbor Cache
```

**Solicited-Node Multicast:** `ff02::1:ff` + letzte 24 Bit der IPv6-Adresse

> [!tip] **Merksatz**
> NDP = ARP + RARP + ICMP Router Discovery + Redirect, alles in einem, über ICMPv6.

## DAD – Duplicate Address Detection

DAD prüft vor der Nutzung einer IPv6-Adresse, ob diese bereits vergeben ist (RFC 4862).

### Ablauf

```
1. Gerät generiert Kandidat-Adresse (z. B. via SLAAC)
2. Gerät setzt Adresse in "tentative"-Zustand
3. Gerät sendet NS an Solicited-Node Multicast der Kandidat-Adresse
   Quell-IP: :: (unspecified), Ziel: ff02::1:ffXX:XXXX
4a. Kein NA empfangen nach 1s → Adresse ist einzigartig → Adresse wird aktiv
4b. NA empfangen → Adresse ist Duplikat → DAD-Fehler, Adresse wird nicht verwendet
```

> [!important] **Kernregel**
> DAD läuft **immer** vor Aktivierung einer IPv6-Adresse – auch bei manuell konfigurierten Adressen. Kein Broadcast, sondern ICMPv6 NS an Multicast.

## Neighbor Cache vs. ARP-Cache

| Merkmal | ARP-Cache (IPv4) | Neighbor Cache (IPv6) |
|---|---|---|
| Protokoll | ARP (Layer 2/3) | NDP / ICMPv6 |
| Adressierung | Broadcast | Multicast (Solicited-Node) |
| Zustände | - | INCOMPLETE, REACHABLE, STALE, DELAY, PROBE |
| Anzeige | `arp -a` | `ip -6 neigh show` |
