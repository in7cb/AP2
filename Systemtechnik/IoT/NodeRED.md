Node-RED ist eine **flow-basierte, visuell programmierbare** Laufzeitumgebung auf Basis von Node.js. Entwickelt von IBM, jetzt Open Source (OpenJS Foundation). Primär für IoT-Datenflüsse: Sensordaten erfassen, transformieren, weiterleiten, visualisieren.

## Grundkonzepte

**Flow:** Eine Verbindung von Nodes (links nach rechts), die Daten verarbeitet. Flows laufen parallel.

**Node:** Der Grundbaustein – empfängt eine Nachricht, tut etwas damit, sendet sie weiter.

**Message (`msg`):** Das Objekt, das zwischen Nodes fließt. Wichtigstes Feld: `msg.payload`.

```text
[inject] → [function] → [mqtt out]
   ↓           ↓              ↓
auslösen   verarbeiten    publizieren
```

## Node-Kategorien

| Kategorie | Beispiele | Funktion |
|---|---|---|
| **Input** | inject, mqtt in, http in, file, serial | Daten hereinholen |
| **Output** | mqtt out, http request, debug, email, file | Daten ausgeben |
| **Function** | function, change, switch, template | Daten transformieren |
| **Network** | http request, websocket, tcp | Netzwerkoperationen |
| **Dashboard** | gauge, chart, text, button | UI-Elemente (node-red-dashboard) |
| **Storage** | file, mongodb, influxdb | Persistenz |

## Wichtige Nodes im Detail

### inject
Löst einen Flow manuell oder zeitgesteuert aus (Cron, Intervall). Gibt `msg.payload` mit einem konfigurierten Wert aus.

### function
JavaScript direkt einbetten:
```javascript
// Temperatur von Fahrenheit auf Celsius
msg.payload = (msg.payload - 32) * 5/9;
return msg;
```

### switch
Routing: je nach `msg.payload` oder anderem Feld werden verschiedene Ausgänge aktiviert.

### change
Setzt, löscht oder verschiebt Felder in `msg` ohne Code – z.B. `msg.payload = msg.topic`.

### template
Mustache-Templating für Strings:
```text
Die Temperatur ist {{payload}}°C in Raum {{topic}}
```

### debug
Gibt `msg.payload` (oder das gesamte `msg`-Objekt) in der Debug-Seitenleiste aus – essenziell beim Entwickeln.

## MQTT-Integration

```text
[mqtt in]         ← abonniert Topic "sensors/+/temp"
    ↓
[json]            ← parsed JSON-Payload → JS-Objekt
    ↓
[function]        ← extrahiert Wert, prüft Schwellwert
    ↓          ↓
[debug]    [mqtt out]  ← sendet Alert auf "alerts/high-temp"
```

## Dashboard (node-red-dashboard)

Zusätzliches npm-Paket (`node-red-dashboard`) für einfache Web-UIs:

| Widget | Verwendung |
|---|---|
| `gauge` | Momentanwert (Temperatur, Füllstand) |
| `chart` | Zeitreihendarstellung |
| `text` | Label / Statusanzeige |
| `button` | Trigger für Flows |
| `switch` | Toggle (an/aus) |
| `slider` | Wert einstellen |

Das Dashboard ist unter `http://<ip>:1880/ui` erreichbar.

## Deployment-Modi

- **Full:** Alle Flows neu starten (alle laufenden Verbindungen werden getrennt)
- **Modified Flows:** Nur geänderte Flows neu starten
- **Modified Nodes:** Nur geänderte Nodes neu starten

## Typische IoT-Architektur mit Node-RED

```text
Sensor (MQTT) → [MQTT-Broker] → [Node-RED]
                                     ↓
                              Daten aufbereiten
                              /               \
                    [InfluxDB speichern]   [Grafana Dashboard]
                                    \      /
                                  Alerting bei Schwellwert
                                    ↓
                              [E-Mail / Telegram]
```

> [!tip] **Merksatz**
> Node-RED ist der **Kleber** im IoT-Stack: nimmt Daten von überall (MQTT, HTTP, Serial), transformiert sie und schickt sie weiter – ohne eigene Programmierkenntnisse zwingend nötig.

> [!important] **Kernregel**
> Alle Daten zwischen Nodes fließen als `msg`-Objekt. Das Pflichtfeld ist `msg.payload` – alle anderen Felder sind optional und frei wählbar.
