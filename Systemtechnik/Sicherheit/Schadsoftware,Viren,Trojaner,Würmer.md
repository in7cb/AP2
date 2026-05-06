Schadsoftware (Malware) ist Software, die ohne Wissen des Nutzers schädliche Aktionen ausführt. Unterschied liegt primär in **Verbreitung**, **Tarnung** und **Ziel**.

## Überblick: Malware-Typen

| Typ | Verbreitung | Tarnung | Ziel |
|---|---|---|---|
| **Virus** | Anheften an Dateien, aktiviert durch Nutzer | Als legitime Datei | Schaden, Verbreitung |
| **Wurm** | Selbstständig über Netz (kein Wirtsfile) | Eigenständig | Massenverbreitung, DDoS |
| **Trojaner** | Als nützliche Software getarnt | Als legitimes Programm | Backdoor, Datenklau |
| **Ransomware** | Meist per Phishing/Trojan | Trojanisch | Verschlüsselung + Lösegeld |
| **Rootkit** | Mit Admin-Rechten installiert | Versteckt sich im OS | Dauerhafter Zugang, Kontrolle |
| **Spyware** | Gebündelt mit Software | Unauffällig im Hintergrund | Datenaustausch, Keylogging |
| **Adware** | Gebündelt mit Freeware | Sichtbar (nervend) | Werbeeinnahmen |
| **Keylogger** | Eigenständig oder als Trojaner-Modul | Unsichtbar | Passwörter, Eingaben |
| **Botnet-Client** | Trojanisch | Verborgen | DDoS, Spam, Mining |

## Im Detail

### Viren

- Hängen sich an **legitime Dateien** (exe, doc, pdf) an
- Werden beim Öffnen der Wirtsdate **aktiviert**
- Verbreiten sich durch Dateiübertragung (USB, Mail-Anhang, Download)
- **Polymorpher Virus:** verändert seinen Code bei jeder Infektion → erschwert Signatur-Erkennung

### Würmer

- Benötigen **kein Wirtsprogramm** – eigenständige, selbst-replizierende Programme
- Verbreiten sich über Netzwerk, E-Mail, Sicherheitslücken (ohne Nutzerinteraktion)
- Können in kurzer Zeit tausende Systeme befallen
- **Beispiel:** WannaCry (2017) – nutzte SMB-Exploit EternalBlue

### Trojaner

- Tarnen sich als **nützliche oder erwünschte Software**
- Öffnen Backdoors, stehlen Daten, laden weitere Malware nach
- Kein selbstständiges Verbreiten (im Gegensatz zu Würmern)
- **RAT (Remote Access Trojan):** gibt Angreifer volle Fernsteuerung

### Ransomware

- Verschlüsselt Dateien des Opfers (oft AES-256), Schlüssel beim Angreifer
- Lösegeld-Forderung in Kryptowährung (Bitcoin, Monero)
- **Double Extortion:** Daten werden verschlüsselt **und** mit Veröffentlichung gedroht
- **Beispiele:** WannaCry, Ryuk, LockBit, REvil

### Rootkits

- Verstecken sich tief im System (Kernel-Level oder Bootsektor)
- Tarnen andere Malware, Prozesse, Netzwerkverbindungen
- Schwer zu erkennen (normaler Virenscanner sieht sie nicht)
- **BIOS/UEFI-Rootkit:** überleben sogar Betriebssystem-Neuinstallation

## Verbreitungswege

```text
Phishing-Mail → Anhang öffnen → Trojaner installiert
Exploit-Kit → Browser-Schwachstelle → Drive-by-Download
USB-Stick → AutoRun → Virus
Netzwerk-Scan → Offener Port / ungepatchte Software → Wurm
Social Engineering → Nutzer installiert "Update" → Trojan
```

## Schutzmaßnahmen

| Maßnahme | Schutz gegen |
|---|---|
| Aktuelles Antivirus/EDR | Viren, Trojaner, Würmer |
| Regelmäßige Patches | Würmer (Exploit-basiert), Drive-by |
| Spam-Filter / Mail-Gateway | Phishing, Anhänge |
| Backups (offline/immutable) | Ransomware |
| Least Privilege / Sandboxing | Rootkits, Malware-Ausbreitung |
| Netzwerk-Segmentierung | Laterale Bewegung von Würmern |
| User-Awareness-Training | Social Engineering, Phishing |

> [!tip] **Merksatz**
> **Virus = braucht Wirt**, **Wurm = wandert alleine**, **Trojaner = verkleidet sich**. Ransomware ist kein eigener Typ, sondern eine Trojaner-Variante mit spezifischem Ziel.

> [!warning] **Achtung Falle**
> Würmer verbreiten sich **ohne Nutzerinteraktion** – das ist der entscheidende Unterschied zu Viren. WannaCry war ein Wurm (kein Phishing nötig, nur offener SMB-Port).

> [!important] **Kernregel**
> Ransomware-Schutz: **3-2-1-Backup-Regel** – 3 Kopien, auf 2 verschiedenen Medien, davon 1 offline (nicht erreichbar vom infizierten System).
