**Virtual Extensible LAN (VXLAN)** ist ein Overlay-Protokoll, das L2-Frames in UDP/IP kapselt, um VLANs über L3-Netze hinweg zu strecken – der Standard im modernen Rechenzentrum.

## Motivation

- Klassische VLANs: **max. 4094** (12-Bit VLAN-ID)
- VXLAN: **~16,7 Mio. Segmente** (24-Bit VNI)
- Problem mit VLANs: können nicht über L3-Router-Grenzen hinweg (ohne spezielle Tricks)
- VXLAN löst das durch Tunneling: L2 sieht aus wie L3

## Kernbegriffe

| Begriff | Bedeutung |
|---|---|
| **VNI** | VXLAN Network Identifier (24 Bit) – entspricht der VLAN-ID, aber viel größer |
| **VTEP** | VXLAN Tunnel Endpoint – das Gerät, das kapselt/entkapselt (physischer Switch, vSwitch, VM-Host) |
| **BUM-Traffic** | Broadcast, Unknown Unicast, Multicast – muss im VXLAN-Fabric verteilt werden |

## Encapsulation – Paketaufbau

```text
┌────────────────┬──────────────┬──────────────┬───────────────┬──────────────┐
│ Outer Ethernet │  Outer IP    │  Outer UDP   │ VXLAN Header  │ Inner Frame  │
│ (VTEP MACs)    │ (VTEP IPs)   │ Port 4789    │  (VNI 24bit)  │ (orig. L2)   │
└────────────────┴──────────────┴──────────────┴───────────────┴──────────────┘
```

- **Outer IP**: Quell-/Ziel-IP der VTEPs (Underlay-Adressen)
- **UDP-Port 4789**: IANA-Standard für VXLAN
- **VXLAN-Header**: 8 Byte, enthält den 24-Bit VNI
- **Inner Frame**: originaler L2-Ethernet-Frame (mit VLAN-Tag optional)

Overhead: **50 Byte** pro Paket (Outer ETH 14 + IP 20 + UDP 8 + VXLAN 8)

> [!warning] **Achtung Falle**
> VXLAN-Overhead erhöht die effektive Paketgröße. Bei MTU 1500 Byte auf dem Underlay muss die MTU für VXLAN mindestens **1550 Byte** betragen (Jumbo Frames empfohlen: MTU 9000). Sonst Fragmentierung → Performance-Einbruch!

## VTEP – Tunnel Endpoint

VTEPs können auf verschiedenen Geräten laufen:

| VTEP-Typ | Gerät | Einsatz |
|---|---|---|
| **Hardware-VTEP** | physischer Switch (Leaf) | RZ-Fabric |
| **Software-VTEP** | Linux/OVS, vSwitch (VMware, Hyper-V) | Hypervisor |
| **Container-VTEP** | Flannel, Calico, Cilium | Kubernetes-Networking |

## BUM-Traffic-Handling

Bei VXLAN gibt es kein natives Broadcast im Underlay (L3). BUM-Traffic wird behandelt via:

| Methode | Beschreibung |
|---|---|
| **Multicast** | Alle VTEPs treten Multicast-Gruppe bei – einfach, aber Multicast-Infrastruktur nötig |
| **Ingress Replication / Headend** | Sender-VTEP schickt BUM-Paket als Unicast an alle anderen VTEPs (Unicast-Flood) |
| **BGP EVPN** | BGP verteilt MAC/IP-Info → kein BUM-Traffic mehr nötig (moderner Standard) |

## BGP EVPN – Control Plane für VXLAN

**EVPN (Ethernet VPN)** ist die bevorzugte Control-Plane für VXLAN in großen Fabrics:

- VTEPs lernen MAC/IP-Zuordnungen via **BGP EVPN Route-Types** (kein Flooding)
- Zentrale, skalierbare Verteilung
- Unterstützt L2 und L3 VPN gleichzeitig
- Standard in **Cisco ACI**, **VMware NSX**, **Cumulus**, **Arista EOS**

```text
VTEP-A lernt VM-MAC via BGP EVPN → weiß direkt, wohin es tunneln muss
→ kein Broadcast, keine unbekannten Unicasts mehr
```

> [!tip] **Merksatz**
> VXLAN = **L2 in UDP** (Port 4789), VNI statt VLAN-ID, VTEP kapselt/entkapselt. 24-Bit VNI = 16 Mio. Segmente statt 4094.

> [!important] **Kernregel**
> VXLAN streckt L2-Netze über L3. Outer-IP = Underlay (VTEPs), Inner-Frame = Original-Traffic. MTU beachten! BGP EVPN ist die skalierbare Control Plane dazu.
