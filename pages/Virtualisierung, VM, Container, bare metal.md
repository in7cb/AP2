[[Server, Rechenzentrum, Cloud|zurück]]

---

# Virtualisierung, VM, Container, bare metal

Virtualisierung abstrahiert Hardware-Ressourcen, sodass mehrere isolierte Systeme auf einer physischen Maschine laufen – von vollständiger Hardware-Emulation (VM) bis zu Prozess-Isolation (Container).

## Bare Metal

Direktinstallation eines OS auf physischer Hardware – kein Hypervisor dazwischen.

| Merkmal | Wert |
|---|---|
| Performance | maximal (kein Overhead) |
| Isolation | vollständig (eigene Hardware) |
| Flexibilität | gering (1 OS pro Maschine) |
| Einsatz | Hochlast-DBs, HPC, Gaming-Server |

## Hypervisor & VMs

Ein **Hypervisor** (Virtual Machine Monitor) teilt physische Ressourcen auf mehrere VMs auf. Jede VM hat ein eigenes vollständiges Gast-OS.

### Typ 1 – Bare-Metal-Hypervisor

Läuft direkt auf der Hardware, kein Host-OS darunter.

```text
┌──────────┬──────────┬──────────┐
│  VM 1    │  VM 2    │  VM 3    │
├──────────┴──────────┴──────────┤
│         Hypervisor Typ 1        │
├─────────────────────────────────┤
│           Hardware               │
└─────────────────────────────────┘
```

Beispiele: **VMware ESXi**, **Microsoft Hyper-V** (Server), **KVM** (Linux-Kernel-integriert), **Xen**

### Typ 2 – Hosted Hypervisor

Läuft als Anwendung auf einem Host-OS.

```text
┌──────────┬──────────┐
│  VM 1    │  VM 2    │
├──────────┴──────────┤
│   Hypervisor Typ 2  │
├─────────────────────┤
│       Host-OS        │
├─────────────────────┤
│       Hardware       │
└─────────────────────┘
```

Beispiele: **VirtualBox**, **VMware Workstation**, **Parallels**

> [!tip] **Merksatz Typ 1 vs. 2**
> Typ 1 = **direkt auf Eisen** → Produktion. Typ 2 = **auf OS drauf** → Desktop/Test.

### VM-Eigenschaften

- **vCPU, vRAM, vDisk**: virtuelle Hardware-Zuteilung, unabhängig vom Gast-OS
- **Snapshots**: Zustand einfrieren und wiederherstellen
- **Live-Migration**: laufende VM zwischen Hosts verschieben (VMware vMotion, KVM live-migrate)
- **Overcommitting**: mehr vCPUs/vRAM zuweisen als physisch vorhanden (riskant bei Last-Peaks)

## Container

Container teilen den **Kernel des Host-OS** und isolieren nur den Prozess-Namespace. Kein eigenes Kernel-Image, dadurch sehr leichtgewichtig.

```text
┌─────────┬─────────┬─────────┐
│Container│Container│Container│
│ App A   │ App B   │ App C   │
├─────────┴─────────┴─────────┤
│      Container Runtime       │
│      (Docker, containerd)    │
├──────────────────────────────┤
│         Host-OS / Kernel     │
├──────────────────────────────┤
│           Hardware           │
└──────────────────────────────┘
```

### Docker – Kernkonzepte

| Begriff | Bedeutung |
|---|---|
| **Image** | Read-only Template (Schichten/Layers) |
| **Container** | laufende Instanz eines Images |
| **Dockerfile** | Bauanleitung für ein Image |
| **Registry** | Image-Ablage (z.B. Docker Hub) |
| **Volume** | persistenter Speicher außerhalb des Containers |

```bash
docker build -t myapp:1.0 .          # Image bauen
docker run -d -p 8080:80 myapp:1.0   # Container starten
docker ps                             # laufende Container
docker stop <id>                      # Container stoppen
docker exec -it <id> bash            # Shell im Container
```

### Orchestrierung – Kubernetes (K8s)

Wenn viele Container verwaltet werden müssen → **Kubernetes**:
- **Pod**: kleinste Einheit, enthält 1–n Container
- **Deployment**: definiert gewünschten Zustand (z.B. 3 Replicas)
- **Service**: stabiler Endpunkt für Pods (Load Balancing)
- **Namespace**: logische Trennung im Cluster

## Vergleich: Bare Metal vs. VM vs. Container

| Kriterium | Bare Metal | VM | Container |
|---|---|---|---|
| Start-Zeit | Minuten | 30 s – 2 min | Sekunden |
| Overhead | keiner | mittel (Gast-OS) | minimal |
| Isolation | physisch | stark (eigenes OS) | Prozess-Level |
| Portabilität | gering | mittel | hoch |
| Density | 1 OS | ~10–50 VMs/Host | ~100–1000/Host |
| Einsatz | DBs, HPC | Server-Konsolidierung | Microservices, CI/CD |

> [!warning] **Achtung Falle**
> Container sind **kein Ersatz für VMs** bei starker Isolation (z.B. Multi-Tenant-Umgebungen). Container-Ausbrüche (Container Escape) sind zwar selten, aber möglich – bei VMs muss der Angreifer den Hypervisor überwinden.

> [!important] **Kernregel**
> VM = eigenes Gast-OS, eigener Kernel. Container = gemeinsamer Host-Kernel, nur Prozess-Isolation. Typ-1-Hypervisor läuft direkt auf Hardware → Produktionseinsatz.
