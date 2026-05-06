Ports identifizieren Dienste/Anwendungen auf einem Host. Zusammen mit IP-Adresse und Protokoll (TCP/UDP) bilden sie ein **Socket** (5-Tupel: SrcIP, SrcPort, DstIP, DstPort, Protokoll).

## Port-Bereiche

| Bereich | Ports | Bezeichnung | Vergabe |
|---|---|---|---|
| **Well-Known Ports** | 0–1023 | System Ports | IANA zugewiesen, Root-Rechte nötig |
| **Registered Ports** | 1024–49151 | User Ports | IANA registriert, kein Root nötig |
| **Dynamic / Ephemeral Ports** | 49152–65535 | Private Ports | Client-Ports (zufällig, temporär) |

> [!tip] **Merksatz**
> 0–1023 = bekannte Dienste (brauchen Admin). 1024–49151 = registrierte Apps. 49152–65535 = temporäre Client-Ports.

## Wichtige Well-Known Ports

| Port | Protokoll | Dienst |
|---|---|---|
| **20** | TCP | FTP (Daten, Active Mode) |
| **21** | TCP | FTP (Steuerung) |
| **22** | TCP | SSH |
| **23** | TCP | Telnet |
| **25** | TCP | SMTP (Mail-Versand) |
| **53** | TCP + UDP | DNS |
| **67** | UDP | DHCP Server |
| **68** | UDP | DHCP Client |
| **69** | UDP | TFTP |
| **80** | TCP | HTTP |
| **110** | TCP | POP3 |
| **123** | UDP | NTP |
| **143** | TCP | IMAP |
| **161** | UDP | SNMP (Agent) |
| **162** | UDP | SNMP (Trap/Manager) |
| **179** | TCP | BGP |
| **389** | TCP | LDAP |
| **443** | TCP | HTTPS |
| **445** | TCP | SMB / Windows File Sharing |
| **514** | UDP | Syslog |
| **520** | UDP | RIP |
| **546** | UDP | DHCPv6 Client |
| **547** | UDP | DHCPv6 Server |
| **587** | TCP | SMTP (Submission / Auth) |
| **636** | TCP | LDAPS (LDAP über TLS) |
| **993** | TCP | IMAPS (IMAP über TLS) |
| **995** | TCP | POP3S (POP3 über TLS) |

## Wichtige Registered Ports

| Port | Protokoll | Dienst |
|---|---|---|
| **1194** | UDP/TCP | OpenVPN |
| **1433** | TCP | Microsoft SQL Server |
| **1521** | TCP | Oracle DB |
| **1701** | UDP | L2TP |
| **1723** | TCP | PPTP |
| **1812** | UDP | RADIUS Authentication |
| **1813** | UDP | RADIUS Accounting |
| **3306** | TCP | MySQL / MariaDB |
| **3389** | TCP | RDP (Remote Desktop Protocol) |
| **4500** | UDP | IPsec NAT-Traversal |
| **5060** | UDP/TCP | SIP (VoIP Signaling) |
| **5061** | TCP | SIP (TLS) |
| **5246** | UDP | CAPWAP (WLAN Controller Control) |
| **5247** | UDP | CAPWAP (WLAN Controller Data) |
| **5432** | TCP | PostgreSQL |
| **8080** | TCP | HTTP Alt (Proxy, Dev-Server) |
| **8443** | TCP | HTTPS Alt |

## Socket-Konzept

```
Verbindung zwischen zwei Hosts:
Client 192.168.1.10:54321 ──TCP──→ Server 10.0.0.1:443

5-Tupel:
  SrcIP:   192.168.1.10
  SrcPort: 54321 (ephemeral)
  DstIP:   10.0.0.1
  DstPort: 443
  Proto:   TCP
```

Jede gleichzeitige Verbindung hat einen eindeutigen Source-Port → ein Browser kann 100 parallele HTTPS-Verbindungen zu demselben Server öffnen.

## Firewall-Regeln & Ports

Firewalls arbeiten typischerweise auf Port-Basis:

```
ALLOW TCP 0.0.0.0/0 → 10.0.0.5:443    ! HTTPS erlauben
ALLOW UDP 0.0.0.0/0 → 10.0.0.5:53     ! DNS erlauben
DENY  ALL                               ! alles andere blockieren
```

> [!warning] **Achtung Falle**
> DNS nutzt **sowohl** TCP als auch UDP auf Port 53. UDP für normale Anfragen (<512 Byte), TCP für Zone Transfers und große Antworten (>512 Byte oder wenn EDNS0 nicht unterstützt).

> [!important] **Kernregel**
> Well-Known Ports auswendig lernen: die häufigsten in der Prüfung sind FTP (20/21), SSH (22), Telnet (23), SMTP (25), DNS (53), DHCP (67/68), HTTP (80), HTTPS (443), RDP (3389).
