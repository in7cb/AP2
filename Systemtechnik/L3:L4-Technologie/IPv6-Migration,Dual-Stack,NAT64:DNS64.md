Die Migration von IPv4 zu IPv6 ist ein Jahrzehnte langer Prozess. Es gibt drei Hauptstrategien: Dual-Stack, Tunneling und Translation.

## Warum Migration nötig?

- IPv4-Adressraum ist erschöpft (IANA: 2011, RIPE NCC: 2019)
- IPv6 bietet 2¹²⁸ Adressen (~340 Sextillionen)
- Neue Features: kein Broadcast, integrierte Sicherheit, SLAAC, bessere QoS

## Strategie 1: Dual-Stack (bevorzugt)

Geräte und Router laufen **gleichzeitig mit IPv4 und IPv6** – beide Protokollstapel voll funktionsfähig.

```
Host: 192.168.1.10 / 2001:db8::1
Router: IPv4-Routing + IPv6-Routing aktiv
DNS: gibt A-Records (IPv4) und AAAA-Records (IPv6) zurück
```

**Verhalten:**
- Browser verbindet per IPv6 wenn vorhanden (Happy Eyeballs – RFC 8305: parallel starten, schnellerer Gewinner wird genutzt)
- Kein Umbau nötig, beide Welten koexistieren

**Vorteile:** native Performance, keine Übersetzung, einfachste Übergangsstrategie
**Nachteil:** beide Protokolle müssen administriert werden, IPv4-Probleme bleiben

> [!important] **Kernregel**
> Dual-Stack ist die empfohlene Standardmigrationsstrategie. Alle großen Betriebssysteme bevorzugen IPv6 gegenüber IPv4 (Happy Eyeballs).

## Strategie 2: Tunneling

IPv6-Pakete werden in IPv4-Pakete eingekapselt, um durch IPv4-only-Infrastruktur zu transportieren.

### 6in4 (Protokoll 41)

Manuell konfigurierter Tunnel: IPv6 in IPv4 verpackt (kein Overhead-Protokoll).

```
IPv6-Host → [IPv6-Paket | IPv4-Header (Proto=41)] → IPv4-Netz → IPv6-Host
```

```bash
# Cisco: 6in4-Tunnel
interface Tunnel0
 ipv6 address 2001:db8:1::1/64
 tunnel source GigabitEthernet0/0
 tunnel destination 203.0.113.5
 tunnel mode ipv6ip          ! = 6in4
```

### 6to4 (RFC 3056)

Automatisches Tunneling – IPv6-Präfix wird aus öffentlicher IPv4-Adresse abgeleitet:

```
IPv4: 203.0.113.5
→ IPv6-Präfix: 2002:CB00:7105::/48
              (203 = CB, 0 = 00, 113 = 71, 5 = 05)
```

Relay-Router mit Anycast `192.88.99.1` → kein Endpoint nötig. Gilt als veraltet.

### ISATAP (Intra-Site Automatic Tunnel Addressing Protocol)

Tunneling innerhalb eines Unternehmensnetzes. IPv6-Adresse enthält IPv4-Adresse:
`fe80::5efe:192.168.1.10` (Link-Local ISATAP).

### Teredo (RFC 4380)

Tunneling durch NAT (UDP-basiert). Notlösung wenn alle anderen Methoden scheitern. Genutzt von Windows in IPv4-only-Umgebungen. Gilt als veraltet und unsicher.

## Strategie 3: Translation (NAT64 + DNS64)

IPv6-only-Hosts kommunizieren mit IPv4-only-Hosts durch Adressübersetzung.

### NAT64 (RFC 6146)

Ein Gateway übersetzt IPv6-Pakete in IPv4 und umgekehrt:

```
IPv6-Host (2001:db8::1) → NAT64-GW → IPv4-Server (93.184.216.34)

IPv6-Adresse des IPv4-Ziels: 64:ff9b::93.184.216.34
                               └─ NAT64 Well-Known Prefix
```

Der IPv6-Client adressiert IPv4-Ziele über den NAT64-Präfix `64:ff9b::/96`. Die letzten 32 Bit enthalten die IPv4-Adresse.

### DNS64 (RFC 6147)

DNS64 erzeugt synthetische AAAA-Records für Hosts, die nur A-Records haben:

```
Client fragt: AAAA für example.com?
DNS64 findet: kein AAAA-Record, aber A = 93.184.216.34
DNS64 antwortet: AAAA = 64:ff9b::93.184.216.34 (synthetisch)
Client → sendet an 64:ff9b::93.184.216.34 → NAT64 übersetzt
```

```
Ablauf:
IPv6-Client → DNS64 → AAAA 64:ff9b::IPv4 → IPv6-Paket → NAT64-GW → IPv4-Server
```

> [!tip] **Merksatz**
> NAT64 + DNS64 = IPv6-only-Client kann IPv4-only-Server erreichen. DNS64 macht den AAAA-Record, NAT64 macht die Übersetzung.

## 464XLAT

Erweiterung von NAT64 für Apps, die nur IPv4-Sockets öffnen können (ältere Apps):

```
App (IPv4-Socket) → CLAT (Client-seitiger Translator, 464) → IPv6 → PLAT (NAT64) → IPv4
```

Genutzt von Mobilfunkbetreibern, die IPv6-only-Kernnetze betreiben.

## Vergleich der Migrationsstrategien

| Strategie | Beschreibung | Vorteil | Nachteil |
|---|---|---|---|
| **Dual-Stack** | IPv4 + IPv6 parallel | nativ, kein Overhead | beide Protokolle zu verwalten |
| **6in4 / Tunneling** | IPv6 in IPv4 einkapseln | keine Infrastruktur nötig | höherer Overhead, MTU-Probleme |
| **NAT64 + DNS64** | IPv6-only + Übersetzung | IPv4 abschalten möglich | kein natives IPv4 für Clients |
| **464XLAT** | NAT64 + CLAT für IPv4-Apps | IPv6-only-Netze mit alten Apps | Komplexität |

## Typische Migrations-Roadmap

```
Phase 1: Dual-Stack einführen (Infrastruktur)
Phase 2: Alle neuen Dienste auf IPv6-only oder Dual-Stack
Phase 3: IPv4-only-Dienste via NAT64 erreichbar machen
Phase 4: IPv4 deaktivieren (IPv6-only)
```

> [!warning] **Achtung Falle**
> Tunneling-Methoden wie Teredo und 6to4 gelten als **Sicherheitsrisiko** und Legacy. In modernen Netzen Dual-Stack oder NAT64 bevorzugen, Teredo ggf. deaktivieren.
