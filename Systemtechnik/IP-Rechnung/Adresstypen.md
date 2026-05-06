Übersicht aller IP-Adresstypen in IPv4 und IPv6 inklusive der reservierten Bereiche.

## IPv4-Adresstypen

| Typ | Bedeutung | Beispiel |
|---|---|---|
| **Unicast** | 1:1 – ein Sender, ein Empfänger | `192.168.1.10` |
| **Multicast** | 1:n – ein Sender, definierte Gruppe | `224.0.0.0/4` (z.B. `224.0.0.5` = OSPF) |
| **Broadcast** | 1:alle im Subnetz | `192.168.1.255` (Subnetz-Broadcast), `255.255.255.255` (limited) |
| **Anycast** | Mehrere Empfänger, nächstgelegener antwortet | Nicht in IPv4 standardisiert, meist über Routing realisiert |

### Reservierte / private IPv4-Bereiche (RFC 1918 + Sonderfälle)

| Bereich | CIDR | Verwendung |
|---|---|---|
| `10.0.0.0` – `10.255.255.255` | `10.0.0.0/8` | Privat (Class A) |
| `172.16.0.0` – `172.31.255.255` | `172.16.0.0/12` | Privat (Class B) |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` | Privat (Class C) |
| `127.0.0.0/8` | – | Loopback (`127.0.0.1` = localhost) |
| `169.254.0.0/16` | – | APIPA (Auto-IP, wenn DHCP fehlschlägt) |
| `224.0.0.0/4` | – | Multicast |
| `0.0.0.0/8` | – | "Dieses Netz" / Default-Route |

## IPv6-Adresstypen

> [!important] **Kein Broadcast in IPv6!** Stattdessen wird Multicast (`ff02::1` = alle Knoten) genutzt.

| Typ | Präfix | Bedeutung |
|---|---|---|
| **Global Unicast (GUA)** | `2000::/3` | Weltweit routbar, vergleichbar mit öffentlicher IPv4 |
| **Unique Local (ULA)** | `fc00::/7` (praktisch `fd00::/8`) | Privat, nicht im Internet routbar (analog RFC 1918) |
| **Link-Local (LLA)** | `fe80::/10` | Nur im selben Link gültig, automatisch generiert (SLAAC), Pflicht auf jedem Interface |
| **Loopback** | `::1/128` | localhost |
| **Unspecified** | `::/128` | "Keine Adresse" (z.B. während DAD) |
| **Multicast** | `ff00::/8` | 1:n – ersetzt IPv4-Broadcast |
| **Anycast** | aus Unicast-Bereich | Mehrere Knoten, nächster antwortet (z.B. DNS-Root-Server) |

### Wichtige IPv6-Multicast-Adressen

| Adresse | Bedeutung |
|---|---|
| `ff02::1` | Alle Knoten im Link |
| `ff02::2` | Alle Router im Link |
| `ff02::1:ffXX:XXXX` | Solicited-Node (für ND/DAD) |
| `ff02::5`, `ff02::6` | OSPFv3 |

> [!tip] **Merksatz**
> IPv4 hat Broadcast, IPv6 nicht. IPv6-Adressen haben **immer** mindestens eine Link-Local-Adresse pro Interface – auch wenn keine globale konfiguriert ist.
