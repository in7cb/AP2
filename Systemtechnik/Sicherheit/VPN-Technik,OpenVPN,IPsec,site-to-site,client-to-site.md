VPN (Virtual Private Network) schafft einen **verschlüsselten Tunnel** über ein unsicheres Netz (meist Internet) und verbindet entfernte Netze oder Clients sicher miteinander.

## VPN-Szenarien

| Szenario | Verbindet | Typischer Einsatz |
|---|---|---|
| **Site-to-Site** | Netzwerk ↔ Netzwerk | Filiale ↔ Zentrale (dauerhaft) |
| **Client-to-Site** (Remote Access) | Einzelner Client ↔ Netzwerk | Homeoffice, Außendienst |
| **Client-to-Client** (Peer-to-Peer) | Einzelner Client ↔ Client | Selten, z.B. Gaming, WireGuard-Mesh |

```text
Site-to-Site:
[Filiale-LAN] ── [VPN-GW] ══IPsec-Tunnel══ [VPN-GW] ── [Zentrale-LAN]

Client-to-Site:
[Laptop] ══OpenVPN/IPsec-Tunnel══ [VPN-GW] ── [Firmen-LAN]
```

## IPsec

IPsec ist ein **Protokoll-Framework** auf OSI Layer 3, das IP-Pakete schützt.

### IPsec-Komponenten

| Protokoll/Konzept | Funktion |
|---|---|
| **AH** (Authentication Header) | Integrität + Authentizität, **keine** Verschlüsselung |
| **ESP** (Encapsulating Security Payload) | Integrität + Authentizität + **Verschlüsselung** |
| **IKE** (Internet Key Exchange) | Schlüsselaushandlung und SA-Aufbau |
| **SA** (Security Association) | Unidirektionaler Sicherheitskontext (je Richtung eine SA) |

**In der Praxis:** Fast immer **ESP** (wegen Verschlüsselung), AH wird kaum noch eingesetzt.

### IPsec-Modi

| Modus | Schützt | Verwendung |
|---|---|---|
| **Transport-Modus** | Nur IP-Payload (nicht den IP-Header) | Host-to-Host, L2TP/IPsec |
| **Tunnel-Modus** | Gesamtes IP-Paket (neuer äußerer Header) | Site-to-Site, Client-to-Site |

```text
Tunnel-Modus:
┌────────────────────────────────────────────────────────────┐
│ Neuer IP-Header │ ESP-Header │ [Original-IP │ Payload] enc │ ESP-Trailer │
└────────────────────────────────────────────────────────────┘
                              ↑ verschlüsselt ↑
```

### IKE-Phasen

```text
Phase 1 (IKE SA):
  – Aushandlung: Verschlüsselung, Hash, DH-Gruppe, Authentifizierung
  – Aufbau eines sicheren Kanals für Phase 2
  – Methoden: Main Mode (sicherer) oder Aggressive Mode (schneller)

Phase 2 (IPsec SA):
  – Aushandlung der eigentlichen IPsec-Parameter (ESP/AH, Algorithmen)
  – Quick Mode
  – Ergebnis: zwei SAs (eine je Richtung)
```

**IKEv2:** Modernere Version – weniger Round-Trips, MOBIKE (Mobility), einfacher

### Ports/Protokolle

```text
IKE:         UDP 500
NAT-T:       UDP 4500  (wenn NAT auf dem Weg)
ESP:         IP-Protokoll 50  (kein TCP/UDP!)
AH:          IP-Protokoll 51
```

## OpenVPN

SSL/TLS-basiertes VPN – läuft auf **User Space**, nutzt TLS für Tunnel-Aufbau.

| Merkmal | Wert |
|---|---|
| Protokoll | TLS (über TCP oder UDP) |
| Port | typisch UDP 1194 (konfigurierbar, z.B. TCP 443 für Firewalls) |
| Verschlüsselung | AES-256-GCM |
| Authentifizierung | Zertifikate (PKI), Pre-Shared Key, Username/Passwort |
| OS-Unterstützung | Linux, Windows, macOS, Android, iOS |

**Vorteile:** Flexibel, läuft auf TCP 443 (schwer zu blocken), sehr gut dokumentiert  
**Nachteile:** Langsamer als IPsec (User Space), kein nativer OS-Support (Client nötig)

## WireGuard (Modern, kurz)

- Sehr modernes, schlankes VPN-Protokoll (< 4000 Zeilen Code)
- **Statisch konfigurierte Public Keys** (kein IKE)
- Nur UDP, Port 51820 (konfigurierbar)
- Deutlich schneller als OpenVPN und IPsec
- In Linux-Kernel integriert (seit 5.6)

## Vergleich

| | IPsec | OpenVPN | WireGuard |
|---|---|---|---|
| Layer | L3 | L3 (über TLS) | L3 |
| Geschwindigkeit | schnell | mittel | sehr schnell |
| Firewall-Durchdringung | schwierig (ESP, UDP 500) | einfach (TCP 443) | mittel (UDP) |
| Komplexität | hoch | mittel | niedrig |
| Standard | ✅ IETF-Standard | OSS | OSS |
| Site-to-Site | ✅ | ✅ | ✅ |
| Client-to-Site | ✅ (IKEv2/EAP) | ✅ | ✅ |

> [!tip] **Merksatz**
> **IPsec = Industrie-Standard** (Cisco, Juniper, Firewalls), **OpenVPN = flexibel & feuerwall-freundlich**, **WireGuard = modern & schnell**. Für die Prüfung: IPsec und OpenVPN kennen.

> [!warning] **Achtung Falle**
> IPsec ESP ist **kein TCP/UDP** – es ist ein eigenes IP-Protokoll (Nummer 50). NAT macht damit Probleme → deshalb **NAT-Traversal (NAT-T)** auf UDP 4500.

> [!important] **Kernregel**
> - **Tunnel-Modus:** schützt das gesamte Originalpaket inkl. IP-Header → für Site-to-Site
> - **Transport-Modus:** schützt nur den Payload → für End-to-End (Host-to-Host)
