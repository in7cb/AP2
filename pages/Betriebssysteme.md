[[Netzwerk-Client|zurück]]

---

# Betriebssysteme

Ein Betriebssystem (OS) verwaltet Hardware-Ressourcen und stellt Anwendungen eine standardisierte Schnittstelle bereit. Für FiSi relevant: Windows, Linux, grundlegend macOS.

## Kernaufgaben eines Betriebssystems

| Aufgabe | Beschreibung |
|---|---|
| **Prozessverwaltung** | Scheduling, Multitasking, Prozessrechte |
| **Speicherverwaltung** | RAM-Zuweisung, virtueller Speicher, Paging |
| **Dateisystemverwaltung** | Lesen/Schreiben, Rechtevergabe, Journaling |
| **Gerätesteuerung** | Treiber, I/O-Verwaltung |
| **Netzwerkkommunikation** | TCP/IP-Stack, Sockets |
| **Sicherheit & Benutzer** | Authentifizierung, Rechtesystem |
| **UI** | GUI und/oder CLI |

## Windows

### Client vs. Server

| | Windows 10/11 (Client) | Windows Server 2019/2022 |
|---|---|---|
| Ziel | Einzelplatz, Endnutzer | Dienste, zentrale Verwaltung |
| Max. RAM | 2 TB | 48 TB |
| Concurrent Remote Sessions | 1 (RDP beschränkt) | Unbegrenzt (mit Lizenz) |
| Besonderheiten | WinRE, BitLocker, WSL2 | AD DS, DNS, DHCP, IIS, Hyper-V |

### Windows-Dateisystem

- **NTFS** – Standard: Zugriffsrechte, Journaling, Komprimierung, Verschlüsselung (EFS)
- **FAT32** – Legacy, max. 4 GB Dateigröße, USB-Sticks
- **exFAT** – FAT32-Nachfolger für externe Medien (keine 4 GB Grenze)
- **ReFS** – Resilient File System, Server-Fokus, Dateiintegrität

### Wichtige Windows-Konzepte

```text
%SystemRoot%  → C:\Windows
%AppData%     → C:\Users\<User>\AppData\Roaming
%TEMP%        → temporäre Dateien

Dienste:    services.msc
Firewall:   Windows Defender Firewall
Eventlog:   eventvwr.msc
Registry:   regedit.exe
```

## Linux

### Verbreitung & Distributionen

| Distribution | Paketmanager | Einsatz |
|---|---|---|
| Ubuntu / Debian | apt (dpkg) | Server, Desktop, weit verbreitet |
| RHEL / CentOS / Rocky | yum / dnf (rpm) | Enterprise-Server |
| Arch Linux | pacman | Rolling Release, Enthusiasten |
| Kali Linux | apt | Security/Pentesting |
| Raspbian / Raspberry Pi OS | apt | IoT, Einplatinen-PCs |

### Linux-Verzeichnisstruktur (FHS)

```text
/           Root-Verzeichnis
├── bin/    Systembinaries (ls, cp, ...)
├── etc/    Konfigurationsdateien
├── home/   Benutzerverzeichnisse
├── var/    Variable Daten (Logs, Spool)
├── tmp/    Temporäre Dateien
├── usr/    Anwendungen, Libraries
├── dev/    Geräte als Dateien (sda, ttyS0)
├── proc/   Kernel/Prozess-Informationen (virtuell)
└── sys/    Kernel-Schnittstelle (virtuell)
```

### Wichtige Linux-Befehle (Systemverwaltung)

```bash
# Netzwerk
ip a                      # IP-Adressen anzeigen
ip route show             # Routing-Tabelle
ss -tulpn                 # Offene Ports / Sockets

# Systemdienste (systemd)
systemctl status sshd     # Status eines Dienstes
systemctl start/stop/restart sshd
systemctl enable sshd     # Autostart aktivieren

# Pakete (Debian/Ubuntu)
apt update && apt upgrade
apt install <paket>
apt remove <paket>

# Logs
journalctl -u sshd        # Logs eines Dienstes
tail -f /var/log/syslog   # Live-Log
```

## macOS

- Basis: BSD Unix (Darwin-Kernel), POSIX-konform
- Dateisystem: **APFS** (Apple File System) – Copy-on-Write, Snapshots
- Paketmanager (inoffiziell): Homebrew
- Im Unternehmenskontext: Jamf für MDM, Active Directory-Anbindung möglich

## Vergleich der großen Drei

