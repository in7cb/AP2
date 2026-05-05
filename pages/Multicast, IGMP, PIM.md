[[L3, L4-Technologie|zurück]]

---

# Multicast, IGMP & PIM

Multicast ermöglicht es, Daten gleichzeitig an eine Gruppe von Empfängern zu senden – effizienter als mehrfach Unicast, ohne den Broadcast-Overhead.

## Multicast vs. Unicast vs. Broadcast

| Typ | Empfänger | Effizienz | Skalierung |
|---|---|---|---|
| **Unicast** | genau 1 | gut bei 1:1 | schlecht bei 1:n (n × Traffic) |
| **Broadcast** | alle im Segment | schlecht | nicht routbar |
| **Multicast** | Gruppe (opt-in) | gut bei 1:n | routbar, skalierbar |

## IPv4 Multicast-Adressen

| Bereich | Beschreibung |
|---|---|
| `224.0.0.0/4` | Gesamter IPv4-Multicast-Bereich (224.0.0.0–239.255.255.255) |
| `224.0.0.0/24` | Link-Local (nicht routbar) – OSPF, EIGRP, VRRP, etc. |
| `224.0.1.0–238.255.255.255` | Globally Scoped (routbar) |
| `239.0.0.0/8` | Administrativ Scoped (private Multicast, wie RFC 1918) |

### Bekannte Link-Local Multicast-Adressen

| Adresse | Protokoll |
|---|---|
| `224.0.0.1` | All Hosts (alle Hosts im Segment) |
| `224.0.0.2` | All Routers |
| `224.0.0.5` | OSPF All Routers |
| `224.0.0.6` | OSPF DR/BDR |
| `224.0.0.9` | RIPv2 |
| `224.0.0.18` | VRRP |
| `224.0.0.102` | HSRP |

## Layer-2 Multicast-Mapping

IPv4-Multicast-Adressen werden auf MAC-Adressen gemappt:

```
IPv4 Multicast: 224.X.Y.Z
MAC:            01:00:5E:0X:Y:Z  (letzten 23 Bit der IP)

Beispiel:
IP:  239.1.2.3
     ↓
MAC: 01:00:5E:01:02:03
```

> [!warning] **Achtung Falle**
> Das Mapping ist nicht eindeutig! Das oberste Bit der IP-Gruppe (Bit 24) wird nicht übertragen → 32 verschiedene IP-Multicast-Gruppen können auf dieselbe MAC gemappt werden. Switches müssen IGMP Snooping nutzen, sonst werden Multicast-Frames wie Broadcasts geflutet.

## IGMP – Internet Group Management Protocol (IPv4)

IGMP läuft zwischen Hosts und dem nächsten Multicast-Router. Hosts melden sich damit für Multicast-Gruppen an.

| Version | RFC | Eigenschaften |
|---|---|---|
| **IGMPv1** | RFC 1112 | nur Join, kein Leave |
| **IGMPv2** | RFC 2236 | Leave Group Nachricht, schnelleres Verlassen |
| **IGMPv3** | RFC 3376 | Source-spezifischer Multicast (SSM) – nur von bestimmten Quellen empfangen |

### IGMPv2 Ablauf

```
Host                              Router
  │── IGMP Membership Report ────→│  (Join Gruppe 239.1.2.3)
  │← IGMP General Query ──────────│  (alle 60s: Wer will noch was?)
  │── IGMP Membership Report ────→│  (Antwort: ich noch)
  │── IGMP Leave Group ──────────→│  (Abmeldung)
  │← IGMP Group-Specific Query ───│  (Noch jemand anderes?)
  │ (kein Report) 2s warten        │
  │                                 │  Gruppe abgebaut
```

### IGMP Snooping

Switches "lauschen" IGMP-Nachrichten und leiten Multicast-Frames nur an Ports weiter, auf denen sich Empfänger befinden – statt zu fluten.

```bash
ip igmp snooping               ! Cisco: global aktivieren (meist Default)
show ip igmp snooping groups   ! Mitglieder anzeigen
```

## PIM – Protocol Independent Multicast

PIM ist das Routing-Protokoll für Multicast zwischen Routern. Es ist "protokollunabhängig", weil es beliebige Unicast-Routing-Tabellen nutzt.

### PIM-Modi

| Modus | Beschreibung | Einsatz |
|---|---|---|
| **PIM Dense Mode (DM)** | Flood & Prune – sendet überall hin, dann zurückschneiden | kleine Netze, viele Empfänger |
| **PIM Sparse Mode (SM)** | Shared Tree über Rendezvous Point (RP) | große Netze, wenige Empfänger |
| **PIM SSM** | Source-Specific Multicast – direkt zur Quelle | IPTV, moderne Deployments |

### PIM Sparse Mode – Ablauf

```
Sender → Quell-Router → RP (Rendezvous Point) → Empfänger-Router → Empfänger
```

1. Sender sendet Multicast-Paket → nächster Router registriert es beim RP
2. Empfänger-Router meldet Interesse beim RP (via IGMP)
3. RP verbindet Sender und Empfänger über Shared Tree
4. Optional: Empfänger-Router wechselt auf Shortest Path Tree (SPT) direkt zur Quelle

> [!tip] **Merksatz**
> PIM SM braucht einen **RP (Rendezvous Point)** als zentralen Treffpunkt. Ohne RP kein Multicast-Routing im SM.

## IPv6 Multicast

IPv6 verwendet nur Multicast (kein Broadcast). Adressen beginnen mit `FF`:

```
FF02::1     All nodes (link-local)
FF02::2     All routers (link-local)
FF02::5     OSPFv3 all routers
FF02::1:FF00:0/104  Solicited-Node Multicast (für NDP)
```

Für IPv6 ersetzt **MLD (Multicast Listener Discovery)** das IGMP, läuft über ICMPv6.

## Anwendungen von Multicast

| Anwendung | Protokoll | Beschreibung |
|---|---|---|
| IPTV | UDP Multicast | Hunderte TV-Kanäle ohne n×Unicast |
| Videokonferenz | RTP über UDP | Gruppen-Video |
| Routing-Protokolle | OSPF, EIGRP | Nutzen Multicast für Updates |
| VRRP / HSRP | Multicast / Broadcast | Gateway-Redundanz |
| mDNS (Bonjour) | 224.0.0.251 | Lokale Diensterkennung (Apple, etc.) |
