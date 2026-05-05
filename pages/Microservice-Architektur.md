[[Server, Rechenzentrum, Cloud|zurück]]

---

# Microservice-Architektur

Eine Microservice-Architektur zerlegt eine Anwendung in **kleine, unabhängig deploybare Dienste**, die über APIs kommunizieren – Gegenentwurf zum klassischen Monolithen.

## Monolith vs. Microservices

| Merkmal | Monolith | Microservices |
|---|---|---|
| Deployment | gesamte App auf einmal | jeder Service unabhängig |
| Skalierung | nur als Ganzes | einzelne Services skalierbar |
| Entwicklung | einfacher Einstieg | klare Service-Grenzen nötig |
| Fehlerquelle | ein Fehler → alles down | Fehler isoliert im Service |
| Technologie | einheitlicher Stack | jeder Service eigene Sprache/DB möglich |
| Kommunikation | intern (Funktionsaufruf) | über Netz (REST, gRPC, Message Queue) |
| Team-Organisation | eine Codebase | Teams pro Service (Conway's Law) |

```text
Monolith:                    Microservices:
┌─────────────────┐          ┌───────┐ ┌───────┐ ┌───────┐
│  Auth           │          │ Auth  │ │ Order │ │Payment│
│  Order-Mgmt     │    →     │  :8001│ │ :8002 │ │ :8003 │
│  Payment        │          └───────┘ └───────┘ └───────┘
│  Notification   │               ↕ API-Gateway ↕
└─────────────────┘          ┌─────────────────────┐
       ↕                     │      Client          │
    Client                   └─────────────────────┘
```

## Kernkomponenten

### API-Gateway

Zentraler Eintrittspunkt für alle Clients. Übernimmt:
- **Routing** → Request an den richtigen Service
- **Authentifizierung / Autorisierung**
- **Rate Limiting** (Schutz vor Überlast)
- **SSL-Termination**
- **Load Balancing**

Beispiele: Kong, NGINX, AWS API Gateway, Traefik

### Service Discovery

Microservices starten/stoppen dynamisch – statische IPs funktionieren nicht. Service Discovery löst das:

| Typ | Beschreibung | Beispiel |
|---|---|---|
| **Client-side** | Client fragt Registry, wählt Instanz | Netflix Eureka |
| **Server-side** | Load Balancer fragt Registry | AWS ALB + ECS |

Registry-Beispiele: Consul, etcd, Kubernetes-DNS

### Kommunikation zwischen Services

| Muster | Protokoll | Wann |
|---|---|---|
| **Synchron** | REST (HTTP), gRPC | Request/Response, niedrige Latenz nötig |
| **Asynchron** | Message Queue (RabbitMQ, Kafka) | Events, Entkopplung, hohe Last |

> [!warning] **Achtung Falle**
> Synchrone Kommunikation (REST) zwischen vielen Services erzeugt **Kaskadenfehler**: fällt Service A aus, hängt auch Service B, der A aufruft. Lösung: **Circuit Breaker** (z.B. Hystrix, Resilience4j).

### Containerisierung & Orchestrierung

Microservices laufen typischerweise in Containern → [[Virtualisierung, VM, Container, bare metal]].

**Kubernetes** ist der Standard für Orchestrierung:

| K8s-Konzept | Aufgabe |
|---|---|
| **Pod** | 1 Container (= 1 Microservice-Instanz) |
| **Service** | stabiler DNS-Name + IP für Pods |
| **Deployment** | gewünschte Replikas, Rolling Updates |
| **Ingress** | externer Zugriff, API-Gateway-Funktion |
| **ConfigMap / Secret** | Konfiguration und Credentials |

## Vor- und Nachteile

**Vorteile:**
- unabhängiges Deployment → schnellere Release-Zyklen
- gezielte Skalierung (nur der überlastete Service)
- Technologie-Heterogenität möglich
- bessere Fehlertoleranz (Isolation)

**Nachteile:**
- hohe operative Komplexität (viele Services, viele Repos)
- Netzwerk-Overhead und Latenz durch API-Calls
- Distributed Tracing nötig (Fehlersuche schwieriger)
- Datenkonsistenz schwieriger (jeder Service eigene DB)

## Typische Patterns

| Pattern | Beschreibung |
|---|---|
| **Strangler Fig** | Monolith schrittweise durch Microservices ersetzen |
| **Circuit Breaker** | Fehler isolieren, Fallback auslösen |
| **Saga** | verteilte Transaktionen über mehrere Services |
| **CQRS** | Command und Query trennen (Schreiben / Lesen) |
| **Sidecar** | Hilfs-Container neben Hauptcontainer (Logging, mTLS) |

> [!tip] **Merksatz**
> Microservice = **ein Service, eine Aufgabe**. Kommunikation über API. Deployment unabhängig. Container + Kubernetes = moderner Standard.

> [!important] **Kernregel**
> Microservices erhöhen Flexibilität und Skalierbarkeit auf Kosten der Komplexität. API-Gateway ist der zentrale Einstiegspunkt. Asynchrone Kommunikation (Message Queue) entkoppelt Services zuverlässig.
