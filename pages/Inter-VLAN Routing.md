[[Routing|zurück]]

---

# Inter-VLAN Routing

VLANs trennen Broadcast-Domains – ohne Router oder Layer-3-Switch können Geräte in verschiedenen VLANs **nicht** miteinander kommunizieren. Inter-VLAN Routing schafft diese Verbindung.

## Variante 1: Router-on-a-Stick (RoaS)

Ein einziger physischer Router-Port wird als Trunk konfiguriert. Pro VLAN wird ein **Sub-Interface** mit eigener IP erstellt.

```
Switch (Trunk)──────────────────────────────Router
                                       Gi0/0.10 (VLAN 10)
                                       Gi0/0.20 (VLAN 20)
                                       Gi0/0.99 (VLAN 99 Management)
```

### Konfiguration (Cisco)

```bash
! Switch-Seite: Trunk zum Router
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99

! Router-Seite: Sub-Interfaces
interface GigabitEthernet0/0
 no shutdown                    ! physisches Interface muss up sein

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10         ! VLAN-Tag 10 entfernen/einfügen
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface GigabitEthernet0/0.99
 encapsulation dot1Q 99 native  ! native VLAN (untagged)
 ip address 192.168.99.1 255.255.255.0
```

**Vorteile:** einfach, ein Kabel zum Router
**Nachteile:** Engpass beim physischen Uplink (Bandbreite geteilt), nicht für viele VLANs skalierbar

> [!tip] **Merksatz**
> Router-on-a-Stick = ein Trunk-Kabel, viele Sub-Interfaces. Trunk muss auf Switch-Seite konfiguriert werden.

## Variante 2: Layer-3-Switch mit SVIs

Ein L3-Switch hat für jedes VLAN ein **SVI (Switched Virtual Interface)** – eine virtuelle Schnittstelle mit IP-Adresse. Routing findet direkt im Switch statt (kein externer Router nötig).

```
L3-Switch
├── VLAN 10: SVI ip 192.168.10.1/24
├── VLAN 20: SVI ip 192.168.20.1/24
└── Routing zwischen VLANs intern
```

### Konfiguration (Cisco)

```bash
! IP-Routing aktivieren (oft default aus)
ip routing

! SVIs erstellen
interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

! VLAN muss auf dem Switch existieren
vlan 10
 name Mitarbeiter
vlan 20
 name Server

! Ports den VLANs zuordnen
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

**Vorteile:** hohe Performance (Routing in Hardware/ASIC), keine externe Hardware nötig, skalierbar
**Nachteile:** L3-Switches teurer als L2-Switches + Router

> [!important] **Kernregel**
> Ohne `ip routing` auf dem L3-Switch funktioniert kein Inter-VLAN Routing, auch wenn SVIs konfiguriert sind.

## Variante 3: Separater Router-Port pro VLAN (Legacy)

Jedes VLAN bekommt einen eigenen physischen Router-Port. Selten genutzt (zu viele Ports belegt).

```
Switch VLAN 10 ──Kabel──→ Router Gi0/0 (192.168.10.1)
Switch VLAN 20 ──Kabel──→ Router Gi0/1 (192.168.20.1)
```

## Default-Gateway pro VLAN

Jeder Host braucht das **SVI oder Sub-Interface des jeweiligen VLANs als Default-Gateway**:

```
Host in VLAN 10: IP 192.168.10.50/24, Gateway 192.168.10.1
Host in VLAN 20: IP 192.168.20.50/24, Gateway 192.168.20.1
```

DHCP kann Gateways automatisch vergeben (DHCP-Server pro VLAN oder DHCP-Relay).

## Paketfluss: VLAN 10 → VLAN 20

```
PC-A (VLAN 10, 192.168.10.50) sendet an PC-B (VLAN 20, 192.168.20.50):

1. PC-A: Ziel nicht im eigenen Subnetz → an Default-GW (192.168.10.1) senden
2. PC-A: ARP für 192.168.10.1 → SVI/Sub-Interface antwortet mit MAC
3. Frame mit Tag VLAN 10 → Switch → Router/L3-Switch
4. Router entfernt VLAN-10-Tag, liest Ziel-IP 192.168.20.50
5. Routing-Tabelle: 192.168.20.0/24 ist connected via VLAN 20
6. Router: ARP für 192.168.20.50 → PC-B antwortet
7. Frame mit Tag VLAN 20 → Switch → PC-B
```

> [!warning] **Achtung Falle**
> Das SVI wird nur aktiv (up/up), wenn mindestens **ein Port aktiv im jeweiligen VLAN** ist. Kein aktiver Port im VLAN → SVI bleibt down → kein Routing.
