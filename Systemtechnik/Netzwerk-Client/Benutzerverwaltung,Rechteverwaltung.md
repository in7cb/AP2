Zentrale Nutzerverwaltung ermöglicht einheitliche Authentifizierung und Autorisierung im Netzwerk. Im Windows-Umfeld läuft das über Active Directory, unter Linux via LDAP/PAM.

## Active Directory (AD)

AD ist Microsofts Verzeichnisdienst zur zentralen Verwaltung von Benutzern, Computern, Gruppen und Richtlinien in einer Windows-Domäne.

### Struktur

```text
Forest (höchste Ebene)
└── Domain (z.B. firma.local)
    ├── OU (Organizational Unit)
    │   ├── Benutzer
    │   ├── Computer
    │   └── Gruppen
    └── OU
        └── ...
```

| Begriff | Bedeutung |
|---|---|
| **Forest** | Oberste Grenze; mehrere Domains möglich |
| **Domain** | Verwaltungseinheit (z.B. firma.local) |
| **OU** | Ordner für Objekte, Basis für GPO-Zuweisung |
| **DC** (Domain Controller) | Server der AD hostet (mindestens 1, besser 2+) |
| **LDAP** | Protokoll für Verzeichnisabfragen (Port 389 / 636 TLS) |
| **Kerberos** | Authentifizierungsprotokoll in AD (Port 88) |
| **DNS** | AD basiert zwingend auf DNS für Namensauflösung |

> [!important] **Kernregel**
> AD ohne DNS = nicht funktionsfähig. Der DC hostet meistens auch den DNS-Dienst.

## Gruppentypen in AD

| Typ | Reichweite | Verwendung |
|---|---|---|
| **Domain Local Group** | Nur in eigener Domain | Ressource zugriffsrechte direkt vergeben |
| **Global Group** | Über Domains hinweg | Benutzer gleicher Domain zusammenfassen |
| **Universal Group** | Forest-weit | Forest-übergreifende Zugriffssteuerung |

> [!tip] **AGDLP-Prinzip**
> **A**ccounts → **G**lobal Groups → **D**omain **L**ocal Groups → **P**ermissions  
> Benutzer in Global Groups, Global Groups in Domain Local Groups, Domain Local Groups bekommen Rechte.

## Gruppenrichtlinien (GPO – Group Policy Objects)

GPOs erlauben zentrale Konfiguration von Systemen und Benutzern.

```text
GPO wird verknüpft mit:  Site / Domain / OU
Anwendungsreihenfolge:   Local → Site → Domain → OU (letzte gewinnt)
```

| Bereich | Beispiele |
|---|---|
| **Computer-Konfiguration** | Passwortrichtlinien, Software-Installation, Firewall |
| **Benutzer-Konfiguration** | Desktop-Hintergrund, Laufwerksmappings, Druckerverteilung |

```bash
# GPO erzwingen (auf Client)
gpupdate /force

# Angewandte GPOs anzeigen
gpresult /r
```

## Windows-Rechtesystem (NTFS-Berechtigungen)

Jede Datei/Ordner hat eine **ACL** (Access Control List) mit ACEs (Access Control Entries).

| Berechtigung | Dateien | Ordner |
|---|---|---|
| Lesen | Datei öffnen/anzeigen | Inhalt anzeigen |
| Schreiben | Datei ändern | Dateien erstellen |
| Ausführen | Programm starten | Ordner betreten |
| Vollzugriff | Alle Rechte inkl. Berechtigungen ändern | dto. |
| Ändern | Lesen + Schreiben + Löschen | dto. |

> [!warning] **Achtung Falle**
> **Effektive Rechte** = Kombination aus NTFS + Freigabe-Berechtigungen.  
> Beim Netzwerkzugriff gilt das **restriktivere** der beiden Rechte.  
> Lokaler Zugriff: nur NTFS zählt.

## Linux-Rechtesystem

### chmod / chown

```bash
ls -l /etc/passwd
# -rw-r--r-- 1 root root 2048 Jan 1 10:00 /etc/passwd
#  rwxrwxrwx
#  │││││││││
#  ││││││└┴┘ Others: r--  = 4
#  │││└┴┘    Group:  r--  = 4
#  └┴┘        Owner: rw-  = 6
# → chmod 644

chmod 755 script.sh    # rwxr-xr-x
chmod u+x script.sh    # Owner: +Ausführen
chown user:gruppe datei.txt
```

| Oktalwert | Binär | Bedeutung |
|---|---|---|
| 4 | 100 | Read |
| 2 | 010 | Write |
| 1 | 001 | Execute |
| 7 | 111 | rwx |
| 6 | 110 | rw- |
| 5 | 101 | r-x |

### Spezielle Bits

| Bit | Name | Effekt |
|---|---|---|
| SUID (4xxx) | Set User ID | Programm läuft mit Rechten des Dateibesitzers |
| SGID (2xxx) | Set Group ID | Programm läuft mit Gruppenrechten; Ordner: neue Dateien erben Gruppe |
| Sticky (1xxx) | Sticky Bit | Im Ordner kann nur Eigentümer eigene Dateien löschen (z.B. /tmp) |

## RBAC – Role-Based Access Control

Statt individuelle Rechte zu vergeben, werden **Rollen** definiert und Nutzern zugewiesen.

```text
Nutzer → Rolle → Berechtigungen

Beispiel:
  Leon → Rolle "Helpdesk" → Lesen auf OU=User, Passwort-Reset-Recht
  Maria → Rolle "Admin" → Vollzugriff AD
```

Vorteile: Skalierbar, nachvollziehbar, einfachere Compliance-Prüfung.

## Wichtige Konzepte

| Konzept | Beschreibung |
|---|---|
| **SSO** (Single Sign-On) | Einmaliges Login für viele Dienste (Kerberos Ticket) |
| **MFA** | Zweiter Faktor zur Absicherung (TOTP, Hardware-Token) |
| **Least Privilege** | Nutzer bekommt nur minimal notwendige Rechte |
| **Separation of Duties** | Kritische Aufgaben auf mehrere Personen verteilt |
| **PAM** (Privileged Access Management) | Verwaltung von Admin-Zugängen, Session-Recording |

> [!important] **Kernregel**
> Least Privilege + Need-to-Know = Grundprinzipien jeder Rechtevergabe. Niemals Vollzugriff geben, wenn Lesezugriff ausreicht.
