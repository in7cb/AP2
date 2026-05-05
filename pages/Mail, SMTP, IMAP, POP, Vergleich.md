[[Netzwerkdienste|zurück]]

---

# Mail – SMTP, IMAP, POP3

E-Mail funktioniert mit mehreren Protokollen: **SMTP** zum Senden/Weiterleiten, **IMAP oder POP3** zum Abrufen.

## Protokollübersicht

```text
[Client]                [Mailserver Absender]      [Mailserver Empfänger]   [Client]
   │──SMTP (587)──────►│                           │                            │
   │                   │──SMTP (25)───────────────►│                            │
   │                   │                           │◄──IMAP (993)/POP3 (995)───│
```

## SMTP (Simple Mail Transfer Protocol)

Zuständig für das **Senden und Weiterleiten** von E-Mails zwischen Servern.

| Variante | Port | Verschlüsselung | Einsatz |
|---------|------|----------------|---------|
| SMTP | 25 | ❌ / STARTTLS | Server-zu-Server |
| SMTP Submission | 587 | STARTTLS ✅ | Client → Server |
| SMTPS | 465 | TLS (direkt) ✅ | Client → Server (älter) |

**Typischer Ablauf:**
```text
Client ──(587/STARTTLS)──► eigener Mailserver (MTA)
                              ──(25)──► MX-Record Lookup via DNS
                                         ──► Ziel-Mailserver (MTA)
```

**MX-Record:** DNS-Eintrag, der den zuständigen Mailserver für eine Domain angibt.
```text
example.com.  MX  10  mail.example.com.
```
Niedrigere Prioritätszahl = bevorzugter Server.

## IMAP (Internet Message Access Protocol)

**Port:** TCP 143 (unverschlüsselt) / **993** (IMAPS, TLS)

- E-Mails bleiben **auf dem Server** gespeichert
- Synchronisation über mehrere Clients (PC, Smartphone, Webmail)
- Ordnerstruktur wird serverseitig gespeichert
- Mails werden nur bei Bedarf heruntergeladen (Header first)
- Standard heute für alle Multi-Device-Szenarien

## POP3 (Post Office Protocol v3)

**Port:** TCP 110 (unverschlüsselt) / **995** (POP3S, TLS)

- E-Mails werden **heruntergeladen und (meist) vom Server gelöscht**
- Keine Synchronisation – nur ein Client sinnvoll
- Einfacher, weniger Serverressourcen
- Heute kaum noch im Einsatz (außer Offline-Szenarien)

## IMAP vs. POP3

| Merkmal | IMAP | POP3 |
|---------|------|------|
| Port (unverschl.) | 143 | 110 |
| Port (TLS) | 993 | 995 |
| Speicherort Mails | Server | Lokal (nach Download) |
| Multi-Device | ✅ | ❌ (problematisch) |
| Ordner serverseitig | ✅ | ❌ |
| Offline-Zugriff | ❌ (Sync nötig) | ✅ |
| Serverauslastung | höher | niedriger |

## Verschlüsselung – STARTTLS vs. TLS

| Methode | Ablauf | Port |
|---------|--------|------|
| **Klartext** | keine Verschlüsselung | 25, 143, 110 |
| **STARTTLS** | startet unverschlüsselt, upgradet auf TLS | 587, 143 |
| **Implicit TLS** | sofort TLS ab dem ersten Byte | 465, 993, 995 |

> [!important] **Kernregel**
> SMTP (25) = Server-zu-Server. SMTP Submission (587) = Client zum eigenen Server. IMAP (993) = Abrufen mit Sync. POP3 (995) = Abrufen und löschen.

> [!warning] **Achtung Falle**
> STARTTLS ≠ SSL/TLS direkt: STARTTLS startet die Verbindung unverschlüsselt und upgradet dann. Bei Downgrade-Angriffen kann die Verschlüsselung deaktiviert werden → **Implicit TLS bevorzugen**.

> [!tip] **Merksatz**
> **S**MTP = **S**enden, **I**MAP = **I**m Server lassen (Sync), **P**OP = **P**ulle und lösche.
