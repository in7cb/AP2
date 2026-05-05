[[Netzwerkmanagement|zurück]]

---

# RADIUS – Remote Authentication Dial-In User Service

RADIUS (RFC 2865) ist ein zentrales **AAA-Protokoll** – es übernimmt **Authentication, Authorization und Accounting** für Netzwerkzugänge. Typisch für: WLAN-Authentifizierung (802.1X), VPN-Zugänge, Switch-Port-Authentifizierung.

## AAA-Konzept

| Komponente | Funktion | Beispiel |
|-----------|----------|---------|
| **Authentication** | Wer bist du? Identität prüfen | Benutzername + Passwort |
| **Authorization** | Was darfst du? Rechte vergeben | VLAN-Zuweisung, ACLs |
| **Accounting** | Was hast du gemacht? Nutzung protokollieren | Login-Zeit, Datenmenge |

## RADIUS-Architektur

```text
[Client / Supplicant]
       |  (Credentials)
[NAS / Authenticator]          ← z.B. Switch, WLAN-AP, VPN-Gateway
  (RADIUS-Client)
       |  UDP 1812 (Auth) / UDP 1813 (Accounting)
[RADIUS-Server]                ← z.B. FreeRADIUS, Microsoft NPS, Cisco ISE
       |
[User-Datenbank]               ← lokale DB, Active Directory, LDAP
```

**NAS (Network Access Server):** Ist der Vermittler – nimmt Credentials vom Client entgegen und fragt den RADIUS-Server.

## Protokolldetails

- **Transport:** UDP (nicht TCP) – kein verbindungsorientierter Kanal
- **Auth-Port:** UDP **1812** (alt: 1645)
- **Accounting-Port:** UDP **1813** (alt: 1646)
- **Shared Secret:** symmetrischer Schlüssel zwischen NAS und RADIUS-Server (nicht zwischen Client und Server!)
- **Passwort-Verschlüsselung:** MD5-basiert (schwach – Kanal sollte trotzdem sicher sein)

## RADIUS vs. TACACS+

| | RADIUS | TACACS+ |
|-|--------|---------|
| Transport | UDP | TCP |
| AAA-Trennung | kombiniert Auth+Authz | getrennte Pakete |
| Verschlüsselung | nur Passwort | gesamter Body |
| Einsatz | WLAN, 802.1X, VPN | Cisco-Geräte-Management, CLI-Auth |
| Standard | RFC (offen) | Cisco-proprietär (offen dokumentiert) |

> [!tip] **Merksatz**
> **RADIUS** = Netzwerk**zugang** (WLAN, VPN), **TACACS+** = Geräteverwaltung (SSH-Login auf Router/Switch)

## 802.1X – Port-Based Network Access Control

RADIUS wird häufig mit **IEEE 802.1X** kombiniert für Switch-Port-Authentifizierung:

```text
[Endgerät / Supplicant]
    EAP over LAN (EAPOL)
[Switch-Port / Authenticator]
    RADIUS (EAP over RADIUS)
[RADIUS-Server]
```

**Ablauf:**
1. Gerät steckt ins Netz → Port blockiert (nur EAPOL durch)
2. Switch fordert Authentifizierung an
3. Gerät sendet Credentials via EAP
4. Switch leitet an RADIUS-Server weiter
5. RADIUS → `Access-Accept` oder `Access-Reject`
6. Bei Accept: Port öffnet, optional VLAN-Zuweisung per RADIUS-Attribut

**EAP-Methoden:**
- **EAP-TLS:** Zertifikat beidseitig → sicherste Methode
- **PEAP:** Server-Zertifikat + Passwort-Tunnel → gängiger Kompromiss
- **EAP-MD5:** nur Passwort, keine Verschlüsselung → veraltet

> [!important] **Kernregel**
> RADIUS antwortet immer mit **Access-Accept**, **Access-Reject** oder **Access-Challenge** (bei Multi-Faktor).

> [!warning] **Achtung Falle**
> Das **Shared Secret** ist kein Passwort für den User – es ist der gemeinsame Schlüssel zwischen NAS und RADIUS-Server. Wird oft verwechselt.

## RADIUS-Anbindung an Verzeichnisdienste (LDAP / Active Directory)

Statt eine eigene Nutzerdatenbank zu pflegen, kann RADIUS an einen bestehenden **Verzeichnisdienst** angebunden werden – so gelten die gleichen Zugangsdaten wie für PC-Login, E-Mail etc.

```text
[Client / Supplicant]
       │
[NAS / Authenticator]  (z.B. WLAN-Controller)
       │  RADIUS (UDP 1812)
[RADIUS-Server]  (z.B. Microsoft NPS, FreeRADIUS)
       │  LDAP (TCP 389) oder LDAPS (TCP 636)
[Verzeichnisdienst]
  ├── Microsoft Active Directory (AD)
  └── OpenLDAP
```

**LDAP (Lightweight Directory Access Protocol):**
- Standard-Protokoll zum Abfragen und Ändern von Verzeichnisdiensten
- Hierarchische Struktur (Tree): `DC=firma,DC=de` → `OU=Mitarbeiter` → `CN=Max Müller`
- RADIUS fragt per LDAP-Bind: „Ist Benutzername + Passwort gültig?"
- LDAPS (Port 636) = LDAP über TLS (verschlüsselt)

**Active Directory (AD):**
- Microsoft-Verzeichnisdienst, basiert auf LDAP + Kerberos
- Zentraler Authentifizierungsdienst in Windows-Umgebungen
- RADIUS-Server (z.B. Windows NPS = Network Policy Server) ist direkt in AD integriert → keine separate LDAP-Anbindung nötig

### Geräte- vs. Benutzerauthentifizierung

| | Geräteauthentifizierung | Benutzerauthentifizierung |
|---|---|---|
| **Wer** authentifiziert sich? | Das Gerät selbst | Der eingeloggte Benutzer |
| **Merkmal** | Gerätezertifikat, MAC-Adresse, Geräte-ID | Benutzerzertifikat, Benutzername + Passwort |
| **Zeitpunkt** | Beim Verbinden des Geräts (vor Login) | Nach Benutzeranmeldung |
| **Vorteil** | Nur bekannte/zugelassene Geräte kommen ins Netz | Zugriff ist an Person gebunden, nicht an Gerät |
| **Typischer Einsatz** | Unternehmens-Notebooks (Zertifikat aus AD) | BYOD, persönliche Zugangsdaten |
| **EAP-Methode** | EAP-TLS (Zertifikat) | PEAP (Passwort im verschlüsselten Tunnel) |

> [!important] **Kernregel**
> **Gerät** → Zertifikat/MAC (Was darf verbinden?). **Benutzer** → Benutzername/Passwort/Zertifikat (Wer verbindet sich?). Kombination möglich: erst Gerät prüfen, dann Nutzer.

> [!tip] **Merksatz**
> LDAP-Anbindung = RADIUS fragt bei jedem Login beim Verzeichnisdienst nach. So müssen Passwörter nur einmal (in AD/LDAP) gepflegt werden – **Single Source of Truth**.
