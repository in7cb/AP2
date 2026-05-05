[[Sicherheit|zurück]]

---

# DMZ (Demilitarisierte Zone)

Eine DMZ ist ein **abgetrenntes Netzwerksegment** zwischen dem internen LAN und dem Internet, das öffentlich erreichbare Dienste hostet, ohne das interne Netz direkt zu exponieren.

## Zweck

- Öffentlich erreichbare Dienste (Web, Mail, DNS) sollen zugänglich sein
- Bei Kompromittierung eines DMZ-Servers soll das interne LAN **nicht direkt erreichbar** sein
- Separation of Concerns: DMZ-Hosts kennen interne Systeme nicht (oder nur eingeschränkt)

## Single-Firewall-DMZ (Three-Legged)

Einfachste Variante: eine Firewall mit drei Interfaces.

```text
Internet
    │
    │
┌───┴───────────────────┐
│       Firewall        │
│  eth0  eth1    eth2   │
└───┬────┬──────────┬───┘
    │    │          │
  Internet  DMZ    LAN
           │          │
      Web/Mail      Clients
       DNS          Server
```

**Vorteil:** günstig, ein Gerät  
**Nachteil:** Firewall ist Single Point of Failure; bei Kompromittierung der Firewall ist alles offen

## Dual-Firewall-DMZ (empfohlen)

Zwei separate Firewalls – höhere Sicherheit durch Tiefenverteidigung.

```text
Internet
    │
┌───┴───┐
│  FW1  │  (Outer Firewall / Edge Firewall)
└───┬───┘
    │
   DMZ
 ┌──┴──────────────────┐
 │ Web-Server          │
 │ Mail-Server         │
 │ DNS-Resolver (pub.) │
 └──┬──────────────────┘
    │
┌───┴───┐
│  FW2  │  (Inner Firewall / Internal Firewall)
└───┬───┘
    │
   LAN (intern)
 ┌──┴────────────────────┐
 │ Clients               │
 │ Interne Server        │
 │ Datenbanken           │
 └───────────────────────┘
```

**FW1 (Outer):** erlaubt HTTP/HTTPS/SMTP/DNS aus Internet zur DMZ  
**FW2 (Inner):** erlaubt nur spezifische Verbindungen von DMZ ins LAN (z.B. DB-Port)

## Typische Dienste in der DMZ

| Dienst | Port(s) | Begründung |
|---|---|---|
| Web-Server (HTTP/HTTPS) | 80, 443 | muss öffentlich erreichbar sein |
| Mail-Server (SMTP eingehend) | 25 | empfängt externe Mails |
| DNS-Resolver (autoritativ) | 53 | antwortet auf externe Anfragen |
| Reverse Proxy | 443 | leitet zu internen Backends durch |
| VPN-Gateway | 500, 4500 (IPsec) | Endpunkt für externe VPN-Clients |

## Regelwerk-Prinzip

| Quelle | Ziel | Erlaubt |
|---|---|---|
| Internet | DMZ (Web) | TCP 80, 443 |
| Internet | DMZ (Mail) | TCP 25 |
| DMZ | LAN (DB) | TCP 3306 (nur vom Web-Server!) |
| LAN | DMZ | Alle (für Admin-Zugriff) |
| DMZ | Internet | TCP 80, 443 (Updates), SMTP out |
| Internet | LAN | ❌ Alles verboten |
| DMZ | LAN (beliebig) | ❌ Verboten |

> [!tip] **Merksatz**
> DMZ = **Vorraum der Burg**: Besucher kommen bis in den Vorraum (DMZ), aber nicht in die Innenräume (LAN). Kompromittierter Gast ist eingesperrt.

> [!warning] **Achtung Falle**
> Niemals direkte Verbindungen **Internet → LAN** erlauben, auch nicht "nur für einen Port". Alles läuft über die DMZ als Zwischenstufe.

> [!important] **Kernregel**
> Prinzip der minimalen Rechte: Ein DMZ-Server bekommt **nur** die Ports freigegeben, die er für seinen Dienst braucht. Kein generelles „allow any".
