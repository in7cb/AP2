[[Sicherheit|zurück]]

---

# Schlüsselrollen bei Verschlüsselung & Signatur

Das häufigste Missverständnis in der Kryptografie: Welcher Schlüssel macht was? Die Rollen unterscheiden sich je nach **Anwendungsfall** (Verschlüsseln vs. Signieren).

## Übersicht: Welcher Schlüssel wofür?

| Anwendungsfall | Schlüssel | Wer nutzt ihn |
|---|---|---|
| **Verschlüsseln** | Public Key des Empfängers | Sender |
| **Entschlüsseln** | Private Key des Empfängers | Empfänger |
| **Signieren** | Private Key des Senders | Sender |
| **Signatur prüfen** | Public Key des Senders | Empfänger (oder jeder) |

> [!warning] **Achtung Falle**
> Häufiger Prüfungsfehler: „Mit dem Private Key verschlüsseln" ist **keine Verschlüsselung** (jeder kann mit dem Public Key entschlüsseln) – es ist eine **Signatur**.

## Verschlüsselung im Detail

**Ziel:** Nur der Empfänger soll die Nachricht lesen können.

```text
Sender                                    Empfänger
──────                                    ──────────
Klartext                                  Klartext
    │                                         ▲
    │ verschlüsseln mit                       │ entschlüsseln mit
    │ Public Key (Empfänger)                  │ Private Key (Empfänger)
    ▼                                         │
  Chiffrat ──────────────────────────────────▶
```

- Jeder, der den Public Key kennt, kann verschlüsseln
- Nur der Besitzer des Private Keys kann entschlüsseln
- **Schutzziel:** Vertraulichkeit

## Digitale Signatur im Detail

**Ziel:** Empfänger soll prüfen können, dass Nachricht wirklich vom Sender stammt und unverändert ist.

```text
Sender                                    Empfänger
──────                                    ──────────
Klartext → Hash (z.B. SHA-256)           Klartext → Hash berechnen
               │                                         │
               │ signieren mit                           │ vergleichen
               │ Private Key (Sender)                    │
               ▼                                         │
           Signatur ──────────────────────────▶ prüfen mit Public Key (Sender)
                                                         │
                                               Hash aus Signatur
                                                    │
                                               Stimmen überein? → ✅ gültig
```

- Nur der Besitzer des Private Keys kann signieren
- Jeder mit dem Public Key kann die Signatur prüfen
- **Schutzziele:** Integrität + Authentizität + Verbindlichkeit (Non-Repudiation)

## Kombiniert: Signieren + Verschlüsseln

Wenn beides gewünscht ist (vertraulich UND authentisch):

```text
1. Sender signiert Klartext mit seinem Private Key  → Signatur
2. Sender verschlüsselt (Klartext + Signatur) mit Public Key des Empfängers → Chiffrat

Empfänger:
1. Entschlüsselt mit eigenem Private Key
2. Prüft Signatur mit Public Key des Senders
```

## Hash-Funktion in der Signatur

Warum wird nicht der gesamte Text signiert, sondern nur der Hash?

| Aspekt | Direkt signieren | Hash signieren |
|---|---|---|
| Datenmenge | Volle Datei (MB/GB) | 32 Byte (SHA-256) |
| Performance | sehr langsam | schnell |
| Sicherheit | gleichwertig | gleichwertig |

> [!tip] **Merksatz**
> **"Verschlüsseln = Schloss zudrehen"** → Public Key des Empfängers (nur der hat den Schlüssel)  
> **"Signieren = Stempel draufmachen"** → Private Key des Senders (nur der hat den Stempel)

> [!important] **Kernregel**
> - Verschlüsseln: **fremder Public Key rein** (Empfänger)
> - Entschlüsseln: **eigener Private Key** (Empfänger)
> - Signieren: **eigener Private Key** (Sender)
> - Prüfen: **fremder Public Key** (Sender's Key)
