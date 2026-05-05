[[Routing|zurück]]

---

# L3-Netzwerke & Campus-Netze

Ein Campus-Netz ist das typische Unternehmensnetz, das Büro- und Produktionsgebäude verbindet. Es folgt einem hierarchischen 3-Schichten-Modell.

## Hierarchisches Campus-Modell (Cisco)

```
                    ┌────────────────────────────────┐
                    │         Core Layer              │
                    │  (Hochgeschwindigkeit, kein ACL) │
                    │     Core-Switch / Router         │
                    └────────────┬───────────────────-─┘
                                 │ LWL (10G/40G/100G)
              ┌──────────────────┼──────────────────┐
              │                  │                  │
     ┌────────┴──────┐  ┌────────┴──────┐  ┌────────┴──────┐
     │ Distribution  │  │ Distribution  │  │ Distribution  │
     │    Layer      │  │    Layer      │  │    Layer      │
     │  (Routing,    │  │  (Routing,    │  │  (Routing,    │
     │  ACL, QoS)    │  │  ACL, QoS)   │  │  ACL, QoS)    │
     └──────┬────────┘  └──────┬────────┘  └──────┬────────┘
            │ LWL (1G/10G)     │                  │
     ┌──────┴───────┐   ┌──────┴───────┐   ┌──────┴───────┐
     │ Access Layer │   │ Access Layer │   │ Access Layer │
     │  (L2, VLANs, │   │  (L2, VLANs, │   │  (L2, VLANs, │
     │  PoE, 1G)    │   │  PoE, 1G)    │   │  PoE, 1G)    │
     └──────────────┘   └──────────────┘   └──────────────┘
          Endgeräte          Endgeräte          Endgeräte
```

### Die drei Schichten

| Layer | Funktion | Typische Geräte | Protokolle |
|---|---|---|---|
| **Access** | Endgeräte anschließen, VLANs, PoE, Port Security | L2-Switch | STP/RSTP, 802.1Q, 802.1X |
| **Distribution** | Inter-VLAN Routing, ACLs, QoS, Aggregation | L3-Switch / Router | OSPF, EIGRP, SVI |
| **Core** | Schnelles Forwarding, keine Filterung | High-End L3-Switch | OSPF, BGP, ECMP |

> [!tip] **Merksatz**
> **A**ccess = **A**nschluss. **D**istribution = **D**urchleitung + Filterung. **C**ore = **C**entral/schnell.

## Collapsed Core (2-Tier)

Für kleinere Netze: Distribution und Core werden zusammengelegt:

```
            ┌────────────────────────────┐
            │  Distribution/Core Layer    │
            │  (L3-Switch, OSPF, ACL)     │
            └──────────┬─────────────────┘
                       │
       ┌───────────────┼───────────────┐
       │               │               │
  Access-SW       Access-SW       Access-SW
```

Spart Kosten, typisch für KMU und mittlere Standorte.

## Typische VLAN-Struktur im Campus

| VLAN | Name | Subnetz |
|---|---|---|
| VLAN 1 | (default, ungenutzt lassen) | – |
| VLAN 10 | Mitarbeiter | 10.10.10.0/24 |
| VLAN 20 | Server | 10.20.20.0/24 |
| VLAN 30 | Gäste / WLAN | 10.30.30.0/24 |
| VLAN 40 | VoIP | 10.40.40.0/24 |
| VLAN 99 | Management | 10.99.99.0/24 |
| VLAN 100 | Drucker | 10.100.100.0/24 |

## L3-Routing im Campus

### Distribution-Layer übernimmt Routing

- SVIs für jedes VLAN auf Distribution-L3-Switch
- Access-Switches sind reine L2 → kein IP-Routing
- Default-Route von Distribution → Core

```
Access-SW: kein ip routing, VLANs per Trunk
Distribution-SW:
  ip routing
  interface Vlan10 → ip address 10.10.10.1/24 (Gateway für VLAN 10)
  interface Vlan20 → ip address 10.20.20.1/24
  OSPF → Verbindung zum Core
```

### Redundanz im Campus

- **Dual-Uplinks** vom Access- zum Distribution-Layer → STP/RSTP
- **Dual Distribution-Switches** → Active-Active oder Active-Standby via VRRP/HSRP
- **LACP/EtherChannel** zwischen Distribution und Core

## Subnetz-Design

| Anforderung | Empfehlung |
|---|---|
| pro VLAN ein Subnetz | /24 für Endgeräte (253 Hosts), /25 oder /26 für kleinere VLANs |
| Uplinks zwischen Switches | /30 oder /31 (2 Hosts) |
| Loopback-Interfaces | /32 (1 Host, Router-ID) |
| Management | separates VLAN, restriktive ACLs |

## Best Practices

- VLAN 1 nicht für Nutzerdaten verwenden (Sicherheitsrisiko)
- Native VLAN von VLAN 1 auf ein ungenutztes VLAN ändern
- STP-Root-Bridge explizit auf Distribution-Switches konfigurieren
- PortFast + BPDU Guard auf allen Access-Ports zu Endgeräten
- Management-VLAN nur über Out-of-Band oder gesichertes In-Band erreichbar
- QoS Trust-Grenze am Access-Port setzen (Endgeräten nicht vertrauen)

> [!warning] **Achtung Falle**
> Ohne explizite Root-Bridge-Konfiguration wählt STP die Root Bridge zufällig (niedrigste MAC). Das kann ein billiger Access-Switch werden → alle Pakete fließen über den Access-Switch → Engpass.
