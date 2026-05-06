Drei Protokolle für Dateiübertragung im Netz – unterscheiden sich stark in Sicherheit, Komplexität und Einsatzgebiet.

## FTP (File Transfer Protocol)

- **Port:** TCP **21** (Steuerkanal) + TCP **20** (Datenkanal, aktiv) oder dynamisch (passiv)
- **Verschlüsselung:** ❌ Klartext – Login und Daten unverschlüsselt
- **RFC:** 959
- **Modi:**
  - **Aktiv (PORT):** Server öffnet Datenverbindung zurück zum Client (Port 20) → Probleme mit NAT/Firewall
  - **Passiv (PASV):** Client öffnet Datenverbindung zum Server → firewall-freundlich

**Varianten:**
- **FTPS:** FTP + TLS/SSL-Verschlüsselung (explizit: STARTTLS auf Port 21, implizit: Port 990)
- **SFTP:** kein echter FTP-Nachfolger – läuft über **SSH** (Port 22), völlig anderes Protokoll

## TFTP (Trivial File Transfer Protocol)

- **Port:** UDP **69**
- **Verschlüsselung:** ❌ keine
- **Authentifizierung:** ❌ keine
- **RFC:** 1350
- **Besonderheit:** extrem simpel – nur GET und PUT, keine Directory-Listings, kein Login
- **Einsatz:** Bootstrap-Protokoll – Firmware-Updates auf Netzwerkgeräten, PXE-Boot (Bootimage laden), Cisco IOS-Update

```bash
# Cisco IOS: Firmware via TFTP laden
copy tftp flash
# Adresse des TFTP-Servers und Dateiname eingeben
```

## SCP (Secure Copy Protocol)

- **Port:** TCP **22** (über SSH)
- **Verschlüsselung:** ✅ vollständig (SSH)
- **Authentifizierung:** ✅ SSH-Authentifizierung
- **Besonderheit:** SSH-basiert, einfaches Kommandozeilentool

```bash
# Datei zu Remote-Host kopieren
scp datei.txt user@192.168.1.10:/home/user/

# Verzeichnis rekursiv
scp -r /lokaler/ordner user@host:/ziel/

# Von Remote holen
scp user@host:/pfad/datei.txt .
```

**SFTP vs. SCP:** SFTP ist interaktiv (wie FTP-Client), SCP ist ein einzelner Kopierbefehl.

## Vergleich

| Merkmal | FTP | TFTP | SCP |
|---------|-----|------|-----|
| Transport | TCP | UDP | TCP (SSH) |
| Port | 21/20 | 69 | 22 |
| Verschlüsselung | ❌ (FTP) / ✅ (FTPS) | ❌ | ✅ |
| Authentifizierung | Passwort (Klartext) | ❌ | SSH |
| Verzeichnis-Navigation | ✅ | ❌ | ❌ (nur copy) |
| Einsatz | Dateiserver, Legacy | PXE, Firmware | sichere Übertragung |
| Produktiv empfohlen | nur FTPS/SFTP | nur isolierte Netze | ✅ |

> [!important] **Kernregel**
> FTP und TFTP übertragen Zugangsdaten im Klartext – für produktive Dateiübertragung immer **SCP, SFTP oder FTPS**.

> [!tip] **Merksatz**
> **T**FTP = **T**rivial (kein Login, kein TLS) – für Geräte-Bootstrap und PXE. **SCP** = SSH-Copy – sicher, simpel, direkt.

> [!warning] **Achtung Falle**
> SFTP ≠ FTPS: SFTP läuft über SSH (Port 22), FTPS ist FTP mit TLS (Port 21/990). Vollkommen unterschiedliche Protokolle, gleicher Name-Anfang – häufige Verwechslung in Prüfungen.
