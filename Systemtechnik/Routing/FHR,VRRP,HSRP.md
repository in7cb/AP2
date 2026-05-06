FHR-Protokolle (First Hop Redundancy Protocols) stellen sicher, dass Endgeräte auch beim Ausfall ihres Default-Gateways weiterhin ins Netz kommen – durch einen automatischen Failover auf einen Backup-Router.

## Das Problem ohne FHR

```
Host: Gateway = 192.168.1.1 (Router A)
Router A fällt aus → Host kann keine Pakete mehr routen
→ manuelle Änderung des Gateways nötig (nicht praktikabel)
```

FHR-Protokolle lösen das durch eine **virtuelle IP/MAC**, die immer erreichbar ist.

## Konzept: Virtual IP & Virtual MAC

```
Router A  ──┐
            ├──── Virtuelle IP: 192.168.1.1 (Virtual Router)
Router B  ──┘

Host: Gateway = 192.168.1.1 (virtual)
→ egal welcher physische Router aktiv ist, die IP bleibt gleich
```

- **Virtual IP (VIP):** die IP, die Hosts als Gateway nutzen
- **Virtual MAC:** wird für ARP-Antworten genutzt (je nach Protokoll unterschiedlich)
- **Master/Active:** der Router, der aktuell antwortet
- **Backup/Standby:** der Router, der bei Ausfall übernimmt

## VRRP – Virtual Router Redundancy Protocol

Offener Standard (RFC 5798), unterstützt von allen Herstellern.

| Merkmal | VRRP v2 (IPv4) | VRRP v3 (IPv4 + IPv6) |
|---|---|---|
| Standard | RFC 3768 | RFC 5798 |
| Multicast | `224.0.0.18` | `224.0.0.18` / `FF02::12` |
| IP-Protokoll | 112 | 112 |
| Virtual MAC | `00:00:5E:00:01:<VRID>` | `00:00:5E:00:02:<VRID>` |
| Priorität | 1–254 (default 100) | 1–254 |
| Preemption | Standard: an | Standard: an |

### VRRP Konfiguration (Cisco IOS)

```bash
! Router A (Master, höhere Priorität)
interface GigabitEthernet0/0
 ip address 192.168.1.2 255.255.255.0
 vrrp 1 ip 192.168.1.1           ! virtuelle IP (VIP)
 vrrp 1 priority 110             ! höher als default 100 → Master
 vrrp 1 preempt                  ! bei Rückkehr wieder Master werden

! Router B (Backup)
interface GigabitEthernet0/0
 ip address 192.168.1.3 255.255.255.0
 vrrp 1 ip 192.168.1.1
 vrrp 1 priority 100             ! niedrigere Prio → Backup
```

```bash
show vrrp brief
! Interface   Grp  Pri  Timer Own  Pre  State   Master addr     Group addr
! Gi0/0       1    110  1     N    Y    Master  192.168.1.2     192.168.1.1
```

## HSRP – Hot Standby Router Protocol

Cisco-proprietär (RFC 2281 für HSRPv1, RFC 7430 für HSRPv2). Weit verbreitet in Cisco-Umgebungen.

| Merkmal | HSRPv1 | HSRPv2 |
|---|---|---|
| Gruppen | 0–255 | 0–4095 |
| Multicast | `224.0.0.2` | `224.0.0.102` |
| Virtual MAC | `00:00:0C:07:AC:<Gruppe>` | `00:00:0C:9F:F0:xx` |
| IPv6 | ❌ | ✅ |
| Priorität | 0–255 (default 100) | 0–255 (default 100) |

### HSRP Zustände

```
Initial → Learn → Listen → Speak → Standby → Active
```

| Zustand | Beschreibung |
|---|---|
| **Active** | Beantwortet VIP-Traffic, sendet Hellos |
| **Standby** | Überwacht Active, bereit zum Übernehmen |
| **Listen** | Kennt VIP, aber weder Active noch Standby |

### HSRP Konfiguration (Cisco IOS)

```bash
! Router A (Active)
interface GigabitEthernet0/0
 ip address 192.168.1.2 255.255.255.0
 standby version 2
 standby 1 ip 192.168.1.1        ! VIP
 standby 1 priority 110          ! höher → Active
 standby 1 preempt               ! bei Rückkehr Active werden
 standby 1 track GigabitEthernet0/1 20   ! Uplink überwachen, bei Ausfall Prio -20

! Router B (Standby)
interface GigabitEthernet0/0
 ip address 192.168.1.3 255.255.255.0
 standby version 2
 standby 1 ip 192.168.1.1
 standby 1 priority 100
 standby 1 preempt
```

```bash
show standby brief
! Interface   Grp  Prio P State    Active       Standby      Virtual IP
! Gi0/0       1    110  P Active   local        192.168.1.3  192.168.1.1
```

## HSRP Interface Tracking

Wenn der Uplink (z. B. WAN) von Router A ausfällt, soll Router B übernehmen:

```bash
! Prio sinkt bei Ausfall von Gi0/1 um 20
standby 1 track GigabitEthernet0/1 20

! Router A: Prio 110 - 20 = 90 → niedriger als Router B (100) → B übernimmt
```

## GLBP – Gateway Load Balancing Protocol

Cisco-proprietär. Erweitert HSRP/VRRP um **aktives Load Balancing**:
- Alle Router sind gleichzeitig aktiv (nicht nur einer)
- Hosts bekommen via ARP verschiedene Virtual MACs → Traffic verteilt auf alle Gateways

## Vergleich FHR-Protokolle

| Merkmal | HSRP | VRRP | GLBP |
|---|---|---|---|
| Standard | Cisco | offen (RFC) | Cisco |
| IPv6 | v2 | v3 | ✅ |
| Load Balancing | ❌ | ❌ | ✅ |
| Preemption default | aus | **an** | an |
| Active/Master Router | einer | einer | alle |

> [!important] **Kernregel**
> VRRP = offener Standard, HSRP = Cisco. Beide lösen dasselbe Problem. In Cisco-only-Netzen HSRP üblich, in gemischten Umgebungen VRRP bevorzugen.

> [!warning] **Achtung Falle**
> HSRP Preemption ist **standardmäßig deaktiviert**. Ohne `standby X preempt` bleibt nach einem Ausfall und Rückkehr des eigentlich priorisierten Routers der Backup Active. Immer `preempt` konfigurieren!
