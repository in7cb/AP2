Eine Floating-Route ist eine statische Route mit einer **künstlich erhöhten Administrative Distance (AD)**, die nur dann aktiv wird, wenn die bevorzugte Route ausfällt – ähnlich einem Standby-Link.

## Konzept

Normale statische Routen haben AD=1 und verdrängen dynamisch gelernte Routen. Floating-Routen bekommen eine AD **höher** als die des dynamischen Protokolls → sie tauchen in der Routing-Tabelle **nicht auf**, solange die dynamische Route existiert.

```
Primäre Route:  OSPF 10.0.0.0/8 via Gi0/0  [AD=110]  ← aktiv
Floating-Route: Static 10.0.0.0/8 via Gi0/1 [AD=150]  ← inaktiv (höhere AD)

OSPF-Neighbor fällt aus:
→ OSPF-Route verschwindet aus Tabelle
→ Floating-Route [AD=150] wird eingetragen → Backup-Pfad aktiv ✅
```

> [!important] **Kernregel**
> AD der Floating-Route muss **höher** sein als die AD des dynamischen Protokolls, das sie sichern soll (OSPF=110 → Floating-Route AD=150 o. ä.).

## Konfiguration (Cisco IOS)

```bash
! Primärer Pfad: OSPF lernt 10.0.0.0/8 via Gi0/0 (AD=110, automatisch)

! Floating-Route: Backup via Gi0/1 mit AD=150
ip route 10.0.0.0 255.0.0.0 192.168.2.1 150
!                                         ^^^
!                             Administrative Distance (höher als OSPF=110)

! Default-Route als Floating-Route (Backup-ISP)
ip route 0.0.0.0 0.0.0.0 203.0.113.2 200   ! AD=200, höher als eBGP=20
```

### Überprüfen

```bash
show ip route
! Solange OSPF-Route aktiv: Floating-Route nicht sichtbar!

show ip route 10.0.0.0
! S    10.0.0.0/8 [150/0] via 192.168.2.1  ← erscheint nur wenn OSPF-Route weg
```

## Typische Einsatzszenarien

### Dual-ISP Failover

```
ISP 1 (Primär): eBGP Default-Route  → AD=20  (aktiv)
ISP 2 (Backup): Floating-Route      → AD=200 (standby)

ISP 1 fällt aus:
→ BGP-Route verschwindet
→ Floating-Route AD=200 → Traffic über ISP 2
```

### OSPF + statischer Backup-Link

```
Normalbetrieb:     OSPF     über LAN (AD=110, schnell)
Backup (DSL/LTE):  Floating  AD=150 → nur aktiv wenn LAN-Weg fällt
```

### Redundante WAN-Links

```
MPLS-Link (primär):  EIGRP AD=90
Internet-VPN (Backup): Floating-Route AD=180
```

## AD-Werte als Referenz für Floating-Routes

| Zu sicherndes Protokoll | AD | Floating-Route AD |
|---|---|---|
| eBGP | 20 | 21–254 |
| EIGRP internal | 90 | 91–254 |
| OSPF | 110 | 111–254 |
| IS-IS | 115 | 116–254 |
| RIP | 120 | 121–254 |
| iBGP | 200 | 201–254 |

> [!warning] **Achtung Falle**
> AD=255 bedeutet "diese Route niemals verwenden". Floating-Routen müssen AD **≤ 254** haben.

## Floating vs. Blackhole vs. Default-Route

| Typ | Zweck | AD |
|---|---|---|
| **Default-Route** | Catch-All für unbekannte Ziele | 1 (statisch) |
| **Blackhole-Route** | Traffic gezielt verwerfen | variabel (oft 254) |
| **Floating-Route** | Backup für dynamische Route | > dynamisches Protokoll |

> [!tip] **Merksatz**
> Floating = "schwimmend" – die Route wartet im Hintergrund und taucht nur auf, wenn der Hauptweg versinkt.
