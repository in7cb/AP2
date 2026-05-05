[[Server, Rechenzentrum, Cloud|zurück]]

---

# Linux & Windows-Server, Vergleich

Beide Plattformen haben klare Stärken – die Wahl hängt von Anwendungsszenario, Lizenzkosten und Team-Know-how ab.

## Allgemeiner Vergleich

| Kriterium | Linux | Windows Server |
|---|---|---|
| Lizenz | meist Open Source (kostenfrei) | kostenpflichtig (pro Kern + CALs) |
| GUI | optional (CLI bevorzugt) | standardmäßig vorhanden (GUI oder Core) |
| CLI | bash, zsh (sehr mächtig) | PowerShell, CMD |
| Remote-Zugriff | SSH (Standard) | RDP (Remote Desktop), WinRM |
| Dateisystem | ext4, XFS, Btrfs, ZFS | NTFS, ReFS |
| Verzeichnisdienst | LDAP, Samba, FreeIPA | Active Directory (AD DS) |
| Stabilität | sehr hoch (oft jahrelanger Uptime) | gut, aber häufiger Reboots (Patches) |
| Verbreitung | ~80% der Webserver, Cloud | Unternehmens-Intranet, Microsoft-Dienste |

## Linux-Server

### Distributionen im Server-Einsatz

| Distribution | Einsatz | Besonderheit |
|---|---|---|
| **Ubuntu Server** | Cloud, Web, Container | LTS (5 Jahre Support), große Community |
| **Debian** | stabil, konservativ | sehr stabiles Paketsystem |
| **RHEL** | Enterprise | Red Hat Support, zertifiziert für viele Apps |
| **Rocky Linux / AlmaLinux** | RHEL-kompatibel | kostenfreie RHEL-Alternative |
| **SUSE Linux Enterprise** | SAP, Enterprise | starke Enterprise-Zertifizierungen |

### Typische Linux-Server-Rollen

| Rolle | Technologie |
|---|---|
| Webserver | Apache, Nginx |
| Appserver | Node.js, Python, Java (Tomcat) |
| Datenbankserver | MySQL, MariaDB, PostgreSQL |
| Mailserver | Postfix, Dovecot |
| DNS-Server | BIND, Unbound |
| DHCP-Server | ISC DHCP, dnsmasq |
| Containerplattform | Docker, Kubernetes |
| Virtualisierung | KVM, Proxmox |

### Paketverwaltung

```bash
# Debian/Ubuntu
apt update && apt upgrade -y
apt install nginx

# RHEL/Rocky
dnf update -y
dnf install httpd

# Dienst-Management (systemd)
systemctl start nginx
systemctl enable nginx
systemctl status nginx
```

### Benutzer und Rechte (kurz)

```bash
# Benutzer anlegen
useradd -m -s /bin/bash username
passwd username

# Root-Rechte via sudo
usermod -aG sudo username

# Dateirechte (rwx)
chmod 755 /var/www/html
chown www-data:www-data /var/www/html
```

## Windows Server

### Editionen

| Edition | Zielgruppe |
|---|---|
| **Standard** | für die meisten Unternehmensserver |
| **Datacenter** | unbegrenzte VMs, RZ-Einsatz |
| **Essentials** | KMU, max. 25 User / 50 Geräte |
| **Core** | kein GUI, weniger Overhead (empfohlen für Server) |

### Lizenzierung

- Lizenzierung **pro physischem Kern** (mind. 16 Kerne pro Server)
- **CAL** (Client Access License): pro Nutzer oder Gerät, das auf den Server zugreift
- Exception: Web-Server, Öffentlich zugängliche Dienste brauchen keine CALs

> [!warning] **Achtung Falle**
> Für jede Rolle (z.B. RDS – Remote Desktop Services) braucht man zusätzlich spezielle CALs. Windows-Lizenzierung ist komplex – häufige Prüfungsfalle!

### Typische Windows-Server-Rollen

| Rolle | Funktion |
|---|---|
| **AD DS** | Active Directory Domain Services – zentrales Verzeichnis für User/PCs |
| **DNS** | Name Resolution (oft mit AD kombiniert) |
| **DHCP** | IP-Vergabe |
| **File Server** | SMB-Freigaben (\\server\share) |
| **RDS** | Remote Desktop Services – Terminal-Server |
| **IIS** | Webserver für .NET / ASP.NET-Anwendungen |
| **Hyper-V** | Hypervisor (Typ 1) |
| **WSUS** | Windows Server Update Services – Patch-Management |

### Active Directory – Grundstruktur

```text
Forest
└── Domain (company.local)
    ├── Organizational Units (OUs)
    │   ├── OU: Benutzer
    │   ├── OU: Computer
    │   └── OU: Server
    ├── Benutzerkonten
    ├── Computerkonten
    └── Gruppenrichtlinien (GPOs)
```

- **GPO** (Group Policy Object): Einstellungen für User/Computer automatisch verteilen
- **AD-Replikation**: mehrere Domain Controller (DC) replizieren die AD-Datenbank
- **LDAP**: Protokoll für AD-Abfragen (Port 389 / 636 für LDAPS)
- **Kerberos**: Authentifizierungsprotokoll in AD (Port 88)

### Remote-Zugriff

```text
RDP (Remote Desktop Protocol):
- Port 3389 (TCP)
- vollständige GUI-Session
- Client: mstsc.exe / Remote Desktop Client

SSH unter Windows Server:
- seit Windows Server 2019 nativ möglich
- OpenSSH Server als Feature installierbar
```

## Entscheidungshilfe

| Szenario | Empfehlung |
|---|---|
| Webserver, Cloud, Container | Linux |
| Microsoft-Ökosystem, .NET, Active Directory | Windows Server |
| SAP-Systeme | Linux (RHEL/SUSE) oder Windows |
| Kosten minimieren | Linux |
| Zentrales User-Management im Unternehmen | Windows Server (AD) |
| Hoher Automatisierungsgrad | beide (PowerShell vs. bash) |

> [!tip] **Merksatz**
> Linux = **flexibel, kosteneffizient, Cloud-nativ**. Windows Server = **Active Directory, GPOs, Microsoft-Integration**. In der Praxis: hybride Umgebungen mit beiden.

> [!important] **Kernregel**
> Active Directory ist der zentrale Unterschied: kein anderes OS bietet nativ AD DS. Linux-Alternativen (Samba, FreeIPA) existieren, sind aber komplexer. Windows-Lizenzierung = Kerne × Preis + CALs.
