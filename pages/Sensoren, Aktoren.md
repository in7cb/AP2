[[IoT|zurück]]

---

# Sensoren & Aktoren

**Sensoren** wandeln physikalische Größen in elektrische Signale um (Messen). **Aktoren** wandeln elektrische Signale in physikalische Aktionen um (Steuern). Zusammen bilden sie die Schnittstelle zwischen digitaler und physischer Welt.

## Sensoren

### Ausgangssignaltypen

| Typ | Beschreibung | Beispiel |
|---|---|---|
| **Digital** | HIGH/LOW, definierter Schwellwert | PIR-Bewegungsmelder, Reed-Kontakt |
| **Analog** | kontinuierliche Spannung (0–5V, 0–3,3V) | Fotowiderstand (LDR), Poti |
| **Protokollbasiert** | I²C, SPI, UART (kalibriert) | BME280, DHT22, GPS |
| **PWM-Ausgang** | Pulsweite trägt Information | manche CO₂-Sensoren |

### Typische Sensortypen

| Messgröße | Sensor | Interface | Besonderheit |
|---|---|---|---|
| Temperatur | DS18B20 | 1-Wire | Kette mehrerer Sensoren möglich |
| Temperatur | NTC-Thermistor | Analog | Widerstand sinkt bei Wärme |
| Temp + Feuchte | DHT22 / DHT11 | Digital (1-pin) | DHT22 genauer, DHT11 billiger |
| Temp + Feuchte + Druck | BME280 | I²C / SPI | sehr präzise, beliebt |
| Helligkeit | LDR (Fotowiderstand) | Analog | Widerstand sinkt bei Licht |
| Bewegung | PIR (HC-SR501) | Digital | Passiv-Infrarot, Menschen |
| Abstand | HC-SR04 (Ultraschall) | Digital (Trig/Echo) | 2 cm – 4 m |
| Abstand | VL53L0X | I²C | TOF (Time of Flight), präziser |
| Gas / CO₂ | MQ-Sensoren (MQ-2, MQ-135) | Analog | Aufwärmzeit beachten! |
| Beschleunigung | MPU6050 (6-Achsen) | I²C | Accel + Gyro |
| Bodenfeuchte | kapazitiver Sensor | Analog | resistiver Typ korrodiert |
| Strom | ACS712 | Analog | Hall-Sensor, ±5/20/30A |

### Kenngrößen von Sensoren

| Kenngröße | Bedeutung |
|---|---|
| **Messbereich** | Minimaler und maximaler Wert |
| **Auflösung** | Kleinste erkennbare Änderung |
| **Genauigkeit** | Abweichung vom Realwert (±x%) |
| **Präzision** | Wiederholbarkeit der Messung |
| **Ansprechzeit** | Zeit bis zur stabilen Ausgabe |
| **Offset/Drift** | Langzeitverschiebung des Messwertes |

> [!warning] **Achtung Falle**
> **Genauigkeit ≠ Präzision!** Ein Sensor kann präzise (reproduzierbar) aber ungenau (falscher Absolutwert) sein. Kalibrierung korrigiert Offset, nicht Präzision.

### ADC – Analog-Digital-Converter

Sensoren mit Analogausgang müssen vom µC digitalisiert werden:

```text
Spannung (0–5V) → ADC (10 Bit) → Digitalwert (0–1023)

Formel: Spannung = Digitalwert × (Vref / 2^Auflösung)
        = 512 × (5.0 / 1024) = 2.5V
```

Wichtig: Referenzspannung (Vref) muss stabil sein → keine Messung direkt an Batterie.

## Aktoren

**Aktoren** nehmen Steuersignale entgegen und führen mechanische, thermische, optische oder akustische Aktionen aus.

### Typen und Ansteuerung

| Aktor | Ansteuerung | Besonderheit |
|---|---|---|
| **LED** | Digital / PWM | Vorwiderstand nötig! |
| **Relais** | Digital (über Transistor) | schaltet 230V AC, galvanische Trennung |
| **MOSFET / Transistor** | Digital / PWM | für höhere Ströme als µC liefert |
| **Servo** | PWM (50Hz, 1–2ms Puls) | 0°–180°, präzise Winkelsteuerung |
| **DC-Motor** | PWM + H-Brücke (L298N) | Richtung + Geschwindigkeit |
| **Schrittmotor** | Schrittsignale (A4988, TMC2208) | sehr präzise, ohne Encoder |
| **Buzzer** | Digital / PWM | aktiv (intern) oder passiv (Ton durch PWM) |
| **Display (LCD, OLED)** | I²C / SPI | Ausgabe von Text/Grafik |
| **RGB-LED-Streifen** | Digital (WS2812B = NeoPixel) | 1-Wire-Protokoll, je LED steuerbar |

### Relais – wichtig für 230V-Schalter

```text
µC-Pin (5V, 20mA) → Transistor/Optokoppler → Relais-Spule
                                              → Kontakt öffnet/schließt 230V-Kreis
```

Relais-Module haben meist eingebauten Transistor + Freilaufdiode (Schutz vor Induktionsspannung).

> [!important] **Kernregel**
> Ein µC-GPIO kann typisch **20–40 mA** liefern. Alles was mehr braucht (Motor, Relais, viele LEDs) braucht einen **Transistor oder Treiber-IC** als Verstärker.

### Servo-Ansteuerung

```cpp
#include <Servo.h>
Servo meinServo;

void setup() {
    meinServo.attach(9);  // Pin 9
}

void loop() {
    meinServo.write(0);    // 0°
    delay(1000);
    meinServo.write(90);   // 90°
    delay(1000);
    meinServo.write(180);  // 180°
    delay(1000);
}
```

## Sensor-Aktor-Kette (typisch IoT)

```text
Physikalische Größe
        ↓
    [Sensor]          z.B. BME280
        ↓ (I²C)
  [Microcontroller]   z.B. ESP32
        ↓ (WLAN/MQTT)
   [MQTT-Broker]
        ↓
   [Node-RED]         Logik: Wenn Temp > 25°C → Lüfter an
        ↓ (MQTT)
  [Microcontroller]   z.B. Arduino
        ↓ (PWM)
    [Aktor]           z.B. Lüfter-Motor
```

> [!tip] **Merksatz**
> **Sensor liest**, **Aktor schreibt**. Der µC ist der Übersetzer dazwischen – er digitalisiert Analogsignale (ADC) und erzeugt Steuersignale (PWM, Digital, Bus).
