IoT (Internet of Things) bezeichnet die Vernetzung physischer Objekte mit dem Internet zur Datenerfassung und -steuerung – **IIoT** (Industrial IoT) ist das industrielle Pendant mit höheren Anforderungen an Zuverlässigkeit, Sicherheit und Echtzeit.

## IT vs. OT

**IT** (Information Technology) verarbeitet Daten für Geschäftsprozesse. **OT** (Operational Technology) steuert physische Prozesse (Maschinen, Anlagen, Infrastruktur).

| Merkmal | IT | OT |
|---|---|---|
| Ziel | Datenverfügbarkeit, Vertraulichkeit | Prozessstabilität, Verfügbarkeit |
| Updates | regelmäßig, automatisch | selten, geplant (Wartungsfenster) |
| Lebenszyklen | 3–5 Jahre | 15–30 Jahre |
| Protokolle | TCP/IP, HTTP, TLS | Modbus, PROFIBUS, OPC-UA, DNP3 |
| Ausfalltoleranz | Minuten akzeptabel | oft Null (Sicherheitsrisiko) |
| Typische Systeme | Server, PCs, Cloud | SPS, SCADA, DCS, RTU |

> [!warning] **Achtung Falle**
> OT-Systeme wurden jahrzehntelang ohne Sicherheitskonzepte gebaut – direkte IT/OT-Kopplung ist ein massives Angriffsziel. Stuxnet hat das demonstriert.

## IIoT und Industry 4.0

**Industry 4.0** beschreibt die vierte industrielle Revolution: cyber-physische Systeme (CPS), IoT und Cloud verschmelzen mit der Produktion.

**Kernkonzepte:**
- **CPS (Cyber-Physical Systems):** Maschinen, die sich selbst überwachen und anpassen
- **Smart Factory:** vollständig vernetzte, autonome Fertigung
- **Digitaler Zwilling (Digital Twin):** virtuelle Echtzeit-Kopie einer physischen Anlage
- **Predictive Maintenance:** Wartung auf Basis von Sensordaten – bevor etwas ausfällt

## Purdue-Modell (ISA-95)

Das Purdue-Modell strukturiert industrielle Netzwerke in Zonen (Hierarchieebenen):

```text
Level 4/5:  Unternehmens-IT (ERP, Cloud, Internet)
             ↕  DMZ / Firewall
Level 3:    MES (Manufacturing Execution System), Historian
             ↕  Firewall
Level 2:    SCADA, HMI, Prozessvisualisierung
Level 1:    SPS (PLC), DCS – direkte Steuerlogik
Level 0:    Feldebene – Sensoren, Aktoren, Maschinen
```

> [!important] **Kernregel**
> Zwischen IT (Level 4/5) und OT (Level 0–3) gehört immer eine **DMZ mit Firewall**. Direktverbindungen sind ein Sicherheitsproblem.

## IoT-Architektur (typisch)

```text
Sensor/Aktor → Gateway (Edge) → Broker/Cloud → Anwendung/Dashboard
   (MQTT/CoAP)     (Protokollkonvertierung)   (InfluxDB, Grafana)
```

**Schichten:**
- **Perception Layer:** physische Sensoren/Aktoren, Microcontroller
- **Network Layer:** WLAN, LoRaWAN, NB-IoT, Ethernet → Datentransport
- **Application Layer:** Datenverarbeitung, Dashboards, Steuerlogik

## Typische Anwendungsfelder

| Bereich | Beispiele |
|---|---|
| Smart Home | Heizungssteuerung, Beleuchtung, Sicherheitskameras |
| Smart City | Parkplatzsensoren, Straßenbeleuchtung, Müllentsorgung |
| Industrie (IIoT) | Predictive Maintenance, Asset Tracking, Qualitätskontrolle |
| Logistik | Flottentracking, Temperaturüberwachung (Kühlkette) |
| Landwirtschaft | Bodenfeuchtesensoren, autonome Bewässerung |
| Gesundheit | Wearables, Remote Patient Monitoring |

## Relevante OT-Protokolle

| Protokoll | Einsatz |
|---|---|
| **Modbus** (RTU/TCP) | ältester Standard, einfach, weit verbreitet in SPS |
| **OPC-UA** | moderner, sicherer IT/OT-Integrationsstandard (TCP, HTTPS) |
| **PROFIBUS** | industrieller Feldbus (Siemens-Umfeld), seriell |
| **PROFINET** | Ethernet-basierter Nachfolger von PROFIBUS |
| **DNP3** | Energieversorgung, SCADA (Fernwirktechnik) |
| **EtherNet/IP** | Industrielles Ethernet (Rockwell/Allen-Bradley) |

> [!tip] **Merksatz**
> **Modbus = alt aber überall**. **OPC-UA = die Brücke zwischen IT und OT** (sicher, standardisiert, herstellerunabhängig).
