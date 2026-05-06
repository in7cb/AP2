In IPv6 wird **fast immer auf Nibble-Grenzen** (jeweils 4 Bit / 1 Hex-Zeichen) subnettet, weil das Rechnen so trivial bleibt. /64 ist der Standard für Endnutzer-Subnetze (Pflicht für **SLAAC**).

## Übliche Präfix-Hierarchie

| Präfix | Verwendung |
|---|---|
| `/32` | Großer ISP |
| `/48` | Kunde / Standort (Default-Zuteilung pro Org) |
| `/56` | Kleinere Zuteilung (z.B. Privatkunde mit Subnetz-Bedarf) |
| `/64` | **Standard-Endnutzer-Subnetz** (1 LAN / 1 VLAN) |
| `/127` | P2P-Link zwischen Routern (analog IPv4 `/30`) |
| `/128` | Einzelner Host (Loopback) |

## Vorgehen

1. **Subnet-Bits** = Ziel-Präfix − Start-Präfix
2. **Anzahl Subnetze** = `2^Subnet-Bits`
3. Im Hex inkrementieren – Schritt hängt davon ab, an welcher Stelle die Subnet-Bits liegen

## Saubere Subnet-Schnitte (Nibble-Grenzen)

| Schnitt | Subnet-Bits | Subnetze | Inkrement im 4. Hextet |
|---|---|---|---|
| `/48` → `/52` | 4 | 16 | `1000` |
| `/48` → `/56` | 8 | 256 | `0100` |
| `/48` → `/60` | 12 | 4 096 | `0010` |
| `/48` → `/64` | 16 | 65 536 | `0001` |
| `/56` → `/64` | 8 | 256 | `0001` |
| `/60` → `/64` | 4 | 16 | `0001` |

## Beispiel 1: `2001:db8:1234::/60` → `/64`

```text
4. Hextet (binär):   SSSS  (4 Subnet-Bits)
                     ↓
                     0000 = 0
                     0001 = 1
                     ...
                     1111 = f
```

```text
2001:db8:1234:0000::/64   (Subnetz 0)
2001:db8:1234:0001::/64   (Subnetz 1)
2001:db8:1234:0002::/64   (Subnetz 2)
2001:db8:1234:0003::/64   (Subnetz 3)
...
2001:db8:1234:000f::/64   (Subnetz 15)
```

→ **16 Subnetze**, jedes /64.

## Beispiel 2: `2001:db8:1234::/56` → `/64`

8 Subnet-Bits frei → **256 Subnetze**.

```text
2001:db8:1234:0000::/64
2001:db8:1234:0100::/64
2001:db8:1234:0200::/64
...
2001:db8:1234:ff00::/64
```

> Achtung: Hier wird die **dritte Hex-Stelle** des 4. Hextets inkrementiert (nicht die letzte!), weil die ersten 8 Bits des 4. Hextets der Subnet-Anteil sind.

## Beispiel 3: `/60` → `/63`, **4 Subnetze ausgewählt** (Unterrichtsaufgabe)

Aufgabentyp: aus `2001:db8:1234::/60` sollen **4 Subnetze auf /63** gebildet werden.

> /60 → /63 hätte rechnerisch **8 mögliche /63-Subnetze**. Wenn die Aufgabe explizit **4 Subnetze** verlangt, wählt man 4 davon aus – z.B. die ersten 4 mit Schritt 2 (gleichmäßige Verteilung im Adressraum).

```text
4. Hextet (binär): SSSx  → S = Subnet-Bit (3 Bits, da /63), x = restliches Bit pro /63

Subnetz 1:  0000 |00|0  →  2001:db8:1234:0000::/63
Subnetz 2:  0000 |01|0  →  2001:db8:1234:0002::/63
Subnetz 3:  0000 |10|0  →  2001:db8:1234:0004::/63
Subnetz 4:  0000 |11|0  →  2001:db8:1234:0006::/63
```

Notation `0000 |00|0` = die 4 freien Bits aus /60 → /64, davon 3 als Subnet-Bits markiert (mittlerer `|..|`-Block), das letzte Bit gehört in den /63-Hostanteil.

> [!info] **Wenn 4 Subnetze die Vorgabe sind**, wäre rein mathematisch `/62` die "passende" Wahl (genau 4 Subnetze: 0, 4, 8, c). Wenn der Lehrer trotzdem `/63` vorgibt, will er sehen, dass du das Bit-Layout verstehst – dass also nur 4 von 8 möglichen /63-Subnetzen genutzt werden.

> [!warning] **Achtung:** Bei /63 ist SLAAC nicht mehr möglich (braucht /64). /63 wird praktisch nur für Router-zu-Router-Punkt-zu-Punkt-Links genutzt – heute meist sogar **/127** (RFC 6164).

## Adresstypen-Beispiele (aus Notizen)

```text
::1/128                              → Loopback
2001:db8:1234::1/64                  → Global Unicast
fe80::ea62:6ff:fe01:45/64            → Link-Local (aus MAC ea-62-06-01-00-45 via EUI-64)
```

> [!tip] **Merksatz**
> Subnetten auf **Nibble-Grenzen** (/48, /52, /56, /60, /64) macht die Hex-Arithmetik trivial. Jedes zusätzliche Hex-Zeichen im Subnetting bedeutet 16x mehr Subnetze. Endnutzer-Subnetz immer /64, P2P-Links auf /127.
