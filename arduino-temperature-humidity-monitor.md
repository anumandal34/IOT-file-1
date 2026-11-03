---
title: "Arduino Temperature and Humidity Monitoring System Using DHT11"
description: "Build an Arduino-based temperature and humidity monitoring system using a DHT11 sensor. Read temperature in Celsius and Fahrenheit, humidity, and calculated heat index through the Serial Monitor."
category: "IoT"
level: "Beginner"
technologies:
  - Arduino
  - DHT11 Temperature and Humidity Sensor
  - C++
  - Arduino IDE
---

# Arduino Temperature and Humidity Monitoring System Using DHT11

## Introduction

The Arduino Temperature and Humidity Monitoring System is a beginner-friendly IoT project that measures the surrounding temperature and humidity using a DHT11 sensor.

The system uses an Arduino board to collect environmental data and displays the readings through the Arduino IDE Serial Monitor. It measures temperature in both Celsius and Fahrenheit, relative humidity as a percentage, and calculates the heat index in Fahrenheit.

This project is useful for learning how environmental sensors work and how Arduino communicates with sensors to collect real-time data.

![Arduino Temperature and Humidity Monitoring System](/images/projects/iot/arduino-temperature-humidity-monitoring-system.png)

## Project Objectives

- Learn how to interface a DHT11 sensor with Arduino.
- Measure temperature in Celsius and Fahrenheit.
- Measure relative humidity in percentage.
- Calculate the heat index.
- Display sensor readings using the Serial Monitor.
- Understand how Arduino reads and processes sensor data.

## Components Required

| S.N. | Component | Quantity |
|---|---|---:|
| 1 | Arduino Uno | 1 |
| 2 | DHT11 Temperature and Humidity Sensor | 1 |
| 3 | Breadboard | 1 |
| 4 | Jumper Wires | As required |
| 5 | USB Cable | 1 |

## Circuit Diagram

The DHT11 sensor is connected to the Arduino using three connections: power, ground, and data.

![Arduino DHT11 Sensor Circuit Diagram](/images/projects/iot/arduino-dht11-temperature-humidity-circuit.png)

## Circuit Connections

For a common three-pin DHT11 sensor module, use the following connections:

| DHT11 Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| DATA | Digital Pin 2 |
| GND | GND |

**Note:** Check the labels and pinout of your particular DHT11 module before connecting it. If you are using a bare four-pin DHT11 sensor rather than a module, it typically requires a pull-up resistor between VCC and DATA.

## Required Arduino Library

This project uses the DHT library to communicate with the sensor.

Install the library before uploading the code:

1. Open Arduino IDE.
2. Go to **Sketch → Include Library → Manage Libraries**.
3. Search for **DHT sensor library**.
4. Install **DHT sensor library by Adafruit**.
5. Install **Adafruit Unified Sensor** if the library installation or IDE indicates that it is required.

## Working Principle

The DHT11 sensor measures the surrounding temperature and relative humidity and sends the readings to the Arduino through its digital data pin.

The Arduino performs the following operations:

1. Initializes serial communication at 9600 baud.
2. Initializes the DHT11 sensor.
3. Waits for two seconds between readings.
4. Reads relative humidity.
5. Reads temperature in Celsius.
6. Reads temperature in Fahrenheit.
7. Checks whether the sensor readings are valid.
8. Calculates the heat index using Fahrenheit temperature and humidity.
9. Displays all readings in the Serial Monitor.

The process repeats continuously, allowing the user to monitor environmental conditions.

## Arduino Source Code

```cpp
#include "DHT.h"

#define DHTPIN 2     // what pin we're connected to

// Uncomment whatever type you're using!
#define DHTTYPE DHT11   // DHT 11
//#define DHTTYPE DHT22   // DHT 22 (AM2302)
//#define DHTTYPE DHT21   // DHT 21 (AM2301)

// Initialize DHT sensor
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  Serial.println("DHTxx test!");

  dht.begin();
}

void loop() {
  // Wait a few seconds between measurements.
  delay(2000);

  // Read humidity
  float h = dht.readHumidity();

  // Read temperature as Celsius
  float t = dht.readTemperature();

  // Read temperature as Fahrenheit
  float f = dht.readTemperature(true);

  // Check if any reads failed
  if (isnan(h) || isnan(t) || isnan(f)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  // Compute heat index in Fahrenheit
  float hi = dht.computeHeatIndex(f, h);

  Serial.print("Humidity: ");
  Serial.print(h);
  Serial.print(" %\t");

  Serial.print("Temperature: ");
  Serial.print(t);
  Serial.print(" *C ");

  Serial.print(f);
  Serial.print(" *F\t");

  Serial.print("Heat index: ");
  Serial.print(hi);
  Serial.println(" *F");
}
```

## Code Explanation

### 1. Include the DHT Library

```cpp
#include "DHT.h"
```

This includes the DHT library, which provides functions for communicating with DHT temperature and humidity sensors.

### 2. Define Sensor Pin and Type

```cpp
#define DHTPIN 2
#define DHTTYPE DHT11
```

- `DHTPIN 2` specifies that the sensor's DATA pin is connected to Arduino digital pin 2.
- `DHTTYPE DHT11` selects the DHT11 sensor.

