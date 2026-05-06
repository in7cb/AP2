Die vier Deployment-Modelle definieren **wer** die Cloud-Infrastruktur betreibt und **wer** Zugriff hat – unabhängig vom Service-Modell (IaaS/PaaS/SaaS).

## Überblick

| Modell | Betreiber | Nutzer | Infrastruktur |
|---|---|---|---|
| **Private Cloud** | Unternehmen selbst oder beauftragt | nur das Unternehmen | dediziert, on-premise oder hosted |
| **Public Cloud** | Cloud-Anbieter (AWS, Azure, GCP) | beliebige Kunden | geteilt (multi-tenant) |
| **Hybrid Cloud** | beides kombiniert | Unternehmen | on-premise + public |
| **Community Cloud** | Gruppe / Anbieter | bestimmte Gemeinschaft | geteilt, aber nur für die Gruppe |

## Private Cloud

Infrastruktur wird **exklusiv** für eine Organisation betrieben. Kann im eigenen Rechenzentrum oder bei einem Hosting-Anbieter stehen.

**Vorteile:**
- maximale Kontrolle (Compliance, Datenschutz, DSGVO)
- keine Multi-Tenancy → keine Nachbar-Probleme (Noisy Neighbor)
- individuelle Konfiguration und SLAs

**Nachteile:**
- hohe Investitions- und Betriebskosten (CAPEX)
- Skalierung begrenzt durch eigene Hardware
- eigenes IT-Personal notwendig

**Beispiele:** VMware vSphere on-premise, OpenStack, eigene Kubernetes-Cluster

## Public Cloud

Infrastruktur wird von einem Anbieter betrieben und **über das Internet** angeboten. Ressourcen werden mit anderen Kunden geteilt.

**Vorteile:**
- elastische Skalierung (pay-per-use)
- kein eigenes Rechenzentrum nötig
- weltweite Verfügbarkeit und Redundanz
- schnelle Bereitstellung (Self-Service)

**Nachteile:**
- Datenkontrolle beim Anbieter (Datenschutz, Gerichtsbarkeit)
- Abhängigkeit (Vendor Lock-in)
- laufende Kosten (OPEX) können hoch werden

**Anbieter:** AWS, Microsoft Azure, Google Cloud Platform (GCP), Oracle Cloud, Alibaba Cloud

## Hybrid Cloud

Kombination aus Private und Public Cloud – Workloads werden je nach Anforderung verteilt.

```text
┌─────────────────┐          ┌──────────────────┐
│  Private Cloud  │◄────────►│   Public Cloud   │
│  (on-premise)   │  Secure  │  (AWS / Azure)   │
│  sensible Daten │  Link    │  Burst-Workloads │
└─────────────────┘          └──────────────────┘
```

**Typisches Szenario:** Produktionsdaten bleiben on-premise, Testumgebungen oder Lastspitzen (Cloud-Bursting) in der Public Cloud.

**Verbindung:** VPN, Direct Connect (AWS), ExpressRoute (Azure), SD-WAN

**Vorteile:**
- Flexibilität: richtige Cloud für den richtigen Workload
- sensible Daten on-premise, Skalierung in der Public Cloud

**Nachteile:**
- komplex zu managen (zwei Umgebungen)
- Netzwerk zwischen privat und public muss zuverlässig sein

## Community Cloud

Infrastruktur wird von mehreren Organisationen mit **gemeinsamen Interessen** geteilt (z.B. Behörden, Gesundheitswesen, Forschung).

**Beispiele:**
- Bundes-Cloud (gemeinsame IT-Infrastruktur für Bundesbehörden)
- Cloud für Universitäten / Forschungseinrichtungen (DFN-Cloud)
- Healthcare-Cloud (gemeinsame DSGVO-konforme Plattform)

**Vorteile:** geteilte Kosten, gemeinsame Compliance-Anforderungen erfüllt
**Nachteile:** weniger Kontrolle als Private Cloud, Abstimmung unter Teilnehmern nötig

## Entscheidungsmatrix

| Anforderung | Empfehlung |
|---|---|
| DSGVO, max. Kontrolle | Private Cloud |
| schnell skalieren, globale Reichweite | Public Cloud |
| beides – Flexibilität + Compliance | Hybrid Cloud |
| Behörden, geteilte Compliance-Gruppe | Community Cloud |

> [!tip] **Merksatz**
> **P**rivat = **P**ersönlich (nur du). **P**ublic = **P**ublikum (alle). **H**ybrid = **H**alb-halb. **C**ommunity = **C**lub (ausgewählte Gruppe).

> [!important] **Kernregel**
> Die Deployment-Modelle definieren **wer Zugriff hat** – unabhängig davon, ob es IaaS, PaaS oder SaaS ist. Prüfungsfrage kombiniert oft beides.
