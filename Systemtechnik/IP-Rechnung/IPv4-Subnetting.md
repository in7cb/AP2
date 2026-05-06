Großes Netz in kleinere Subnetze aufteilen – spart Adressen, reduziert Broadcast-Domains, ermöglicht Trennung (z.B. nach Abteilung, VLAN).

## Warum subnetten?

- **Sicherheit / Segmentierung** – verschiedene VLANs, ACLs zwischen Netzen
- **Performance** – kleinere Broadcast-Domains
- **Adressökonomie** – nur so viele Adressen pro Subnetz wie wirklich nötig (besonders mit VLSM)
- **Hierarchie** – sauberes Routing mit Summarization

## VLSM (Variable Length Subnet Mask)

Unterschiedlich große Subnetze aus einem Netz herausschneiden.

> **Regel:** Immer mit dem **größten** benötigten Subnetz beginnen, dann absteigend.

## Schritt-für-Schritt-Anleitung

1. Benötigte Hosts pro Subnetz auflisten und absteigend sortieren
2. Pro Subnetz CIDR finden: kleinstes /n mit `2^(32−n) − 2 ≥ Hosts`
3. Subnetze blockweise vergeben (jeweils ab nächstem freien Vielfachen)
4. Netz-ID, Broadcast, Hostrange notieren

## Beispiel

Gegeben: `192.168.10.0/24` – aufteilen für:
- Vertrieb: 50 Hosts
- Buchhaltung: 25 Hosts
- IT: 10 Hosts
- Druckernetz: 5 Hosts

| Abt. | Hosts | CIDR | Netz-ID | First | Last | Broadcast |
|---|---|---|---|---|---|---|
| Vertrieb | 50 | `/26` (62 nutzbar) | `192.168.10.0` | `.1` | `.62` | `.63` |
| Buchhaltung | 25 | `/27` (30 nutzbar) | `192.168.10.64` | `.65` | `.94` | `.95` |
| IT | 10 | `/28` (14 nutzbar) | `192.168.10.96` | `.97` | `.110` | `.111` |
| Drucker | 5 | `/29` (6 nutzbar) | `192.168.10.112` | `.113` | `.118` | `.119` |

Rest (`.120` – `.255`) bleibt für spätere Erweiterungen.

## Schnell-Tabelle (Blockgrößen letztes Oktett)

| CIDR | Maske letztes Oktett | Blockgröße | Hosts |
|---|---|---|---|
| /24 | 0 | 256 | 254 |
| /25 | 128 | 128 | 126 |
| /26 | 192 | 64 | 62 |
| /27 | 224 | 32 | 30 |
| /28 | 240 | 16 | 14 |
| /29 | 248 | 8 | 6 |
| /30 | 252 | 4 | 2 |

> [!tip] **Merksatz**
> Blockgröße = `256 − Wert des Maskenoktetts`. Netz-IDs liegen auf Vielfachen der Blockgröße. Innerhalb eines Subnetzes: erste Adresse = Netz-ID, letzte = Broadcast, alles dazwischen = Hosts.

## Häufige Prüfungsfallen

- `/31` und `/32` sind Sonderfälle (Point-to-Point bzw. Host-Route) → 0 nutzbare Hosts nach Standardformel
- VLSM-Aufgaben: **größtes Subnetz zuerst** vergeben
- Broadcast einer Maske mit kleinerem Block immer korrekt mit Blockgröße rechnen, nicht raten