To use another supported sensor, select the matching type:

```cpp
//#define DHTTYPE DHT22
//#define DHTTYPE DHT21
```

Only one sensor type should be active at a time.

### 3. Initialize the DHT Sensor

```cpp
DHT dht(DHTPIN, DHTTYPE);
```

This creates a DHT sensor object using the selected digital pin and sensor type.

### 4. Setup Function

```cpp
void setup() {
  Serial.begin(9600);
  Serial.println("DHTxx test!");
  dht.begin();
}
```

The setup function runs once when the Arduino starts.

- `Serial.begin(9600)` starts serial communication.
- `Serial.println()` displays an initialization message.
- `dht.begin()` initializes the DHT sensor.

### 5. Read Sensor Data

```cpp
float h = dht.readHumidity();
float t = dht.readTemperature();
float f = dht.readTemperature(true);
```

These commands collect three measurements:

- `h`: Relative humidity in percentage.
- `t`: Temperature in Celsius.
- `f`: Temperature in Fahrenheit.

### 6. Check Sensor Readings

```cpp
if (isnan(h) || isnan(t) || isnan(f)) {
  Serial.println("Failed to read from DHT sensor!");
  return;
}
```

The `isnan()` function checks whether a reading is invalid. If any reading fails, the Arduino displays an error message and skips the remaining operations for that cycle.

### 7. Calculate Heat Index

```cpp
float hi = dht.computeHeatIndex(f, h);
```

This calculates the heat index using temperature in Fahrenheit and relative humidity.

The heat index represents how hot the environment may feel when humidity is considered.

### 8. Display Results

```cpp
Serial.print("Humidity: ");
Serial.print(h);
```

The `Serial.print()` and `Serial.println()` functions display the sensor measurements in the Arduino IDE Serial Monitor.

## Implementation Steps

1. Collect all the required components.
2. Connect the DHT11 VCC pin to Arduino 5V.
3. Connect the DHT11 DATA pin to Arduino digital pin 2.
4. Connect the DHT11 GND pin to Arduino GND.
5. Open Arduino IDE on your computer.
6. Install the required DHT library.
7. Copy the Arduino source code into the IDE.
8. Select the correct Arduino board and COM port.
9. Upload the code to the Arduino.
10. Open the Serial Monitor.
11. Set the baud rate to **9600**.
12. Observe the temperature, humidity, and heat index readings.

## Expected Output

After uploading the code successfully, the Serial Monitor displays readings similar to the following:

```text
DHTxx test!
Humidity: 55.00 %    Temperature: 28.00 *C 82.40 *F    Heat index: 83.10 *F
Humidity: 56.00 %    Temperature: 29.00 *C 84.20 *F    Heat index: 85.20 *F
Humidity: 54.00 %    Temperature: 28.00 *C 82.40 *F    Heat index: 83.00 *F
```

**Note:** These are example readings. Actual values depend on the surrounding environmental conditions and sensor accuracy.

![Arduino DHT11 Sensor Serial Monitor Output](/images/projects/iot/arduino-dht11-serial-monitor-output.png)

## Troubleshooting

| Problem | Possible Cause | Solution |
|---|---|---|
| Failed to read from DHT sensor | Incorrect wiring | Check VCC, DATA, and GND connections. |
| No readings displayed | Incorrect COM port | Select the correct Arduino port. |
| Incorrect temperature readings | Wrong sensor type selected | Check the `DHTTYPE` definition. |
| Compilation error | Missing DHT library | Install the Adafruit DHT sensor library. |
| Unstable readings | Loose connections | Check and secure the jumper wires. |
| Serial Monitor displays unreadable text | Incorrect baud rate | Set the baud rate to 9600. |

## Applications

- Indoor temperature and humidity monitoring.
- Weather monitoring systems.
- Greenhouse environmental monitoring.
- Smart home automation.
- Classroom electronics experiments.
- Basic environmental data collection.

## Advantages

- Easy to build and understand.
- Requires only a few components.
- Measures both temperature and humidity.
- Displays temperature in Celsius and Fahrenheit.
- Calculates heat index.
- Suitable for beginner-level Arduino projects.

## Limitations

- The DHT11 has limited measurement accuracy and range compared with more advanced sensors.
- It is relatively slow and is not suitable for rapid environmental changes.
- Sensor readings can be affected by incorrect wiring and environmental conditions.
- The project displays data through the Serial Monitor and does not provide remote monitoring by itself.

## Future Improvements

- Add an LCD or OLED display to show readings without a computer.
- Add Wi-Fi connectivity using an ESP8266 or ESP32.
- Upload sensor readings to an IoT platform.
- Store temperature and humidity data for later analysis.
- Add alerts when temperature or humidity exceeds selected limits.
- Create a web dashboard for remote environmental monitoring.

## Conclusion

The Arduino Temperature and Humidity Monitoring System using DHT11 is a simple and practical IoT project for beginners. It demonstrates how an Arduino can collect environmental data, process sensor readings, calculate heat index, and display the results through the Serial Monitor.

By completing this project, learners gain practical experience with Arduino programming, sensor interfacing, library installation, and serial communication. It also provides a foundation for developing more advanced IoT-based environmental monitoring systems.