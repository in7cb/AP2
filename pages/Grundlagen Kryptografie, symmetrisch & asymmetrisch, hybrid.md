[[Sicherheit|zurück]]

---

# Grundlagen Kryptografie

Kryptografie schützt Informationen durch mathematische Verfahren vor unbefugtem Zugriff, Manipulation und Nichtabstreitbarkeit.

## Schutzziele (CIA)

| Schutzziel | Englisch | Bedeutung | Kryptografisches Mittel |
|---|---|---|---|
| Vertraulichkeit | Confidentiality | Nur Berechtigte lesen | Verschlüsselung |
| Integrität | Integrity | Daten unverändert | Hash / MAC / Signatur |
| Verfügbarkeit | Availability | System erreichbar | (nicht direkt Kryptografie) |
| Authentizität | Authenticity | Absender verifizierbar | Digitale Signatur, Zertifikat |
| Verbindlichkeit | Non-Repudiation | Absender kann nicht leugnen | Digitale Signatur |

## Drei Kryptografie-Paradigmen

### Symmetrische Kryptografie

Sender und Empfänger teilen **denselben Schlüssel** (Secret Key).

```text
Klartext → [Schlüssel K] → Chiffrat → [Schlüssel K] → Klartext
```

- **Vorteile:** sehr schnell, effizient für große Datenmengen
- **Nachteile:** sicherer Schlüsselaustausch ist das Kernproblem (n*(n-1)/2 Schlüssel bei n Teilnehmern)
- **Beispiele:** AES, DES, 3DES, ChaCha20

### Asymmetrische Kryptografie (Public-Key)

Jeder hat ein **Schlüsselpaar**: öffentlicher Schlüssel (Public Key) und privater Schlüssel (Private Key).

```text
Verschlüsseln: Klartext → [Public Key Empfänger] → Chiffrat
Entschlüsseln: Chiffrat → [Private Key Empfänger] → Klartext
```

- **Vorteile:** kein sicherer Schlüsselaustausch nötig, ermöglicht Signaturen
- **Nachteile:** rechenintensiv, ca. 100–1000× langsamer als symmetrisch
- **Beispiele:** RSA, ECC, Diffie-Hellman, ElGamal

### Hybride Kryptografie

Kombination beider Verfahren: asymmetrisch für den **Schlüsselaustausch**, symmetrisch für die **Datenverschlüsselung**.

```text
1. Zufälligen Session Key generieren (sym.)
2. Session Key mit Public Key des Empfängers verschlüsseln (asym.)
3. Nutzdaten mit Session Key verschlüsseln (sym.)
```

**Verwendung:** TLS/HTTPS, PGP, SSH, IPsec

## Kryptografische Primitive

| Primitive | Zweck | Beispiele |
|---|---|---|
| Blockchiffre | Datenverschlüsselung | AES, DES |
| Stromchiffre | Echtzeit-Verschlüsselung | ChaCha20, RC4 (veraltet) |
| Hash-Funktion | Integrität, Fingerprint | SHA-256, SHA-3, MD5 (veraltet) |
| MAC | Integrität + Authentizität | HMAC-SHA256 |
| Digitale Signatur | Authentizität + Verbindlichkeit | RSA-Sign, ECDSA |
| Key Exchange | Schlüsselaustausch | Diffie-Hellman, ECDH |

> [!tip] **Merksatz**
> **Sym = Schnell, Asym = Sicher beim Austausch** → Hybrid = beides kombiniert. TLS macht genau das.

> [!important] **Kernregel**
> Asymmetrisch verschlüsselt man **nie** große Datenmengen direkt – nur Schlüssel oder Hashes.

> [!warning] **Achtung Falle**
> MD5 und SHA-1 gelten als gebrochen (Kollisionsangriffe möglich). Für neue Systeme: **SHA-256 oder besser**.
