Symmetrische Verschlüsselung verwendet **denselben Schlüssel** zum Ver- und Entschlüsseln. Sehr performant, daher Standard für Massendatenverschlüsselung.

## Überblick der wichtigsten Verfahren

| Algorithmus | Schlüssellänge | Blockgröße | Status |
|---|---|---|---|
| DES | 56 Bit | 64 Bit | ❌ gebrochen (1999 in <24h geknackt) |
| 3DES (Triple-DES) | 112 / 168 Bit | 64 Bit | ⚠️ veraltet, noch in Legacy |
| AES-128 | 128 Bit | 128 Bit | ✅ sicher, Standard |
| AES-192 | 192 Bit | 128 Bit | ✅ sicher |
| AES-256 | 256 Bit | 128 Bit | ✅ sicher, empfohlen |
| ChaCha20 | 256 Bit | Stromchiffre | ✅ Modern, für TLS 1.3 |

## DES (Data Encryption Standard)

- Entwickelt 1977 von IBM / NSA, NIST-Standard bis 2001
- **Feistel-Netzwerk** mit 16 Runden
- 56-Bit-Schlüssel → nur 2⁵⁶ ≈ 7,2 × 10¹⁶ Kombinationen → **Brute-Force-angreifbar**
- 1999: EFF-Rechner knackte DES in **22 Stunden**

**3DES:** DES dreimal hintereinander mit 2 oder 3 verschiedenen Schlüsseln → temporäre Lösung, inzwischen ebenfalls deprecated

## AES (Advanced Encryption Standard)

- Seit 2001 NIST-Standard, Nachfolger von DES
- Basiert auf **Rijndael**-Algorithmus (Joan Daemen & Vincent Rijmen)
- **Blockgröße immer 128 Bit**, Schlüssellänge variabel: 128 / 192 / 256 Bit
- Anzahl der Runden: 10 / 12 / 14 (je nach Schlüssellänge)

### AES-Operationen pro Runde

```text
1. SubBytes     – jedes Byte durch S-Box ersetzen (nicht-linear)
2. ShiftRows    – Zeilen des State-Arrays rotieren
3. MixColumns   – Spalten-Transformation (lineare Mischung)
4. AddRoundKey  – XOR mit Rundenschlüssel
```

## Betriebsmodi

Der Modus bestimmt, **wie** der Blockalgorithmus auf Datenströme angewendet wird:

| Modus | Name | Besonderheit | Verwendung |
|---|---|---|---|
| ECB | Electronic Codebook | Gleicher Klartext → gleicher Chiffrat-Block (unsicher!) | ❌ nicht verwenden |
| CBC | Cipher Block Chaining | XOR mit Vorgängerblock, IV nötig | TLS < 1.3, VPNs |
| CTR | Counter | Stromchiffre-Modus, parallelisierbar | SSH, IPsec |
| GCM | Galois/Counter Mode | CTR + Authentizitäts-Tag (AEAD) | TLS 1.3, bevorzugt |

> [!warning] **Achtung Falle**
> **ECB nie verwenden** – Muster im Klartext bleiben im Chiffrat sichtbar (berühmtes ECB-Penguin-Bild). Immer CBC oder GCM.

> [!tip] **Merksatz**
> **DES = Dead**, **3DES = Deprecated**, **AES = Aktuell**. Schlüssellänge: lieber 256 Bit.

> [!important] **Kernregel**
> AES-256-GCM ist der aktuelle Gold-Standard: bietet **Vertraulichkeit + Integrität** in einem (AEAD = Authenticated Encryption with Associated Data).
