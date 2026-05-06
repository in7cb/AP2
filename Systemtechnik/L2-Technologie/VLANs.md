VLANs trennen ein physisches Switch-Netz in mehrere logische Broadcast-Domains – ohne separate Hardware. Grundlage: **IEEE 802.1Q**.

## Warum VLANs?

- **Sicherheit:** Abteilungen (z. B. HR, IT) logisch trennen
- **Performance:** Broadcast-Domänen verkleinern → weniger Broadcast-Traffic
- **Flexibilität:** Geräte einem VLAN zuordnen unabhängig vom physischen Port

## 802.1Q VLAN-Tag

```
Ethernet Frame mit 802.1Q Tag:
┌──────┬──────┬──────────┬──────────┬─────┐
│ Dst  │ Src  │  0x8100  │ PCP|DEI|VID │Typ  │
│ MAC  │ MAC  │ (TPID)   │ 3b  1b 12b  │/Len │
└──────┴──────┴──────────┴──────────┴─────┘
```

- **TPID:** `0x8100` → markiert Frame als 802.1Q-getaggt
- **PCP (3 Bit):** CoS/802.1p Priorität (0–7)
- **DEI (1 Bit):** Drop Eligible Indicator (bei Überlast zuerst verwerfen)
- **VID (12 Bit):** VLAN-ID 1–4094 (0 und 4095 reserviert)

> [!tip] **Merksatz**
> 12 Bit VID → 2¹² = 4096 mögliche VLANs → nutzbar: 1–4094

## Port-Typen

| Typ | Beschreibung | Tagging |
|---|---|---|
| **Access-Port** | Endgerät (PC, IP-Telefon) – genau ein VLAN | untagged (Tag wird entfernt/hinzugefügt) |
| **Trunk-Port** | Switch-zu-Switch oder Switch-zu-Router – mehrere VLANs | tagged (Tag bleibt im Frame) |
| **Hybrid-Port** | Mischung (z. B. Voice VLAN + Data VLAN am selben Port) | tagged + untagged |

### Access-Port (Cisco)

```bash
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 10
```

### Trunk-Port (Cisco)

```bash
interface GigabitEthernet0/24
 switchport mode trunk
 switchport trunk encapsulation dot1q
 switchport trunk allowed vlan 10,20,30
 switchport trunk native vlan 99
```

## Native VLAN

Das Native VLAN auf einem Trunk wird **untagged** übertragen. Standard: VLAN 1.

> [!warning] **Achtung Falle**
> Native VLAN muss auf **beiden Seiten** eines Trunks identisch konfiguriert sein. Mismatches führen zu VLAN-Hopping-Schwachstellen (Angreifer sendet doppelt getaggte Frames).

## VLAN-Datenbank anlegen (Cisco)

```bash
vlan 10
 name Mitarbeiter
vlan 20
 name Server
vlan 99
 name Management
```

## Inter-VLAN Routing

VLANs sind Layer-2-Konstrukte – Kommunikation zwischen VLANs braucht einen Router oder Layer-3-Switch:

- **Router-on-a-Stick:** Router mit Sub-Interfaces, ein physischer Uplink
- **SVI (Switched Virtual Interface):** Layer-3-Switch mit IP-Adresse pro VLAN

→ Details: [[Inter-VLAN Routing]]

## Voice VLAN

IP-Telefone bekommen ein separates Voice VLAN (höhere Priorität, QoS):

```bash
interface GigabitEthernet0/5
 switchport mode access
 switchport access vlan 10       ! Data VLAN für den PC hinter dem Telefon
 switchport voice vlan 20        ! Voice VLAN für das Telefon
```

> [!important] **Kernregel**
> VLANs trennen Broadcast-Domains auf Layer 2. Ohne Router/L3-Switch kein Datenfluss zwischen VLANs.
