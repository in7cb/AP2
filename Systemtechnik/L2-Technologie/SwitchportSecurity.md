Switchport-Security schützt Access-Ports vor unautorisierten Geräten, MAC-Flooding-Angriffen und nicht autorisierten Netzwerkzugängen auf Layer 2.

## Port Security (Cisco)

Port Security beschränkt, welche MAC-Adressen an einem Port kommunizieren dürfen.

### Grundkonfiguration

```bash
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport port-security                            ! aktivieren
 switchport port-security maximum 2                 ! max. 2 MACs erlaubt
 switchport port-security mac-address sticky        ! MACs automatisch lernen & merken
 switchport port-security violation shutdown        ! Verstoß → Port deaktivieren
```

### MAC-Adress-Arten

| Typ | Beschreibung |
|---|---|
| **Static** | Manuell konfiguriert (`mac-address XXXX.XXXX.XXXX`) |
| **Dynamic** | Automatisch gelernt, **nicht** in Startup-Config gespeichert |
| **Sticky** | Automatisch gelernt **und** in Startup-Config gespeichert |

> [!tip] **Merksatz**
> Sticky = dynamisch + persistent. Beim Neustart bleibt die gelernte MAC erhalten.

### Violation-Modi

| Modus | Reaktion bei Verstoß | Port-Status | Log | SNMP-Trap |
|---|---|---|---|---|
| **Shutdown** | Port → err-disabled | ❌ down | ✅ | ✅ |
| **Restrict** | Frames verwerfen, Port bleibt oben | ✅ up | ✅ | ✅ |
| **Protect** | Frames verwerfen, Port bleibt oben | ✅ up | ❌ | ❌ |

> [!important] **Kernregel**
> Standard-Violation-Modus ist **Shutdown**. Ein err-disabled Port muss manuell oder via `errdisable recovery` reaktiviert werden.

### Err-Disabled Recovery

```bash
# Manuell reaktivieren
interface GigabitEthernet0/1
 shutdown
 no shutdown

# Automatisch nach Timeout
errdisable recovery cause psecure-violation
errdisable recovery interval 300          ! 300 Sekunden warten
```

## 802.1X Port-Based Authentication

802.1X verhindert Netzwerkzugang ohne Authentifizierung – kein VLAN, keine IP, bis der User/Gerät sich ausgewiesen hat.

### Komponenten

```
Supplicant (Client) ←── EAP ───→ Authenticator (Switch) ←── RADIUS ───→ Authentication Server
```

| Rolle | Beschreibung |
|---|---|
| **Supplicant** | Client (PC, IP-Telefon) mit 802.1X-Software |
| **Authenticator** | Switch-Port – kontrolliert Zugang |
| **Authentication Server** | RADIUS-Server (z. B. FreeRADIUS, Cisco ISE) |

### Port-Zustände bei 802.1X

- **Unauthorized State:** Nur EAP-Traffic erlaubt, alles andere geblockt
- **Authorized State:** Voller Netzwerkzugang nach erfolgreicher Authentifizierung

```bash
# 802.1X global aktivieren (Cisco)
aaa new-model
aaa authentication dot1x default group radius
dot1x system-auth-control

interface GigabitEthernet0/1
 authentication port-control auto
 dot1x pae authenticator
```

## DHCP Snooping

DHCP Snooping verhindert Rogue DHCP-Server im Netz.

```
Trusted Port: Uplink zum echten DHCP-Server (DHCP Offers erlaubt)
Untrusted Port: Access-Ports zu Clients (nur DHCP Requests erlaubt)
```

```bash
ip dhcp snooping
ip dhcp snooping vlan 10,20

interface GigabitEthernet0/24     ! Uplink → trusted
 ip dhcp snooping trust

interface GigabitEthernet0/1      ! Client → untrusted (Standard)
 ip dhcp snooping limit rate 15   ! max. 15 DHCP-Pakete/s
```

> [!warning] **Achtung Falle**
> DHCP Snooping muss auch auf **Trunk-Ports zum echten DHCP-Server** als trusted gesetzt sein – sonst werden legitime DHCP Offers verworfen.

## Dynamic ARP Inspection (DAI)

DAI baut auf DHCP Snooping auf und verhindert ARP-Spoofing:
- Validiert ARP-Pakete gegen die DHCP Snooping Binding Database
- Gefälschte ARP Replies (falsche IP-MAC-Zuordnung) werden verworfen

```bash
ip arp inspection vlan 10

interface GigabitEthernet0/24
 ip arp inspection trust           ! Uplink → trusted
```

## IP Source Guard

Verhindert IP-Spoofing auf Layer 2 – erlaubt nur Frames mit der IP/MAC aus der DHCP Snooping Binding Table.

```bash
interface GigabitEthernet0/1
 ip verify source
```

## Übersicht Angriffe & Gegenmaßnahmen

| Angriff | Gegenmaßnahme |
|---|---|
| MAC Flooding (CAM-Table voll) | Port Security |
| Rogue DHCP-Server | DHCP Snooping |
| ARP Spoofing / MitM | Dynamic ARP Inspection |
| IP Spoofing | IP Source Guard |
| Unauth. Gerät im Netz | 802.1X |
| VLAN Hopping (native VLAN) | Native VLAN ändern, nicht VLAN 1 |
