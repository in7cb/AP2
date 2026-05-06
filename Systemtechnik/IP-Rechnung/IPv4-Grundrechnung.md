## Aufbau

- **32 Bit**, aufgeteilt in **4 Oktette** (je 8 Bit)
- Schreibweise: dezimal, Punkt-getrennt → `192.168.1.10`
- Pro Oktett: `0–255` (binär `00000000`–`11111111`)

```text
192     . 168     . 1       . 10
11000000.10101000.00000001.00001010
```

## Subnetzmaske & CIDR

Die **Subnetzmaske** trennt Netzanteil (1er von links) von Hostanteil (0er rechts).

| CIDR | Subnetzmaske | Hosts (nutzbar) |
|---|---|---|
| `/8` | `255.0.0.0` | 16.777.214 |
| `/16` | `255.255.0.0` | 65.534 |
| `/24` | `255.255.255.0` | 254 |
| `/25` | `255.255.255.128` | 126 |
| `/26` | `255.255.255.192` | 62 |
| `/27` | `255.255.255.224` | 30 |
| `/28` | `255.255.255.240` | 14 |
| `/29` | `255.255.255.248` | 6 |
| `/30` | `255.255.255.252` | 2 |

> Formel: **Hosts = 2^(32 − CIDR) − 2**
> (−2: Netz-ID und Broadcast sind nicht für Hosts nutzbar)

## Klassen (historisch)

| Klasse | Bereich | Standardmaske | Verwendung |
|---|---|---|---|
| A | `0.0.0.0`–`127.255.255.255` | `/8` | Sehr große Netze |
| B | `128.0.0.0`–`191.255.255.255` | `/16` | Mittlere Netze |
| C | `192.0.0.0`–`223.255.255.255` | `/24` | Kleine Netze |
| D | `224.0.0.0`–`239.255.255.255` | – | Multicast |
| E | `240.0.0.0`–`255.255.255.255` | – | reserviert / experimentell |

> Heute durch **CIDR** abgelöst – Klassen nur noch zur Orientierung relevant.

## Netz-ID, Broadcast, Hostrange berechnen

**Beispiel:** `172.16.45.130/26`

1. Maske `/26` = `255.255.255.192` → letztes Oktett: `11000000` → **Blockgröße = 64**
2. Netz-ID = größter Vielfacher von 64 ≤ 130 → `172.16.45.128`
3. Broadcast = Netz-ID + Blockgröße − 1 → `172.16.45.191`
4. Erste Hostadresse: `172.16.45.129`
5. Letzte Hostadresse: `172.16.45.190`
6. Nutzbare Hosts: `2^6 − 2 = 62`

```text
Netz-ID:     172.16.45.128
First Host:  172.16.45.129
Last Host:   172.16.45.190
Broadcast:   172.16.45.191
```

> [!tip] **Merksatz**
> Maske im letzten Oktett betrachten → Blockgröße = 256 − Maske → Netz-ID liegt auf Vielfachem der Blockgröße.