| Kriterium | Windows | Linux | macOS |
|---|---|---|---|
| Lizenz | Kommerziell | Meist Open Source (GPL) | Kommerziell (nur Apple-HW) |
| CLI | PowerShell / CMD | bash / zsh / sh | zsh (Standard seit Catalina) |
| Domänen-Beitritt | Native AD | SSSD / realm | Native AD via Directory Utility |
| Treiber-Support | Sehr breit | Wächst stark | Eingeschränkt (Apple-HW) |
| Sicherheit | Stark verbessert (Win11) | Gut, aber config-abhängig | Sehr restriktiv (Gatekeeper, SIP) |

> [!important] **Kernregel**
> Windows dominiert den Desktop im Unternehmensumfeld.  
> Linux dominiert Server, Cloud und Embedded.  
> macOS ist Nische (kreative Berufe, Entwickler).

> [!warning] **Achtung Falle**
> Linux ist kein einzelnes OS, sondern ein Kernel. Die Distribution (Ubuntu, RHEL etc.) bestimmt, welcher Paketmanager, Init-System (systemd vs. SysV) und Konfigurationsstandard verwendet wird.

## Bootprozess & GRUB

Der Bootprozess läuft in festgelegter Reihenfolge ab – von der Hardware bis zum Login-Screen.

```text
POST → UEFI/BIOS → Bootloader (GRUB) → Kernel → Init (systemd)
```

| Phase | Aufgabe |
|---|---|
| **POST** (Power-On Self Test) | Hardware-Selbsttest (RAM, CPU, GPU) – BIOS/UEFI-Firmware |
| **UEFI/BIOS** | Findet Bootgerät (Festplatte, USB, Netz), lädt Bootloader |
| **Bootloader (GRUB)** | Zeigt Auswahlmenü, lädt Kernel + initramfs in den RAM |
| **Kernel** | Initialisiert Hardware-Treiber, hängt Root-Dateisystem ein |
| **Init / systemd** | Startet alle Systemdienste (PID 1), bringt System in Zielstufe |

### GRUB (Grand Unified Bootloader)

GRUB ist der Standard-Bootloader auf Linux-Systemen (GRUB 2). Er ermöglicht die Auswahl zwischen mehreren Betriebssystemen und Kernel-Versionen.

```bash
# GRUB-Konfiguration anpassen (nicht direkt editieren!)
nano /etc/default/grub

# Danach neu generieren:
update-grub          # Debian/Ubuntu
grub2-mkconfig -o /boot/grub2/grub.cfg   # RHEL/CentOS

# Wichtige Parameter in /etc/default/grub:
GRUB_TIMEOUT=5       # Wartezeit bis Auto-Start (Sekunden)
GRUB_DEFAULT=0       # Standardeintrag (0 = erster)
```

**GRUB-Notfallzugang:** Beim Bootmenü `e` drücken → Kernel-Parameter editieren (z.B. `single` für Single-User-Mode / Root-Recovery).

### UEFI vs. BIOS (Legacy)

| | BIOS (Legacy) | UEFI |
|---|---|---|
| Partitionstabelle | **MBR** | **GPT** |
| Max. Festplattengröße | 2 TB | > 2 TB (128 Partitionen) |
| Bootprozess | 16-Bit, langsam | 32/64-Bit, schneller |
| Secure Boot | ❌ | ✅ (verhindert unbekannte Bootloader) |
| Benutzeroberfläche | Text | Grafisch (Maus möglich) |

### MBR vs. GPT

| | MBR (Master Boot Record) | GPT (GUID Partition Table) |
|---|---|---|
| Max. Partitionen | 4 primäre (+ erweiterte) | 128 (unter Windows/Linux) |
| Max. Disk-Größe | 2 TB | 9,4 ZB (praktisch unbegrenzt) |
| Redundanz | keine | Backup-Tabelle am Ende der Disk |
| Kompatibilität | alle Systeme | UEFI empfohlen, BIOS mit Trick |

> [!important] **Kernregel**
> UEFI + GPT ist der moderne Standard. BIOS + MBR ist Legacy – taucht aber in Prüfungen auf. Merke: **UEFI → GPT**, **BIOS → MBR**.

> [!warning] **Achtung Falle**
> Secure Boot kann Linux-Installationen blockieren, wenn der Bootloader nicht signiert ist. Im Unternehmensumfeld muss Secure Boot konfiguriert oder deaktiviert werden. GRUB selbst kann nie direkt editiert werden – immer über `/etc/default/grub` + `update-grub`.
