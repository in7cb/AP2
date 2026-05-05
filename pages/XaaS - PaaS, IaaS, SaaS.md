[[Server, Rechenzentrum, Cloud|zurück]]

---

# XaaS – IaaS, PaaS, SaaS

**XaaS** = „Everything as a Service" – beschreibt Cloud-Service-Modelle, die definieren, **was** der Anbieter übernimmt und was der Kunde selbst verantwortet.

## Verantwortungsbereiche

```text
                  On-Premise   IaaS         PaaS         SaaS
Applications    │ Kunde       │ Kunde       │ Kunde       │ Anbieter │
Data            │ Kunde       │ Kunde       │ Kunde       │ Anbieter │
Runtime         │ Kunde       │ Kunde       │ Anbieter    │ Anbieter │
Middleware      │ Kunde       │ Kunde       │ Anbieter    │ Anbieter │
OS              │ Kunde       │ Kunde       │ Anbieter    │ Anbieter │
Virtualisierung │ Kunde       │ Anbieter    │ Anbieter    │ Anbieter │
Server          │ Kunde       │ Anbieter    │ Anbieter    │ Anbieter │
Storage         │ Kunde       │ Anbieter    │ Anbieter    │ Anbieter │
Netzwerk        │ Kunde       │ Anbieter    │ Anbieter    │ Anbieter │
```

> [!tip] **Merksatz**
> **I**aaS = **I**ch manage alles außer Hardware. **P**aaS = **P**rogrammieren, Rest Anbieter. **S**aaS = **S**ofort nutzen, nichts managen.

## IaaS – Infrastructure as a Service

Der Anbieter stellt Rechenleistung, Storage und Netzwerk bereit. Der Kunde installiert und verwaltet OS, Middleware und Applikationen selbst.

| Aspekt | Details |
|---|---|
| Kunde verantwortet | OS, Middleware, Runtime, App, Daten |
| Anbieter liefert | VMs, Storage, Netzwerk, Hypervisor |
| Abrechnung | nach Laufzeit, CPU/RAM/Storage-Verbrauch |
| Flexibilität | sehr hoch |
| Verwaltungsaufwand | hoch |

**Typische Dienste:**

| Anbieter | Dienst |
|---|---|
| AWS | EC2 (VMs), S3 (Storage), VPC (Netzwerk) |
| Azure | Virtual Machines, Blob Storage, VNet |
| GCP | Compute Engine, Cloud Storage |

**Einsatz:** Wenn volle Kontrolle über OS und Software nötig ist, z.B. individuelle Server-Konfigurationen, Legacy-Anwendungen.

## PaaS – Platform as a Service

Der Anbieter stellt eine vollständige Entwicklungs- und Laufzeitumgebung bereit. Der Kunde deployt nur seine Applikation und kümmert sich um Daten.

| Aspekt | Details |
|---|---|
| Kunde verantwortet | App-Code, Daten |
| Anbieter liefert | OS, Runtime, Middleware, Skalierung, Backup |
| Abrechnung | nach Ressourcennutzung der App |
| Flexibilität | mittel |
| Verwaltungsaufwand | gering |

**Typische Dienste:**

| Anbieter | Dienst |
|---|---|
| AWS | Elastic Beanstalk, RDS (Managed DB) |
| Azure | App Service, Azure SQL |
| GCP | App Engine, Cloud SQL |
| Heroku | Heroku Platform |

**Einsatz:** Webapplikationen, APIs, Entwicklerteams ohne Infra-Expertise.

## SaaS – Software as a Service

Fertige Anwendung wird komplett vom Anbieter betrieben und über Browser/API bereitgestellt. Der Kunde nutzt die Software, ohne sich um irgendetwas darunter zu kümmern.

| Aspekt | Details |
|---|---|
| Kunde verantwortet | Daten (und Nutzerkonfiguration) |
| Anbieter liefert | alles (App, OS, Infra, Updates) |
| Abrechnung | Abo-Modell (pro Nutzer / Monat) |
| Flexibilität | gering (App ist vorgegeben) |
| Verwaltungsaufwand | minimal |

**Beispiele:** Microsoft 365, Google Workspace, Salesforce, Zoom, Slack, ServiceNow

## Weitere XaaS-Modelle

| Modell | Beschreibung | Beispiele |
|---|---|---|
| **FaaS** | Function as a Service / Serverless – Code wird per Event-Trigger ausgeführt | AWS Lambda, Azure Functions |
| **DBaaS** | Database as a Service | AWS RDS, Azure Cosmos DB |
| **STaaS** | Storage as a Service | AWS S3, Dropbox |
| **CaaS** | Container as a Service | AWS ECS/EKS, Azure AKS |
| **SECaaS** | Security as a Service | Zscaler, Cloudflare |

## Vergleich

| Kriterium | IaaS | PaaS | SaaS |
|---|---|---|---|
| Kontrolle | hoch | mittel | gering |
| Flexibilität | hoch | mittel | gering |
| Aufwand | hoch | mittel | minimal |
| Time-to-Market | langsam | mittel | sofort |
| Zielgruppe | Sysadmins | Entwickler | Endnutzer |

> [!warning] **Achtung Falle**
> Datenschutz: Bei SaaS liegen Daten vollständig beim Anbieter. Bei IaaS liegt die Verantwortung für OS-Sicherheit, Patches und Firewall beim Kunden – Anbieter patcht nur die Hypervisor-Schicht.

> [!important] **Kernregel**
> Je höher das Modell (IaaS → PaaS → SaaS), desto mehr übernimmt der Anbieter – und desto weniger Kontrolle hat der Kunde. Prüfung fragt oft: „Wer ist für X verantwortlich?"
