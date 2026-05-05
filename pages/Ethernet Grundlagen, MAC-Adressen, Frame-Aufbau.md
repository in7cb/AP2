[[L2-Technologie|zurück]]

---

# Ethernet – Grundlagen, MAC-Adressen, Frame-Aufbau

Ethernet ist der dominierende LAN-Standard (IEEE 802.3) auf Layer 2. Es definiert, wie Daten als Frames über ein gemeinsames Medium übertragen werden.

## MAC-Adresse

Eine MAC-Adresse (Media Access Control) ist eine 48-Bit-Hardware-Adresse, die weltweit eindeutig einem Netzwerkinterface zugeordnet ist.

```
Beispiel:  AA:BB:CC:DD:EE:FF
           ├──────────┤ ├──────────┤
             OUI          NIC-spezifisch
           (Hersteller)    (Geräteanteil)
```

| Bit | Bedeutung |
|---|---|
| Bit 0 (LSB im ersten Byte) | 0 = Unicast, 1 = Multicast |
| Bit 1 im ersten Byte | 0 = globally unique (OUI), 1 = locally administered |

**Sonderadressen:**
- `FF:FF:FF:FF:FF:FF` → Broadcast (alle im selben Segment)
- `01:00:5E:xx:xx:xx` → IPv4 Multicast
- `33:33:xx:xx:xx:xx` → IPv6 Multicast

## Ethernet Frame-Aufbau (IEEE 802.3)

```
┌──────────┬──────────┬──────┬──────────┬─────┬─────┐
│ Preamble │ SFD      │ Dst  │ Src      │Typ/ │Daten│ FCS
│ 7 Byte   │ 1 Byte   │ MAC  │ MAC      │Len  │     │ 4 Byte
│          │          │6 Byte│6 Byte    │2 B  │46–  │
│10101010…│10101011  │      │          │     │1500B│
└──────────┴──────────┴──────┴──────────┴─────┴─────┘
```

| Feld | Größe | Bedeutung |
|---|---|---|
| Preamble | 7 Byte | Synchronisation (101010...) |
| SFD (Start Frame Delimiter) | 1 Byte | 10101011 – Beginn des Frames |
| Destination MAC | 6 Byte | Ziel-MAC-Adresse |
| Source MAC | 6 Byte | Quell-MAC-Adresse |
| EtherType / Length | 2 Byte | ≥0x0800 = Typ (z. B. IPv4), <0x0600 = Länge |
| Payload (Daten) | 46–1500 Byte | Nutzdaten (min. 46 Byte wegen CSMA/CD) |
| FCS | 4 Byte | CRC-32 Prüfsumme |

> [!tip] **Merksatz**
> **P**lease **S**end **D**ata **S**oon – **P**reamble, **S**FD, **D**st, **S**rc, (Type), Data, FCS

## EtherType – wichtige Werte

| Wert | Protokoll |
|---|---|
| `0x0800` | IPv4 |
| `0x0806` | ARP |
| `0x86DD` | IPv6 |
| `0x8100` | 802.1Q VLAN Tag |
| `0x8847` | MPLS |

## MTU und Jumbo Frames

- **Standard-MTU:** 1500 Byte (Payload ohne Header)
- **Jumbo Frames:** MTU bis 9000 Byte – nur bei Geräten, die es explizit unterstützen
- Reduziert CPU-Overhead bei großen Datenübertragungen (Storage, VMs)

> [!warning] **Achtung Falle**
> MTU bezieht sich auf die **Payload** (1500 Byte), nicht auf den gesamten Frame. Der Frame ist mit Headern und FCS größer (max. 1518 Byte, mit 802.1Q 1522 Byte).

## CSMA/CD (nur historisch relevant)

Collision Sense Multiple Access / Collision Detection – war bei Half-Duplex Ethernet nötig:
1. Lauschen ob Medium frei
2. Senden
3. Kollision erkannt → Jam-Signal, zufällige Wartezeit (Binary Exponential Backoff)
4. Erneut senden

Bei Full-Duplex und Switches heute **nicht mehr relevant**.
