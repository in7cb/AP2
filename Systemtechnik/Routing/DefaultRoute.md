Die Default-Route (auch Gateway of Last Resort) ist eine Catch-All-Route für alle Pakete, für die keine spezifischere Route in der Routing-Tabelle existiert.

## Notation

| Protokoll | Default-Route |
|---|---|
| IPv4 | `0.0.0.0/0` |
| IPv6 | `::/0` |

Das Präfix `/0` passt auf **jede** IP-Adresse – hat aber die kürzeste Prefix-Länge, daher niedrigste Priorität (Longest-Prefix-Match).

> [!tip] **Merksatz**
> `0.0.0.0/0` = alles, was sonst nirgends passt. Kürzester Präfix → wird immer zuletzt geprüft.

## Konfiguration (Cisco IOS)

### Statische Default-Route

```bash
! IPv4 – via Next-Hop IP
ip route 0.0.0.0 0.0.0.0 192.168.1.254

! IPv4 – via Interface (nur bei PPP/Point-to-Point)
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/1

! IPv6 – Default-Route
ipv6 route ::/0 2001:db8::1

! IPv6 – via Link-Local (Interface muss angegeben werden)
ipv6 route ::/0 GigabitEthernet0/1 FE80::1
```

### Überprüfen

```bash
show ip route
! Ausgabe:
Gateway of last resort is 192.168.1.254 to network 0.0.0.0
S*    0.0.0.0/0 [1/0] via 192.168.1.254

show ip route 0.0.0.0      ! Details zur Default-Route
show ipv6 route ::/0
```

## Default-Route via OSPF verteilen

Ein Router kann seine Default-Route per OSPF an alle anderen OSPF-Router verteilen:

```bash
router ospf 1
 default-information originate          ! nur wenn eigene Default-Route existiert
 default-information originate always   ! immer, auch ohne eigene Default-Route
```

→ Alle OSPF-Nachbarn erhalten `O*E2 0.0.0.0/0` in ihrer Routing-Tabelle.

## Default-Route via BGP

```bash
router bgp 65001
 neighbor 203.0.113.1 default-originate  ! Default-Route an BGP-Peer ankündigen
```

## Default-Route via DHCP (Windows/Linux-Clients)

DHCP-Option 3 = Router (Default-Gateway). Wird bei DHCP-Lease automatisch gesetzt:

```
DHCP-Option 3: 192.168.1.1
→ Client trägt 0.0.0.0/0 via 192.168.1.1 in Routing-Tabelle ein
```

## Typische Einsatzszenarien

```
Unternehmen:
  Alle internen Routen: spezifisch (z. B. OSPF)
  Internet: Default-Route → ISP-Router

Heimnetz:
  192.168.1.0/24 – connected
  0.0.0.0/0 via 192.168.1.1 (Heimrouter → Internet)

Stub-Netz (kein Transit-Traffic):
  Nur Default-Route + Connected-Routen nötig
  Kein dynamisches Routing-Protokoll erforderlich
```

> [!important] **Kernregel**
> Kein Paket wird verworfen, solange eine Default-Route existiert. Default-Route fehlt + kein Match → ICMP Destination Unreachable → Pakete werden verworfen ("Blackhole" ohne Blackhole-Route).

## Routing-Hierarchie mit Default-Route

```
Routing-Tabelle (Beispiel):
  10.1.1.0/24  via 10.0.0.1    ← spezifischst, gewinnt für 10.1.1.x
  10.0.0.0/8   via 10.0.0.2    ← für alle anderen 10.x.x.x
  0.0.0.0/0    via 192.168.1.1 ← für alles andere (Internet, Default)

Paket an 8.8.8.8:
  → kein Match für 10.x.x.x
  → Default-Route gewinnt → via 192.168.1.1
```
