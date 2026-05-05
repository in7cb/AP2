[[IoT|zurück]]

---

# MQTT & Broker

MQTT (Message Queuing Telemetry Transport) ist ein leichtgewichtiges **Publish/Subscribe-Protokoll** auf Basis von TCP, entwickelt für ressourcenschwache Geräte und schlechte Netzwerke. Standard-Port: **1883** (unverschlüsselt), **8883** (TLS).

## Grundprinzip: Publish / Subscribe

Kein direkter Client-zu-Client-Kontakt – alles läuft über einen zentralen **Broker**:

```text
Publisher              Broker              Subscriber
  Sensor  ──publish──→  [MQTT-Broker]  ──deliver──→  Dashboard
                         (Mosquitto,                  App
                          HiveMQ, etc.)               andere Sensoren
```

- **Publisher:** sendet Nachrichten an ein Topic (kennt Subscriber nicht)
- **Subscriber:** abonniert Topics (kennt Publisher nicht)
- **Broker:** empfängt, filtert und verteilt alle Nachrichten

## Topics

Topics sind hierarchische, `/`-getrennte Strings – **kein vorher definiertes Schema** nötig:

```text
Beispiele:
  home/wohnzimmer/temperatur
  fabrik/linie1/maschine3/status
  sensors/+/humidity        ← Wildcard + (eine Ebene)
  sensors/#                 ← Wildcard # (alle Ebenen darunter)
```

| Wildcard | Bedeutung | Beispiel |
|---|---|---|
| `+` | genau eine Ebene | `home/+/temperatur` → alle Räume |
| `#` | alle Unterebenen | `home/#` → alles unter home |

> [!warning] **Achtung Falle**
> `#` darf nur am **Ende** eines Topics stehen. `home/#/licht` ist ungültig.

## QoS-Level (Quality of Service)

| QoS | Name | Garantie | Overhead |
|---|---|---|---|
| 0 | At most once | Nachricht kommt ggf. gar nicht an (Fire & Forget) | minimal |
| 1 | At least once | Nachricht kommt mindestens 1× an (Duplikate möglich) | mittel |
| 2 | Exactly once | Nachricht kommt genau 1× an (4-Wege-Handshake) | hoch |

> [!important] **Kernregel**
> QoS 0 für unkritische Sensorwerte (Temperatur alle 10s). QoS 1 für Events. QoS 2 für Steuerbefehle, wo Duplikate gefährlich sind (z.B. Ventil öffnen/schließen).

## Retained Messages

Ein Publisher kann eine Nachricht mit `retain=true` senden – der Broker speichert die **letzte Nachricht** pro Topic. Neue Subscriber erhalten sie sofort beim Abonnieren (kein Warten auf nächste Nachricht).

```text
→ sinnvoll für: aktueller Status, Konfiguration, letzte Messung
```

## Last Will & Testament (LWT)

Bei der Verbindung registriert ein Client eine **Will-Nachricht**. Bricht die Verbindung unerwartet ab (Timeout, Stromausfall), sendet der Broker diese automatisch.

```text
Anwendung: Gerät meldet sich als "offline" wenn Verbindung abbricht
           → anderen Clients/Dashboard kann reagieren
```

## MQTT-Verbindungsparameter

```text
Client → Broker: CONNECT
  - ClientID (eindeutig)
  - CleanSession (true = keine gespeicherten Sessions)
  - KeepAlive (Intervall für PINGREQ)
  - Will Topic/Message/QoS/Retain
  - Username/Password (optional)

Broker → Client: CONNACK (Return Code 0 = OK)
```

## Keep-Alive & PING

Der Client sendet regelmäßig `PINGREQ`, Broker antwortet mit `PINGRESP`. Bleibt das Ping aus (1,5× Keep-Alive-Intervall), gilt die Verbindung als unterbrochen → LWT wird ausgelöst.

## Broker-Implementierungen

| Broker | Besonderheit |
|---|---|
| **Eclipse Mosquitto** | Open Source, leichtgewichtig, Standard für Raspberry Pi & Co. |
| **HiveMQ** | Enterprise-Feature, skalierbar, Cluster-fähig |
| **EMQX** | Hochskalierbar (Millionen Verbindungen), Open Source |
| **Mosquitto (cloud)** | als Managed Service bei AWS IoT, Azure IoT Hub etc. |

## MQTT vs. HTTP

| | MQTT | HTTP |
|---|---|---|
| Modell | Pub/Sub, persistent | Request/Response, stateless |
| Overhead | minimal (2 Byte Header) | groß (Headers, TLS-Handshake) |
| Verbindung | persistent (TCP) | kurzlebig (außer HTTP/2) |
| Eignung | IoT, viele kleine Geräte | Web-APIs, Browser |
| Push-fähig | ja (Broker pushed an Subscriber) | nein (Polling nötig) |

> [!tip] **Merksatz**
> **MQTT = Post-Verteiler**: Publisher wirft Brief (Nachricht) in Fach (Topic), Broker verteilt an alle, die das Fach abonniert haben. Der Absender weiß nicht, wer liest.

## MQTT 5 vs. MQTT 3.1.1

MQTT 5 (2019) bringt u.a.:
- **Reason Codes** (detailliertere Fehlermeldungen)
- **Message Expiry Interval** (Nachrichten automatisch verfallen lassen)
- **Shared Subscriptions** (Load-Balancing unter mehreren Subscribern)
- **Request/Response-Pattern** (mit `Response Topic` und `Correlation Data`)
