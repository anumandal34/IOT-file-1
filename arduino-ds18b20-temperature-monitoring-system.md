---
title: "Arduino DS18B20 Temperature Monitoring System"
description: "A simple Arduino project that measures temperature using a DS18B20 digital temperature sensor and displays readings in Celsius and Fahrenheit."
category: "IoT"
level: "Beginner"
technologies:
  - Arduino Uno
  - DS18B20 Temperature Sensor
  - OneWire Communication
  - DallasTemperature Library
  - Serial Monitor
---

# Arduino DS18B20 Temperature Monitoring System

## Introduction

The Arduino DS18B20 Temperature Monitoring System is a beginner-friendly project that measures temperature using a digital DS18B20 temperature sensor.

The DS18B20 sensor communicates with the Arduino through the OneWire communication protocol. The Arduino reads the temperature and displays the results in two units:

- Celsius (°C)
- Fahrenheit (°F)

The temperature readings are displayed in the Arduino Serial Monitor and updated every second.

![Arduino DS18B20 Temperature Monitoring System](/images/projects/iot/arduino-ds18b20-temperature-monitoring-system.png)

## Project Objectives

- Understand how the DS18B20 digital temperature sensor works.
- Learn the basics of OneWire communication.
- Connect a digital temperature sensor to Arduino.
- Measure temperature in Celsius and Fahrenheit.
- Display temperature readings through the Serial Monitor.
- Use Arduino libraries to simplify sensor communication.

## Components Required

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| DS18B20 Temperature Sensor | 1 |
| 4.7kΩ Resistor | 1 |
| Breadboard | 1 |
| Jumper Wires | As required |
| USB Cable | 1 |

## Circuit Diagram

Connect the DS18B20 temperature sensor to the Arduino according to the following circuit diagram.

![Arduino DS18B20 Circuit Diagram](/images/projects/iot/arduino-ds18b20-temperature-circuit.png)

## Circuit Connections

### DS18B20 Sensor Connections

For a typical three-wire DS18B20 sensor:

| DS18B20 Pin | Arduino Connection |
|---|---|
| VCC (Red) | 5V |
| GND (Black) | GND |
| DATA (Yellow) | Digital Pin 2 |

Connect a 4.7kΩ pull-up resistor between the DATA pin and 5V.

**Note:** Wire colors can vary between sensor manufacturers. Check the sensor's datasheet or module labels before connecting it. Some DS18B20 modules already include a pull-up resistor.

## Required Arduino Libraries

This project requires two libraries:

1. **OneWire Library** – Handles communication using the OneWire protocol.
2. **DallasTemperature Library** – Provides convenient functions for reading temperature from compatible Dallas and Maxim temperature sensors.

### Installing the Libraries

1. Open the Arduino IDE.
2. Select **Sketch → Include Library → Manage Libraries**.
3. Search for `OneWire`.
4. Install the OneWire library.
5. Search for `DallasTemperature`.
6. Install the DallasTemperature library.
7. Restart the Arduino IDE if necessary.

## Working Principle

The DS18B20 is a digital temperature sensor that communicates with the Arduino using the OneWire protocol.

The project works as follows:

1. The Arduino initializes the OneWire communication interface on digital pin 2.
2. The DallasTemperature library uses this interface to communicate with the sensor.
3. The Arduino requests a temperature measurement.
4. The sensor provides its temperature reading.
5. The Arduino displays the reading in Celsius and Fahrenheit.
6. The process repeats after a one-second delay.

The sketch uses sensor index `0`, which refers to the first detected temperature sensor on the OneWire bus.

## Arduino Source Code

Upload the following code to your Arduino Uno:

```cpp
#include <OneWire.h>
#include <DallasTemperature.h>

// Data wire is connected to the Arduino digital pin 2
#define ONE_WIRE_BUS 2

// Setup a OneWire instance to communicate with any OneWire devices
OneWire oneWire(ONE_WIRE_BUS);

// Pass our OneWire reference to Dallas Temperature sensor
DallasTemperature sensors(&oneWire);

void setup(void)
{
  // Start serial communication for debugging purposes
  Serial.begin(9600);

  // Start up the library
  sensors.begin();
}

void loop(void) {
  // Request temperature from all devices on the bus
  sensors.requestTemperatures();

  Serial.print("Celsius temperature: ");

  // 0 refers to the first sensor on the OneWire bus
  Serial.print(sensors.getTempCByIndex(0));

  Serial.print(" - Fahrenheit temperature: ");

  Serial.println(sensors.getTempFByIndex(0));

  delay(1000);
}
```

## Code Explanation

### 1. Include Required Libraries

```cpp
#include <OneWire.h>
#include <DallasTemperature.h>
```

These libraries allow the Arduino to communicate with the DS18B20 sensor.

- `OneWire.h` handles OneWire communication.
- `DallasTemperature.h` provides functions for reading temperature.

### 2. Define the Sensor Pin

```cpp
#define ONE_WIRE_BUS 2
```

