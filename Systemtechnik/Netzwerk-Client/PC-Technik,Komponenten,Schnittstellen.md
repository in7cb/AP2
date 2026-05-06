Ein PC besteht aus Kernkomponenten (CPU, RAM, Mainboard, Speicher) sowie einer Vielzahl standardisierter Schnittstellen für Peripherie und Erweiterungen.

## Kernkomponenten

| Komponente | Funktion | Kenngrößen |
|---|---|---|
| **CPU** | Recheneinheit, führt Instruktionen aus | Kerne, Takt (GHz), Cache, TDP |
| **RAM** | Arbeitsspeicher (flüchtig) | DDR4/DDR5, Kapazität (GB), Takt (MHz) |
| **Mainboard** | Verbindet alle Komponenten | Sockel (CPU), Chipsatz, Formfaktor |
| **SSD/HDD** | Persistenter Massenspeicher | Kapazität, Geschwindigkeit (MB/s, IOPS) |
| **GPU** | Grafikverarbeitung, auch Compute | VRAM, CUDA/ROCm Kerne |
| **PSU** | Netzteil, wandelt Netzspannung um | Watt, 80+ Effizienzklasse |
| **Kühlung** | Wärmeabfuhr CPU/GPU | Luftkühlung, AiO-Wasserkühlung |

## Formfaktoren Mainboard

| Formfaktor | Maße | Verwendung |
|---|---|---|
| ATX | 305 × 244 mm | Standard-Desktop |
| Micro-ATX | 244 × 244 mm | Kompakt-Desktop |
| Mini-ITX | 170 × 170 mm | HTPC, NUC, kleine Systeme |
| E-ATX | 305 × 330 mm | Workstation/HEDT |

## Speicherschnittstellen

| Schnittstelle | Max. Bandbreite | Typische Verwendung |
|---|---|---|
| SATA III | 600 MB/s | 2,5"-SSD, HDD |
| M.2 SATA | 600 MB/s | Kompakte SSDs |
| M.2 NVMe (PCIe 3.0 ×4) | ~3.500 MB/s | Schnelle SSDs |
| M.2 NVMe (PCIe 4.0 ×4) | ~7.000 MB/s | High-End SSDs |
| PCIe 5.0 NVMe | ~14.000 MB/s | Aktuelle Top-SSDs |

> [!tip] **M.2 ≠ NVMe**
> M.2 ist der **Formfaktor** (Steckplatz). Darin kann entweder SATA oder NVMe stecken. NVMe ist das **Protokoll** (schnell). Immer beide Angaben prüfen!

## PCIe

PCIe (Peripheral Component Interconnect Express) ist der Hauptbus für Erweiterungskarten.

- **Lanes:** ×1, ×4, ×8, ×16 (mehr = mehr Bandbreite)
- **Generationen:** PCIe 3.0 → 4.0 → 5.0 (je Generation ~2× Bandbreite)
- **Abwärtskompatibel:** PCIe 4.0 Karte läuft in PCIe 3.0 Slot (mit reduzierter Geschwindigkeit)

## Externe Schnittstellen (Anschlüsse)

### USB

| Version | Name | Max. Geschwindigkeit | Stecker |
|---|---|---|---|
| USB 2.0 | Hi-Speed | 480 Mbit/s | A, B, Mini, Micro |
| USB 3.2 Gen 1 | SuperSpeed | 5 Gbit/s | A, C |
| USB 3.2 Gen 2 | SuperSpeed+ | 10 Gbit/s | A, C |
| USB 3.2 Gen 2×2 | SuperSpeed+ | 20 Gbit/s | C |
| USB4 / Thunderbolt 4 | — | 40 Gbit/s | C |

> [!warning] **USB Namenschaos**
> USB 3.0 = USB 3.1 Gen 1 = USB 3.2 Gen 1 = SuperSpeed. Die Benennung wurde mehrfach umbenannt – immer auf Gbit/s schauen, nicht den Marketingnamen.

### Video-Ausgänge

| Schnittstelle | Max. Auflösung/Refresh | Besonderheit |
|---|---|---|
| HDMI 2.0 | 4K@60Hz | Audio integriert, weit verbreitet |
| HDMI 2.1 | 4K@144Hz, 8K@30Hz | Gaming, neuere TVs |
| DisplayPort 1.4 | 4K@144Hz, 8K@30Hz | Daisy-Chain möglich |
| DisplayPort 2.1 | 16K | Aktueller Standard |
| VGA | 1080p (analog) | Veraltet, kein 4K |
| DVI | 1080p/1440p | Weitgehend abgelöst |

### Weitere Schnittstellen

| Schnittstelle | Verwendung |
|---|---|
| RJ45 (LAN) | Ethernet 1G/2,5G/10G |
| 3,5mm Klinke | Audio In/Out (oft farbkodiert) |
| Thunderbolt 3/4 | Daten 40 Gbit/s, Video, PCIe extern |
| SD-Kartenleser | Speicherkarten |
| Bluetooth | Kurzstrecken-Funk, Peripherie |
| Wi-Fi (intern) | WLAN-Adapter onboard |

## RAM – Wichtiges

```text
DDR4-3200 CL16  →  Takt: 3200 MHz (effektiv), Latenz: 16 Takte
DDR5-6000 CL30  →  DDR5 hat höheren Takt, aber auch höhere Latenzen
```

- **Dual-Channel:** 2 RAM-Riegel in richtigen Slots = doppelte Busbreite → Performancegewinn
- **ECC-RAM:** Fehlerkorrektur, nur mit passender CPU/Mainboard (Workstation/Server)

> [!important] **Kernregel Speicherhierarchie**
> Register → L1 Cache → L2 Cache → L3 Cache → RAM → SSD/HDD  
> Geschwindigkeit nimmt ab, Kapazität nimmt zu.
