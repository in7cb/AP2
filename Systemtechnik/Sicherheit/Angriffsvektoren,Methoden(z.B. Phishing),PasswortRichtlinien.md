Angriffsvektoren sind die Wege, über die Angreifer in Systeme eindringen. Prüfungsrelevant: Methoden verstehen + konkrete Gegenmaßnahmen nennen.

## Social Engineering

Angriffe auf den **Menschen** statt auf die Technik – Manipulation, Täuschung, Vertrauen ausnutzen.

| Methode | Beschreibung | Gegenmaßnahme |
|---|---|---|
| **Phishing** | Gefälschte E-Mails/Websites, die Zugangsdaten abgreifen | Awareness-Training, SPF/DKIM/DMARC, MFA |
| **Spear-Phishing** | Gezieltes Phishing (personalisiert, z.B. CEO-Fraud) | Verifizierung bei ungewöhnlichen Anfragen |
| **Vishing** | Phishing per Telefon (Voice Phishing) | Rückruf-Verifikation |
| **Smishing** | Phishing per SMS | Keine Links in SMS öffnen |
| **Pretexting** | Erfundene Situation um Infos zu erfragen | Awareness, klare Richtlinien |
| **Baiting** | USB-Sticks mit Malware an öffentlichen Orten | USB-Ports deaktivieren, Richtlinien |
| **Tailgating** | Physisches Hinterherscchleichen durch gesicherte Türen | Mantraps, Awareness |

## Technische Angriffsmethoden

### Man-in-the-Middle (MITM)

```text
Client ──── [Angreifer liest/verändert mit] ──── Server
```

- **ARP-Spoofing:** Angreifer sendet gefälschte ARP-Replies, leitet Traffic um
- **DNS-Spoofing:** gefälschte DNS-Antworten leiten auf Fake-Server
- **SSL-Stripping:** HTTPS wird zu HTTP degradiert

**Gegenmaßnahmen:** TLS/HTTPS, HSTS, Certificate Pinning, MFA, Dynamic ARP Inspection

### DoS / DDoS

| Typ | Beschreibung |
|---|---|
| **DoS** (Denial of Service) | Ein Angreifer überflutet ein System |
| **DDoS** | Viele (Botnet-)Systeme greifen gleichzeitig an |
| **SYN-Flood** | Masse halboffener TCP-Verbindungen, State-Tabelle erschöpft |
| **Amplification** | Kleine Anfragen → große Antworten (DNS/NTP-Amplification) |
| **Application Layer** | HTTP-Flood auf Webserver (schwer erkennbar) |

**Gegenmaßnahmen:** Rate-Limiting, SYN-Cookies, Scrubbing-Center, CDN, Firewall-Rules

### Brute Force & Password Attacks

| Methode | Beschreibung | Gegenmaßnahme |
|---|---|---|
| **Brute Force** | Alle Kombinationen durchprobieren | Account-Lockout, MFA, lange Passwörter |
| **Dictionary Attack** | Wörterlistle wird durchprobiert | Kein Wort als Passwort, Passphrase |
| **Rainbow Table** | Vorberechnete Hash-Tabellen | Salting der Passwörter |
| **Credential Stuffing** | Gestohlene Passwörter aus Datenlecks | Passwörter nicht wiederverwenden, MFA |
| **Pass-the-Hash** | NTLM-Hash direkt für Auth. genutzt | Kerberos, Protected Users, Credential Guard |

### SQL Injection

```sql
-- Eingabe: admin' OR '1'='1
SELECT * FROM users WHERE username='admin' OR '1'='1' AND password='...'
-- → Gibt alle User zurück!
```

**Gegenmaßnahme:** Prepared Statements / Parameterized Queries, Input-Validierung, WAF

### Weitere Angriffe

| Angriff | Beschreibung |
|---|---|
| **XSS** (Cross-Site Scripting) | Schadcode in Webseite einschleusen, der bei anderen Nutzern ausgeführt wird |
| **CSRF** | Nutzer wird unbemerkt zu Aktion verleitet (z.B. Geld überweisen) |
| **Zero-Day** | Sicherheitslücke die noch kein Patch existiert |
| **Port Scanning** | Offene Ports erkunden (nmap), Vorbereitung für Angriff |
| **Sniffing** | Netzwerkverkehr mitlauschen (Wireshark, tcpdump) |

## Passwort-Richtlinien

### BSI-Empfehlungen (aktuell)

Das BSI hat seine Empfehlungen überarbeitet – **weg von erzwungenem regelmäßigem Wechsel, hin zu langen Passwörtern**:

| Kriterium | Empfehlung |
|---|---|
| Mindestlänge | **8 Zeichen** (Minimum), besser 12–16+ |
| Komplexität | Groß-, Kleinbuchstaben, Ziffern, Sonderzeichen sinnvoll |
| Regelmäßiger Wechsel | ❌ Nur bei Verdacht auf Kompromittierung |
| Passwortwiederholung | ❌ Kein Passwort mehrfach verwenden |
| Passphrasen | ✅ z.B. `Korrekte-Pferd-Batterie-Heftklammer` |
| Passwort-Manager | ✅ Empfohlen (KeePass, Bitwarden) |
| MFA/2FA | ✅ Immer aktivieren wo möglich |

### Technische Umsetzung

```text
Passwort-Hashing (korrekt):
  Hash = bcrypt(password + salt, cost=12)

Nie:  MD5(password) oder SHA1(password) ohne Salt
```

| Algorithmus | Geeignet für Passwörter? | Warum |
|---|---|---|
| MD5 | ❌ | Zu schnell, Rainbow-Tables existieren |
| SHA-256 | ❌ (allein) | Zu schnell für Passwörter |
| bcrypt | ✅ | Langsam by Design, hat integrierten Salt |
| Argon2 | ✅ | Moderner Standard (Passwort-Hashing) |
| scrypt | ✅ | Memory-hard, gut gegen GPU-Angriffe |

> [!tip] **Merksatz**
> **Phishing trifft Menschen, SQL Injection trifft Anwendungen, DoS trifft die Verfügbarkeit** – je nach Ziel braucht es andere Gegenmaßnahmen.

> [!warning] **Achtung Falle**
> Passwörter niemals im Klartext speichern – auch MD5 ist **nicht ausreichend** (Rainbow Tables). Immer **gesaltetes, langsames Hashing** (bcrypt/Argon2).

> [!important] **Kernregel**
> **MFA (Multi-Faktor-Authentifizierung)** ist die effektivste Einzelmaßnahme gegen Credential-basierte Angriffe – selbst wenn das Passwort kompromittiert ist.
