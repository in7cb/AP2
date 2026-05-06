Eine Firewall kontrolliert den Netzwerkverkehr anhand von Regeln und blockiert oder erlaubt Pakete basierend auf verschiedenen Kriterien.

## Paketfilter (Stateless Firewall)

Prüft jedes Paket **isoliert**, ohne Verbindungskontext.

**Filterkriterien:**
- Quell-IP / Ziel-IP
- Quell-Port / Ziel-Port
- Protokoll (TCP, UDP, ICMP)
- Interface (eingehend/ausgehend)

```text
Paket → Regelwerk (erste passende Regel gewinnt) → PERMIT oder DENY
```

**Vorteil:** sehr schnell, wenig Ressourcen  
**Nachteil:** kennt keinen Verbindungsstatus → kann Antwortpakete nicht automatisch zuordnen, leicht täuschbar (IP-Spoofing)

## Stateful Firewall

Führt eine **Connection-Tracking-Tabelle** – kennt den Zustand jeder Verbindung.

| Zustand | Bedeutung |
|---|---|
| NEW | Erstes Paket einer neuen Verbindung |
| ESTABLISHED | Verbindung wurde aufgebaut |
| RELATED | Zusammenhängende Verbindung (z.B. FTP-Datenkanal) |
| INVALID | Paket passt zu keiner bekannten Verbindung |

```text
Client → SYN → Firewall → trägt in State-Tabelle ein → Server
Server → SYN-ACK → Firewall → prüft State-Tabelle → ESTABLISHED → Client
```

**Vorteil:** Antwortenverkehr wird automatisch erlaubt (kein symmetrisches Regelwerk nötig)  
**Nachteil:** State-Tabelle kostet Speicher, bei DDoS erschöpfbar

## ACLs (Access Control Lists)

ACLs sind die konkrete Implementierung von Paketfilter-Regeln, v.a. auf Routern/Switches (Cisco-Syntax):

```text
# Standard-ACL (nur Quell-IP):
access-list 10 permit 192.168.1.0 0.0.0.255
access-list 10 deny any

# Extended-ACL (Quelle, Ziel, Protokoll, Port):
access-list 100 permit tcp 10.0.0.0 0.0.0.255 any eq 443
access-list 100 permit tcp 10.0.0.0 0.0.0.255 any eq 80
access-list 100 deny ip any any
```

**Wichtige ACL-Regeln:**
- Regeln werden **von oben nach unten** abgearbeitet, erste Übereinstimmung gewinnt
- Am Ende immer implizites **„deny all"** (auch wenn nicht explizit angegeben)
- **Wildcard-Maske** (Cisco): Umkehrung der Subnetzmaske (`255.255.255.0` → `0.0.0.255`)

## Deep Packet Inspection (DPI)

DPI analysiert nicht nur Header, sondern auch den **Nutzdateninhalt** (Payload):

| Fähigkeit | Beschreibung |
|---|---|
| Protokollerkennung | Erkennt Protokoll unabhängig vom Port (z.B. BitTorrent auf Port 80) |
| Content-Filterung | Sperrt Websites nach Inhaltskategorien |
| Malware-Detection | Scannt Dateiinhalte auf Signaturen |
| IDS/IPS-Integration | Intrusion Detection/Prevention in Echtzeit |
| SSL Inspection | Entschlüsselt TLS (MITM-Proxy) für Analyse |

**Einsatz:** Next-Generation Firewalls (NGFW), UTM-Appliances  
**Nachteil:** hoher Rechenaufwand, Privacy-Bedenken bei SSL Inspection

## ALG (Application Layer Gateway)

ALGs verstehen spezifische Protokolle auf Applikationsebene und passen Verbindungen dynamisch an:

| Protokoll | Problem | ALG-Lösung |
|---|---|---|
| FTP (aktiv) | Server öffnet Verbindung zurück zum Client (Firewall blockiert) | ALG erkennt PORT-Befehl, öffnet temporär den Rückkanal |
| SIP (VoIP) | RTP-Ports werden im SIP-Body ausgehandelt | ALG liest SIP, öffnet dynamisch die RTP-Ports |
| TFTP | UDP, keine Verbindungsaushandlung | ALG kennt TFTP-Verhalten |

## Firewall-Typen im Vergleich

| Typ | Layer | Stärke | Schwäche |
|---|---|---|---|
| Paketfilter | L3/L4 | schnell, einfach | kein State, leicht täuschbar |
| Stateful Firewall | L3/L4 | Connection-Tracking | kein App-Verständnis |
| Application Firewall (WAF) | L7 | Versteht HTTP/SQL/… | nur ein Protokoll |
| NGFW (Next-Gen) | L3–L7 | DPI, IPS, SSL Inspect | teuer, rechenintensiv |
| UTM | L3–L7 | All-in-One | Single Point of Failure |

> [!tip] **Merksatz**
> **Stateless = Einzelbild** (jedes Paket für sich), **Stateful = Film** (kennt den ganzen Ablauf). Heute fast immer Stateful.

> [!warning] **Achtung Falle**
> ACL-Regeln werden **sequenziell** geprüft – eine zu weit oben stehende `deny any`-Regel blockiert alles darunter. Reihenfolge entscheidet!

> [!important] **Kernregel**
> Implizites `deny all` am Ende jeder ACL: Was nicht explizit erlaubt ist, ist verboten (Allowlist-Prinzip).
