Asymmetrische Verfahren nutzen **mathematisch verbundene Schlüsselpaare**: Was mit dem Public Key verschlüsselt wird, kann nur der Private Key entschlüsseln – und umgekehrt.

## Schlüsselpaar-Prinzip

```text
Schlüsselpaar:  Public Key (öffentlich)  ←→  Private Key (geheim)
                        ↑                           ↑
               Jeder kann verschlüsseln      Nur Besitzer entschlüsselt
               Jeder kann Signatur prüfen    Nur Besitzer signiert
```

## RSA (Rivest–Shamir–Adleman)

RSA basiert auf der **Schwierigkeit, große Zahlen in Primfaktoren zu zerlegen**.

### Schlüsselgenerierung (vereinfacht)

```text
1. Wähle zwei große Primzahlen p, q
2. n = p × q  (Modulus, öffentlich)
3. φ(n) = (p-1)(q-1)
4. Wähle e mit ggT(e, φ(n)) = 1  → Public Key: (e, n)
5. Berechne d: e × d ≡ 1 (mod φ(n))  → Private Key: (d, n)
```

### Schlüssellängen

| Länge | Sicherheit | Empfehlung |
|---|---|---|
| 512 Bit | ❌ gebrochen | nicht verwenden |
| 1024 Bit | ❌ unsicher | nicht verwenden |
| 2048 Bit | ✅ aktuell | Minimum für neue Systeme |
| 4096 Bit | ✅ sehr sicher | hoher Rechenaufwand |

> [!warning] **Achtung Falle**
> RSA-1024 ist **nicht mehr sicher** – BSI empfiehlt seit Jahren mindestens RSA-2048.

## Diffie-Hellman (DH) – Schlüsselaustausch

DH ist kein Verschlüsselungsverfahren, sondern ein **Key-Exchange-Protokoll**: Beide Seiten einigen sich auf einen gemeinsamen Schlüssel, ohne ihn zu übertragen.

```text
Öffentlich bekannt: Primzahl p, Generator g

Alice:  a (geheim)  → sendet g^a mod p
Bob:    b (geheim)  → sendet g^b mod p

Gemeinsamer Schlüssel: (g^a)^b mod p = (g^b)^a mod p = g^(ab) mod p
```

- **Problem klassisches DH:** kein Forward Secrecy bei statischen Schlüsseln
- **DHE (Ephemeral DH):** neue Schlüssel pro Session → **Perfect Forward Secrecy (PFS)**

## ECC (Elliptic Curve Cryptography)

ECC erreicht **gleiche Sicherheit mit deutlich kürzeren Schlüsseln** als RSA:

| RSA-Äquivalent | ECC-Schlüssellänge |
|---|---|
| 1024 Bit | 160 Bit |
| 2048 Bit | 224 Bit |
| 3072 Bit | 256 Bit |
| 15360 Bit | 512 Bit |

- Basiert auf Gruppenoperationen auf elliptischen Kurven (z.B. Curve25519, P-256)
- **ECDH:** ECC-basierter Diffie-Hellman-Austausch
- **ECDSA:** ECC-basierte digitale Signatur
- **Vorteil:** geringerer Rechenaufwand, ideal für mobile/embedded Systeme

> [!tip] **Merksatz**
> **RSA = Primfaktorzerlegung** (schwer für Computer), **ECC = Elliptische Kurven** (noch schwerer, kleinere Schlüssel). TLS 1.3 bevorzugt ECDHE.

> [!important] **Kernregel**
> Asymmetrische Verfahren werden **nicht** zur Datenverschlüsselung genutzt (zu langsam), sondern für: Schlüsselaustausch, digitale Signaturen, Zertifikate.
