[[Sicherheit|zurück]]

---

# Zertifikate & PKI

Ein digitales Zertifikat bindet einen **Public Key an eine Identität** (Person, Server, Organisation) und wird von einer vertrauenswürdigen Stelle (CA) signiert.

## Problem ohne PKI

Wie weiß ich, dass der empfangene Public Key wirklich zu `bank.de` gehört und nicht von einem Angreifer stammt (Man-in-the-Middle)?

→ Lösung: **Trusted Third Party** (Zertifizierungsstelle / Certificate Authority)

## X.509 Zertifikat – Aufbau

| Feld | Inhalt | Beispiel |
|---|---|---|
| Version | X.509-Version | v3 |
| Serial Number | Eindeutige Nummer bei der CA | 1234567890 |
| Issuer | Ausstellende CA | `CN=Let's Encrypt R3` |
| Subject | Zertifikatsinhaber | `CN=www.example.com` |
| Validity | Gültigkeitszeitraum | 2025-01-01 bis 2025-04-01 |
| Public Key | Öffentlicher Schlüssel des Inhabers | RSA 2048 / ECDSA P-256 |
| Subject Alt Names (SANs) | Weitere gültige Domains | `*.example.com`, `example.com` |
| Key Usage | Erlaubte Verwendungen | Digital Signature, Key Encipherment |
| Signature | CA-Signatur über alle obigen Felder | (binär) |

## PKI-Hierarchie (Certificate Authority Chain)

```text
Root CA  (selbst-signiert, im OS/Browser verankert)
    │
    │ signiert
    ▼
Intermediate CA  (auch: Sub-CA)
    │
    │ signiert
    ▼
End-Entity Zertifikat  (Server, Client, Code-Signing...)
```

- **Root CA:** höchste Vertrauensebene, Zertifikat ist im Betriebssystem/Browser gespeichert (Trust Store). Root CAs signieren i.d.R. nicht direkt End-Entity-Zertifikate.
- **Intermediate CA:** ausgestellte Sub-CA, übernimmt die operative Signierung. Bei Kompromittierung kann nur diese widerrufen werden.
- **End-Entity:** das eigentliche Server-/Client-Zertifikat

### Certificate Chain Validation

```text
Browser prüft:
1. Signatur des Server-Zertifikats durch Intermediate CA
2. Signatur des Intermediate-Zertifikats durch Root CA
3. Root CA im eigenen Trust Store vorhanden?
4. Zertifikat noch gültig (Datum)?
5. Zertifikat nicht widerrufen (CRL / OCSP)?
6. Subject/SAN passt zur aufgerufenen Domain?
```

## Zertifikatswiderruf

| Mechanismus | Funktionsweise | Nachteil |
|---|---|---|
| CRL (Certificate Revocation List) | CA veröffentlicht Liste widerrufener Seriennummern | Groß, periodisch, nicht real-time |
| OCSP (Online Certificate Status Protocol) | Client fragt CA-Server: „Ist Cert X noch gültig?" | Privacy (CA erfährt welche Sites besucht werden) |
| OCSP Stapling | Server legt aktuellen OCSP-Response dem TLS-Handshake bei | Client fragt nicht selbst, kein Privacy-Problem |

## Zertifikatstypen nach Validierungslevel

| Typ | Validierung | Zeigt im Browser | Anwendung |
|---|---|---|---|
| DV (Domain Validated) | Nur Domain-Kontrolle | 🔒 Schloss | Let's Encrypt, einfache Websites |
| OV (Organization Validated) | Firma wird geprüft | 🔒 Schloss | Unternehmenswebseiten |
| EV (Extended Validated) | Erweiterte Firmenprüfung | (früher grüne Leiste) | Banken, kritische Infra |

## Bekannte CAs und Tools

- **Public CAs:** Let's Encrypt (kostenlos, 90 Tage), DigiCert, Sectigo, GlobalSign
- **Private PKI:** Microsoft AD CS, OpenSSL, HashiCorp Vault, EJBCA
- **Tools:** `openssl x509 -text -in cert.pem` → Zertifikat anzeigen

```bash
# Zertifikat eines Servers anzeigen:
openssl s_client -connect example.com:443 -showcerts

# Ablaufdatum prüfen:
openssl x509 -noout -enddate -in cert.pem

# Selbstsigniertes Zertifikat erstellen:
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

> [!tip] **Merksatz**
> Zertifikat = **Personalausweis für Public Keys**. Die CA ist das Einwohnermeldeamt – sie bürgt dafür, dass Name und Schlüssel zusammenpassen.

> [!warning] **Achtung Falle**
> Ein Zertifikat beweist **nicht**, dass eine Website vertrauenswürdig ist – nur, dass die Domain wirklich die ist, die sie vorgibt zu sein. Phishing-Seiten können auch DV-Zertifikate haben!

> [!important] **Kernregel**
> **Chain of Trust:** Browser vertraut nur Zertifikaten, deren Signaturkette lückenlos zu einer im Trust Store verankerten Root CA führt.
