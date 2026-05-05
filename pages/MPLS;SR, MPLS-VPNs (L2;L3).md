[[WAN-Technik|zurück]]

---

# MPLS, Segment Routing & MPLS-VPNs

**MPLS** (Multiprotocol Label Switching) ist eine Provider-Technik, die Pakete nicht nach IP-Routing-Entscheidungen, sondern anhand kurzer Labels im Netzwerk weiterleitet – schneller und mit Traffic-Engineering-Möglichkeiten.

## MPLS-Grundprinzip

Zwischen IP-Header und L2-Header wird ein **Label-Stack** eingefügt (Layer 2.5):

```text
[Ethernet-Header] [MPLS-Label] [IP-Header] [Payload]
```

**Label-Felder:**

| Feld | Bits | Bedeutung |
|---|---|---|
| Label | 20 | Weiterleitungs-ID |
| TC (Traffic Class) | 3 | QoS (früher: EXP) |
| S (Bottom of Stack) | 1 | 1 = letztes Label im Stack |
| TTL | 8 | wie IP-TTL |

### Rollen im MPLS-Netz

| Router-Typ | Kürzel | Funktion |
|---|---|---|
| Label Edge Router (Ingress) | LER | Paket empfangen, Label aufdrücken (Push) |
| Label Switch Router | LSR | Label tauschen (Swap), weiterleiten |
| Label Edge Router (Egress) | LER | Label entfernen (Pop), Paket normal weiterleiten |

```mermaid
graph LR
    CE1[CE Kunde A] --> LER_IN[LER Ingress\nPush Label 100]
    LER_IN --> LSR1[LSR\nSwap 100→200]
    LSR1 --> LSR2[LSR\nSwap 200→300]
    LSR2 --> LER_OUT[LER Egress\nPop Label]
    LER_OUT --> CE2[CE Kunde A]
```

Der Pfad durch das MPLS-Netz heißt **LSP** (Label Switched Path).

> [!tip] **Merksatz**
> **P**ush → **S**wap → **P**op: Ingress drückt auf, Transit tauscht, Egress nimmt ab.

## Label Distribution – LDP & RSVP-TE

| Protokoll | Verwendung | Besonderheit |
|---|---|---|
| LDP | Standard-LSPs | Hop-by-Hop, folgt dem IGP-Pfad |
| RSVP-TE | Traffic Engineering | Pfad explizit reserviert, Bandbreitengarantien möglich |

## MPLS-VPNs

### L3VPN (MPLS Layer-3-VPN, RFC 4364)

Kunden-Routing läuft über VRF-Instanzen (Virtual Routing and Forwarding) auf den PE-Routern. Jeder Kunde hat eine eigene, isolierte Routing-Tabelle.

**Beteiligte Geräte:**

| Begriff | Bedeutung |
|---|---|
| CE | Customer Edge – Kundenrouter (kein MPLS) |
| PE | Provider Edge – VRF + MPLS |
| P | Provider Core Router – nur MPLS-Transit |

```text
CE-A1 ──── PE-1 [VRF A] ──── P ──── PE-2 [VRF A] ──── CE-A2
CE-B1 ──── PE-1 [VRF B] ──── P ──── PE-2 [VRF B] ──── CE-B2
```

**Route Distinguisher (RD):** Macht Kunden-Routen global eindeutig im BGP (`64512:100` + Präfix).
**Route Target (RT):** Steuert Import/Export von VRF-Routen zwischen PEs (via MP-BGP).

**Label-Stack bei L3VPN:**
```text
[L2] [Transport-Label (P→PE)] [VPN-Label (PE→CE-Netz)] [IP]
```

> [!important] **Kernregel**
> L3VPN = 2 Labels: äußeres für den Transport durch den Provider-Core, inneres für die VRF/Kundenidentifikation auf dem Egress-PE.

### L2VPN – Pseudowire & VPLS

**L2VPN** transportiert Layer-2-Frames über das MPLS-Netz – der Kunde sieht seine Standorte als direkt verbundene L2-Segmente.

| Typ | Beschreibung |
|---|---|
| Pseudowire (PWE3) | Punkt-zu-Punkt L2-Verbindung über MPLS (z.B. Ethernet, HDLC) |
| VPLS | Virtual Private LAN Service – Multipoint-L2 (alle Standorte in einem VLAN) |
| EVPN | Ethernet VPN – moderner Nachfolger von VPLS, nutzt BGP-Control-Plane |

> [!warning] **Achtung Falle**
> L2VPN vs. L3VPN: Bei L2VPN routet der Kunde selbst (CE–CE auf Layer 2), beim L3VPN übernimmt der PE das Routing (PE kennt Kunden-Routen).

## Segment Routing (SR)

Segment Routing ist der moderne Nachfolger von MPLS Traffic Engineering – kein RSVP mehr nötig, der Pfad wird im Paket selbst kodiert.

**Prinzip:** Der Ingress-Node drückt eine geordnete Liste von **Segments** (= MPLS-Labels oder IPv6-Adressen) auf das Paket. Jeder Transit-Node folgt dem nächsten Segment.

| Variante | Transport | Segment-Typ |
|---|---|---|
| SR-MPLS | MPLS-Labels | Node-SID, Adj-SID |
| SRv6 | IPv6-Extension-Header | 128-Bit Segment IDs |

**Vorteile gegenüber RSVP-TE:**
- Kein Zustandserhalt auf Transit-Nodes (stateless)
- Einfacheres Operations-Modell
- Nativ SDN-fähig (Controller berechnet Pfade, drückt SID-Stack auf Ingress)

> [!tip] **Merksatz**
> SR = "GPS-Navigation im Netz" – der Ingress bekommt die komplette Route vorgegeben, Transit-Nodes folgen einfach der Karte ohne eigene Routenentscheidung.
