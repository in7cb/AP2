[[IoT|zurück]]

---

# Microcontroller Grundlagen, GPIO & Bussysteme

Ein **Microcontroller (µC)** ist ein vollständiger Computer auf einem Chip: CPU, RAM, Flash, I/O-Ports und Peripherie (Timer, ADC, UART, SPI, I2C) in einem Gehäuse. Konzipiert für eingebettete, ressourcensparende Steuerungsaufgaben.

## Microcontroller vs. Microprocessor vs. SBC

| | Microcontroller (µC) | Microprocessor (µP) | SBC (Single Board Computer) |
|---|---|---|---|
| Beispiele | Arduino (ATmega328), ESP32, STM32 | Intel Core i5, AMD Ryzen | Raspberry Pi |
| OS | kein OS / RTOS | Windows, Linux | Linux, Windows IoT |
| RAM | KB bis wenige MB | GB | GB |
| Peripherie | integriert (ADC, GPIO, Bus) | extern | extern + OS-Treiber |
| Echtzeit | ja | nein (OS-Scheduling) | begrenzt |
| Energieverbrauch | sehr gering (mW) | hoch (W) | mittel (W) |
| Einsatz | Sensoren, Aktoren, Steuerung | PC-Anwendungen | Gateway, Edge-Computing |

> [!important] **Kernregel**
> µC für **Steuerung und Sensoren** (Echtzeit, wenig Energie), SBC für **Verarbeitung und Vernetzung** (Linux, Netzwerk, Node-RED).

## GPIO – General Purpose Input/Output

GPIO-Pins sind flexibel konfigurierbare Ein-/Ausgangspins. Konfiguration per Software.

### Digitale Pins

```text
OUTPUT:  Pin auf HIGH (3,3V oder 5V) oder LOW (0V) setzen
INPUT:   Spannungspegel lesen → HIGH oder LOW
INPUT_PULLUP: interner Pull-up-Widerstand aktiv (Pin ist HIGH wenn nichts angeschlossen)
```

```cpp
// Arduino Beispiel
pinMode(13, OUTPUT);         // Pin 13 als Ausgang
digitalWrite(13, HIGH);      // LED an

pinMode(2, INPUT_PULLUP);    // Pin 2 mit Pull-up
int val = digitalRead(2);    // 0 = Taster gedrückt
```

### Analoge Pins (ADC)

Microcontroller haben eingebaute **ADC (Analog-Digital-Converter)**:
- Arduino Uno: 10 Bit → 1024 Schritte, 0–5V → 0–1023
- ESP32: 12 Bit → 4096 Schritte, 0–3,3V → 0–4095

```cpp
int wert = analogRead(A0);         // 0–1023
float spannung = wert * (5.0 / 1023.0);  // Umrechnung in Volt
```

### PWM (Pulse Width Modulation)

Simulation eines analogen Ausgangs durch schnelles An/Aus-Schalten. Frequenz fix, **Duty Cycle** (Einschaltzeit) variabel.

```text
Duty Cycle 0%   → immer LOW  → LED aus
Duty Cycle 50%  → 50% HIGH   → LED halbe Helligkeit
Duty Cycle 100% → immer HIGH → LED voll an
```

```cpp
analogWrite(9, 128);  // 50% Duty Cycle (0–255)
```

Anwendungen: LED-Dimmer, Motorsteuerung, Servo, Lautstärkeregelung.

## Bussysteme

Kommunikation zwischen µC und Peripherie (Sensoren, Displays, ICs).

### I²C (Inter-Integrated Circuit)

**2 Leitungen:** SDA (Daten) + SCL (Takt) + GND. **Multi-Master, Multi-Slave** – bis zu 127 Slaves über 7-Bit-Adresse.

| Eigenschaft | Wert |
|---|---|
| Leitungen | 2 (SDA, SCL) |
| Geschwindigkeit | 100 kbit/s (Standard), 400 kbit/s (Fast), 3,4 Mbit/s (High Speed) |
| Adressierung | 7-Bit (127 Geräte), 10-Bit erweiterbar |
| Topologie | Bus (alle hängen parallel dran) |
| Pull-ups | externe Widerstände nötig |

```text
Master → START → Adresse+R/W → ACK → Daten → STOP
```

Typische I²C-Geräte: BME280 (Temp/Feuchte/Druck), OLED-Displays, MPU6050 (Gyro).

### SPI (Serial Peripheral Interface)

**4 Leitungen:** MOSI, MISO, SCK, CS (pro Slave). **Vollduplex**, kein ACK, schneller als I²C.

| Eigenschaft | Wert |
|---|---|
| Leitungen | 4 (MOSI, MISO, SCK, CS) + 1 CS pro Slave |
| Geschwindigkeit | bis 50+ Mbit/s |
| Topologie | Stern (je ein CS pro Slave) |
| Duplex | vollduplex |
| ACK | nein |

```text
MOSI = Master Out Slave In
MISO = Master In Slave Out
SCK  = Serial Clock
CS   = Chip Select (LOW = Slave aktiv)
```

Typische SPI-Geräte: SD-Karten, TFT-Displays, RFID-Reader (RC522), Flash-Chips.

### UART / USART

**Asynchrone** serielle Kommunikation, **2 Leitungen:** TX, RX. Kein gemeinsamer Takt – Baudrate muss auf beiden Seiten identisch sein.

| Eigenschaft | Wert |
|---|---|
| Leitungen | 2 (TX, RX) |
| Baudrate | typisch 9600, 115200 bps |
| Synchronisation | asynchron (Start-/Stoppbit) |
| Verbindung | Punkt-zu-Punkt |

Typisch: GPS-Module, Bluetooth (HC-05), GSM-Module, PC-Verbindung (USB-Serial).

```text
Startbit | 8 Datenbits | Paritätsbit (optional) | Stoppbit
```

### 1-Wire

**1 Leitung** (+ GND) für Daten und Stromversorgung. Sehr einfach, langsam.
Typisches Gerät: DS18B20 (Temperatursensor) – besonders beliebt für Außentemperaturen.

## I²C vs. SPI vs. UART – Vergleich

| | I²C | SPI | UART |
|---|---|---|---|
| Leitungen | 2 | 4 (+1/Slave) | 2 |
| Geschwindigkeit | mittel | schnell | langsam–mittel |
| Multi-Slave | ja (Adressierung) | ja (je CS-Pin) | nein |
| Duplex | halbduplex | vollduplex | vollduplex |
| Einsatz | Sensoren, kurze Strecken | SD, Display, Flash | GPS, BT, PC |

> [!tip] **Merksatz**
> **I²C** = wenig Pins, viele Geräte, mittelschnell. **SPI** = viele Pins, sehr schnell, vollduplex. **UART** = einfachste Punkt-zu-Punkt-Verbindung, asynchron.
