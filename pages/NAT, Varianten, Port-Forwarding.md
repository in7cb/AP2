[[L3, L4-Technologie|zurück]]

---

# NAT – Network Address Translation

NAT übersetzt IP-Adressen (und ggf. Ports) beim Übergang zwischen Netzen – typischerweise zwischen privatem LAN und dem Internet. Ermöglicht vielen Hosts das Teilen einer einzigen öffentlichen IP.

## Warum NAT?

- IPv4-Adressmangel: Private Adressbereiche (RFC 1918) + NAT → viele Hosts hinter einer öffentlichen IP
- Sicherheit (eingeschränkt): Interne Adressen von außen nicht direkt erreichbar
- IPv6 macht NAT theoretisch überflüssig – in der Praxis aber noch lange präsent

**Private Adressbereiche (RFC 1918):**

| Bereich | CIDR | Adressen |
|---|---|---|
| `10.0.0.0` | `/8` | 16,7 Mio. |
| `172.16.0.0–172.31.255.255` | `/12` | 1,05 Mio. |
| `192.168.0.0` | `/16` | 65.536 |

## NAT-Varianten

### Static NAT (1:1)

Feste Zuordnung: eine private IP ↔ eine öffentliche IP.

```
Intern: 192.168.1.10  ←→  Extern: 203.0.113.10 (fest)
```

Anwendung: Server, der von außen unter fester öffentlicher IP erreichbar sein soll.

### Dynamic NAT (Pool)

Pool öffentlicher IPs wird dynamisch vergeben – bei mehr Hosts als IPs: Warten.

```
Intern: 192.168.1.10  →  öffentlich: 203.0.113.x (aus Pool)
        192.168.1.11  →  öffentlich: 203.0.113.y
        192.168.1.12  →  (wartet, Pool voll)
```

### PAT / NAT Overload (NAPT) – der Normalfall

**Port Address Translation** = viele private IPs teilen eine öffentliche IP. Unterscheidung über Port-Nummern:

```
192.168.1.10:54321  →  203.0.113.1:1024
192.168.1.11:48000  →  203.0.113.1:1025
192.168.1.12:60000  →  203.0.113.1:1026
```

Der NAT-Router führt eine **NAT-Tabelle** mit diesen Zuordnungen:

| Inside Local | Inside Global | Outside Global |
|---|---|---|
| 192.168.1.10:54321 | 203.0.113.1:1024 | 8.8.8.8:443 |
| 192.168.1.11:48000 | 203.0.113.1:1025 | 8.8.8.8:443 |

> [!tip] **Merksatz**
> **PAT / NAT Overload** ist das, was in jedem Heimrouter läuft. Eine öffentliche IP, tausende Verbindungen dank Port-Mapping.

## NAT-Terminologie (Cisco)

| Begriff | Bedeutung |
|---|---|
| **Inside Local** | Private IP des Hosts im LAN |
| **Inside Global** | Öffentliche IP des Hosts (nach Übersetzung) |
| **Outside Local** | IP des externen Hosts aus Sicht des internen Netzes |
| **Outside Global** | Echte öffentliche IP des externen Hosts |

## Port-Forwarding (DNAT / Static PAT)

Eingehende Verbindungen von außen werden an einen internen Host weitergeleitet:

```
Anfrage von Internet → 203.0.113.1:80
           ↓ Port-Forwarding-Regel
Router leitet weiter → 192.168.1.50:80 (interner Webserver)
```

**Typische Anwendungen:**

| Externer Port | Interner Host | Dienst |
|---|---|---|
| 80/TCP | 192.168.1.50:80 | Webserver |
| 443/TCP | 192.168.1.50:443 | HTTPS |
| 22/TCP | 192.168.1.10:22 | SSH |
| 3389/TCP | 192.168.1.20:3389 | RDP |
| 25565/TCP | 192.168.1.30:25565 | Minecraft |

> [!warning] **Achtung Falle**
> Port-Forwarding öffnet Ports direkt ins Internet. Sicherheitsmaßnahmen: Firewall-Regeln zusätzlich, nur nötige Ports freigeben, kein Telnet/unverschlüsseltes Protokoll forwarden.

## NAT-Konfiguration (Cisco IOS)

```bash
! Inside-Interface (LAN)
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 ip nat inside

! Outside-Interface (WAN)
interface GigabitEthernet0/1
 ip address 203.0.113.1 255.255.255.252
 ip nat outside

! ACL: welche internen Adressen übersetzt werden
access-list 1 permit 192.168.1.0 0.0.0.255

! PAT (Overload) auf Outside-Interface-IP
ip nat inside source list 1 interface GigabitEthernet0/1 overload

! Static Port-Forwarding (Port 80 → 192.168.1.50:80)
ip nat inside source static tcp 192.168.1.50 80 203.0.113.1 80
```

```bash
show ip nat translations       ! aktive NAT-Einträge anzeigen
show ip nat statistics         ! NAT-Statistiken
clear ip nat translation *     ! Tabelle leeren
```

## Probleme mit NAT

| Problem | Beschreibung |
|---|---|
| **End-to-End-Connectivity** | Hosts hinter NAT nicht direkt adressierbar von außen |
| **Protokolle mit IP in Payload** | FTP (Active), SIP, H.323 – NAT versteht den Inhalt nicht → ALG nötig |
| **Mehrfach-NAT** | Geschachtelte NATs (Carrier-grade NAT + Heim-NAT) → Komplexität |
| **Performance** | NAT-Tabelle-Lookups bei hohem Traffic |

**ALG (Application Layer Gateway):** NAT-Erweiterung, die Protokolle wie SIP oder FTP versteht und IP-Adressen auch in der Payload anpasst.

## CGNAT (Carrier-Grade NAT)

Provider vergibt ebenfalls private Adressen (100.64.0.0/10 – RFC 6598) an Kunden und macht NAT im Netz des Providers. Der Kunde hat dann NAT-hinter-NAT.
