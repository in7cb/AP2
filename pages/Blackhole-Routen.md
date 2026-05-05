[[Routing|zurück]]

---

# Blackhole-Routen (Null-Routen)

Eine Blackhole-Route leitet Pakete bewusst ins Nichts – das Paket wird verworfen, ohne dass eine ICMP-Fehlermeldung zurückgesendet wird (silent drop). Das Ziel-Interface ist eine virtuelle Null-Schnittstelle (`Null0`).

## Warum Blackhole-Routen?

1. **Routing-Schleifen verhindern:** Verhindert, dass Pakete endlos zwischen Routern hin- und herspringen, wenn eine spezifischere Route fehlt.
2. **DDoS-Mitigation (RTBH):** Angreifer-Traffic gezielt verwerfen – ohne Firewalls zu belasten.
3. **Aggregierte Routen absichern:** Wenn ein aggregierter Präfix in OSPF/BGP angekündigt wird, aber nicht alle Teil-Subnetze existieren → Blackhole für den Aggregat verhindert Routing-Loops.

## Konfiguration (Cisco IOS)

```bash
! Pakete an 192.0.2.0/24 stillschweigend verwerfen
ip route 192.0.2.0 255.255.255.0 Null0

! Mit AD 254 (damit spezifischere Routen gewinnen können)
ip route 192.0.2.0 255.255.255.0 Null0 254

! IPv6
ipv6 route 2001:db8:dead::/48 Null0
```

```bash
! Überprüfen
show ip route 192.0.2.0
! S     192.0.2.0/24 is directly connected, Null0
```

## Typischer Anwendungsfall: Aggregat + Blackhole

```
Router kündigt 10.0.0.0/8 via OSPF an (Aggregat).
Intern existieren aber nur:
  10.1.0.0/16 (vorhanden)
  10.2.0.0/16 (vorhanden)
  10.3.0.0/16 (nicht vorhanden!)

Ohne Blackhole: Paket an 10.3.x.x → kein Match → Default-Route → Internet → Loop!

Mit Blackhole:
  ip route 10.0.0.0 255.0.0.0 Null0    ← AD 254 (niedrig spezifisch)
  OSPF-Routen für 10.1/10.2 haben AD 110 → gewinnen für diese Subnetze
  10.3.x.x passt auf Blackhole (keine spezifischere Route) → silent drop ✅
```

> [!important] **Kernregel**
> Blackhole-Route immer mit höherer AD als das dynamische Routing-Protokoll konfigurieren, damit spezifischere dynamische Routen Vorrang haben.

## RTBH – Remotely Triggered Blackhole

BGP-basierte Methode zur schnellen DDoS-Mitigation:

```
Angriff auf 203.0.113.50/32

1. Netz-Operator trägt Blackhole-Route für 203.0.113.50/32 ein
2. Route wird via iBGP an alle Edge-Router propagiert (mit Community-Tag)
3. Alle Edge-Router verwerfen Traffic an 203.0.113.50 an ihren Uplinks
→ Angriff wird bereits am Rand des eigenen Netzes absorbiert
```

**uRPF + Blackhole:** Unicast Reverse Path Forwarding mit Blackhole-Routen für Spoofed-Source-IPs.

## Null0 vs. Verwerfen mit ICMP

| Verhalten | Interface | ICMP zurück? |
|---|---|---|
| Blackhole (silent drop) | `Null0` | ❌ nein |
| Reject (mit ICMP) | `Reject` (Linux) / ACL deny | ✅ ja |

Cisco kennt nur `Null0` (kein ICMP). Linux-Router können mit `ip route add blackhole` oder `ip route add prohibit` (mit ICMP) unterscheiden.

## Unterschied zu anderen "Drop"-Mechanismen

| Mechanismus | Ebene | Rückmeldung | Zweck |
|---|---|---|---|
| Blackhole-Route | L3 Routing | keine | Routing-Loop-Schutz, DDoS |
| ACL deny | L3/L4 Filter | optional (ICMP) | Zugriffskontrolle |
| Firewall block | L3–L7 | optional | Sicherheitsrichtlinie |
| Port Security Violation | L2 | Err-Disabled | MAC-Kontrolle |

> [!tip] **Merksatz**
> Null0 = der Papierkorb des Routers. Traffic rein, nichts kommt raus, keine Antwort.
