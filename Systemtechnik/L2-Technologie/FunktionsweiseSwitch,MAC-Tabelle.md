Ein Switch arbeitet auf OSI-Layer 2 und leitet Frames anhand von MAC-Adressen gezielt an den richtigen Port weiter – im Gegensatz zum Hub, der Frames an alle Ports flutet.

## Lernprozess (MAC Address Learning)

Beim Empfang eines Frames liest der Switch die **Quell-MAC** aus und trägt sie zusammen mit dem eingehenden Port in die MAC-Tabelle (auch: CAM-Table) ein.

```
Frame kommt an Port 3 von MAC AA:BB:CC:DD:EE:FF
→ Switch lernt: AA:BB:CC:DD:EE:FF → Port 3
```

| Schritt | Aktion |
|---|---|
| 1. Empfang | Frame trifft an Port X ein |
| 2. Learning | Quell-MAC + Port X → MAC-Tabelle |
| 3. Lookup | Ziel-MAC in Tabelle suchen |
| 4a. Known Unicast | Frame nur an Ziel-Port weiterleiten |
| 4b. Unknown Unicast | Frame an alle Ports fluten (außer Eingangsport) |
| 4c. Broadcast/Multicast | Frame an alle Ports fluten |

## MAC-Tabelle (CAM-Table)

| MAC-Adresse | Port | VLAN | Age (s) |
|---|---|---|---|
| AA:BB:CC:DD:EE:FF | Gi0/1 | 10 | 180 |
| 11:22:33:44:55:66 | Gi0/2 | 10 | 42 |

- **Aging-Time:** Standard 300 Sekunden – danach wird der Eintrag gelöscht
- **Maximale Einträge:** Hardwareabhängig (z. B. 8.000–128.000 Einträge)
- Bei vollem CAM-Table: Switch flutet alle Frames → **MAC Flooding Angriff** nutzt das aus

## Switching-Modi (Store-and-Forward vs. Cut-Through)

| Modus | Beschreibung | Latenz | Fehlerprüfung |
|---|---|---|---|
| **Store-and-Forward** | Kompletten Frame puffern, dann FCS prüfen | höher | ✅ Ja |
| **Cut-Through** | Weiterleitung nach Ziel-MAC (nach 6 Byte) | sehr gering | ❌ Nein |
| **Fragment-Free** | Weiterleitung nach 64 Byte (Collision-Fragment sicher) | mittel | teilweise |

> [!tip] **Merksatz**
> Store-and-Forward = sicher aber langsam. Cut-Through = schnell aber blindes Weiterleiten.

## Full-Duplex vs. Half-Duplex

- **Full-Duplex:** Senden und Empfangen gleichzeitig – kein CSMA/CD nötig, heute Standard
- **Half-Duplex:** Nur eine Richtung gleichzeitig – CSMA/CD aktiv (nur noch bei Hubs / alten Geräten)

## Broadcast-Domain vs. Collision-Domain

| Konzept | Switch | Hub | Router |
|---|---|---|---|
| Collision-Domain | pro Port eine eigene | eine für alle | pro Interface eine |
| Broadcast-Domain | eine für alle Ports (ohne VLAN) | eine für alle | pro Interface eine |

> [!important] **Kernregel**
> Ein Switch trennt Collision-Domains, aber **nicht** Broadcast-Domains. Das übernehmen VLANs oder Router.

## Managed vs. Unmanaged Switch

| Merkmal | Unmanaged Switch | Managed Switch |
|---|---|---|
| Konfiguration | keine – Plug & Play | über CLI (SSH/Telnet), Web-GUI oder SNMP |
| VLANs | ❌ | ✅ |
| QoS (Priorisierung) | ❌ | ✅ |
| Port-Security | ❌ | ✅ |
| ACLs | ❌ | ✅ |
| Spanning Tree | meist fix | konfigurierbar (STP/RSTP/MSTP) |
| SNMP/Monitoring | ❌ | ✅ |
| Kosten | günstig | teurer |
| Einsatz | Heimnetz, einfache Büros | Unternehmensnetze, Rechenzentren |

> [!important] **Kernregel**
> Ein **Managed Switch** bietet volle Konfigurierbarkeit (VLANs, QoS, ACLs, Port-Security, Monitoring) – ein **Unmanaged Switch** leitet Frames ohne jede Konfiguration weiter.

## Wichtige CLI-Befehle (Cisco IOS)

```bash
show mac address-table              # gesamte MAC-Tabelle anzeigen
show mac address-table dynamic      # nur dynamisch gelernte Einträge
clear mac address-table dynamic     # Tabelle leeren (flush)
show interfaces gi0/1               # Port-Status, Duplex, Speed
```