This defines digital pin 2 as the communication pin for the DS18B20 sensor.

### 3. Create a OneWire Object

```cpp
OneWire oneWire(ONE_WIRE_BUS);
```

This creates a OneWire communication object using digital pin 2.

### 4. Initialize the Temperature Sensor

```cpp
DallasTemperature sensors(&oneWire);
```

This creates a DallasTemperature object and connects it to the OneWire interface.

### 5. Initialize Serial Communication

```cpp
Serial.begin(9600);
```

This starts serial communication at 9600 baud so temperature readings can be displayed in the Serial Monitor.

### 6. Start the Sensor Library

```cpp
sensors.begin();
```

This initializes the temperature sensor library and prepares it to communicate with connected sensors.

### 7. Request Temperature Readings

```cpp
sensors.requestTemperatures();
```

This sends a temperature measurement request to all compatible devices on the OneWire bus.

### 8. Read Celsius Temperature

```cpp
sensors.getTempCByIndex(0);
```

This retrieves the temperature in Celsius from the first sensor on the OneWire bus.

### 9. Read Fahrenheit Temperature

```cpp
sensors.getTempFByIndex(0);
```

This retrieves the temperature in Fahrenheit from the first sensor.

### 10. Add a Delay

```cpp
delay(1000);
```

The Arduino waits one second before requesting another temperature measurement.

## Implementation Steps

1. Place the DS18B20 sensor on the breadboard.
2. Connect its VCC pin to Arduino 5V.
3. Connect its GND pin to Arduino GND.
4. Connect its DATA pin to digital pin 2.
5. Connect a 4.7kΩ resistor between DATA and 5V, unless the module already includes one.
6. Connect the Arduino to your computer using a USB cable.
7. Open the Arduino IDE.
8. Install the OneWire and DallasTemperature libraries.
9. Copy the source code into the Arduino IDE.
10. Select the correct Arduino board and COM port.
11. Upload the program.
12. Open the Serial Monitor.
13. Set the baud rate to 9600.
14. Observe the temperature readings in Celsius and Fahrenheit.

## Expected Output

After uploading the program, the Arduino continuously requests temperature measurements from the DS18B20 sensor.

Example Serial Monitor output:

```text
Celsius temperature: 25.00 - Fahrenheit temperature: 77.00
Celsius temperature: 25.06 - Fahrenheit temperature: 77.11
Celsius temperature: 25.13 - Fahrenheit temperature: 77.23
Celsius temperature: 25.19 - Fahrenheit temperature: 77.34
```

![Arduino DS18B20 Serial Monitor Output](/images/projects/iot/arduino-ds18b20-temperature-serial-monitor.png)

*Note: These are illustrative readings. Actual values depend on the surrounding temperature.*

## Troubleshooting

| Problem | Possible Cause | Solution |
|---|---|---|
| Temperature shows -127°C | Sensor not detected or communication problem | Check DATA, VCC, and GND connections |
| Temperature shows 85°C | Sensor may be showing its power-on default reading | Check initialization and measurement timing |
| Serial Monitor shows no output | Incorrect baud rate or COM port | Select 9600 baud and the correct port |
| Compilation error | Required library is missing | Install OneWire and DallasTemperature |
| Temperature readings are unstable | Loose wiring or electrical interference | Check connections and sensor placement |
| Sensor is not detected | Missing pull-up resistor | Add a 4.7kΩ resistor between DATA and 5V |

## Applications

- Room temperature monitoring.
- Home automation systems.
- Greenhouse temperature monitoring.
- Environmental monitoring projects.
- Temperature-controlled systems.
- Educational Arduino experiments.
- IoT temperature monitoring applications.

## Advantages

- Provides digital temperature readings.
- Supports Celsius and Fahrenheit measurements.
- Uses a simple three-wire connection.
- Supports multiple sensors on one OneWire bus.
- Requires only a small number of components.
- Suitable for beginner and intermediate Arduino projects.

## Limitations

- Requires the OneWire and DallasTemperature libraries.
- Needs a pull-up resistor when using a bare sensor without an onboard resistor.
- The basic project only displays readings through the Serial Monitor.
- It does not provide wireless monitoring.
- The supplied sketch reads only the first sensor on the OneWire bus.

## Future Improvements

- Add an LCD or OLED display.
- Connect an ESP8266 or ESP32 for Wi-Fi monitoring.
- Send temperature readings to a mobile application.
- Store temperature data in a database.
- Add a buzzer for high-temperature alerts.
- Add a relay to control a fan automatically.
- Monitor multiple DS18B20 sensors simultaneously.

## Conclusion

The Arduino DS18B20 Temperature Monitoring System demonstrates how to measure temperature using a digital sensor and the OneWire communication protocol.

By using the OneWire and DallasTemperature libraries, the Arduino can request temperature readings and display them in Celsius and Fahrenheit through the Serial Monitor.

This project is a useful introduction to digital sensors, Arduino libraries, and serial communication. It can also be expanded into a more advanced IoT-based environmental monitoring system.
