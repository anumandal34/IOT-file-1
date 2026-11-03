---
title: "Arduino Rain Sensor Plant Watering Indicator"
description: "A simple Arduino project that uses a rain sensor and two LEDs to indicate whether a plant needs watering."
category: "IoT"
level: "Beginner"
technologies:
  - Arduino Uno
  - Rain Sensor Module
  - Analog Sensor Reading
  - LED Indicators
---

# Arduino Rain Sensor Plant Watering Indicator

## Introduction

The Arduino Rain Sensor Plant Watering Indicator is a beginner-friendly project that uses a rain sensor module to monitor moisture or water detection and indicate whether a plant needs watering.

The sensor sends an analog reading to the Arduino. The Arduino compares this reading with a predefined threshold value and controls two LEDs:

- **Green LED:** Indicates that watering is not needed.
- **Red LED:** Indicates that it is time to water the plant.

The sensor reading and watering message are also displayed in the Serial Monitor.

![Arduino Rain Sensor Plant Watering Indicator](/images/projects/iot/arduino-rain-sensor-plant-watering-indicator.png)

## Project Objectives

- Read analog values from a rain sensor module.
- Understand how Arduino's `analogRead()` function works.
- Compare sensor readings with a threshold value.
- Control two LEDs based on sensor readings.
- Display sensor readings and messages in the Serial Monitor.
- Build a simple plant watering indicator.

## Components Required

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| Rain Sensor Module | 1 |
| Green LED | 1 |
| Red LED | 1 |
| 220Ω Resistor | 2 |
| Breadboard | 1 |
| Jumper Wires | As required |
| USB Cable | 1 |

## Circuit Diagram

Connect the rain sensor module and LEDs to the Arduino according to the following diagram.

![Arduino Rain Sensor Circuit Diagram](/images/projects/iot/arduino-rain-sensor-plant-watering-circuit.png)

## Circuit Connections

### Rain Sensor Connections

| Rain Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| AO (Analog Output) | A0 |

### LED Connections

| LED | Arduino Pin |
|---|---|
| Green LED Anode (+) | Digital Pin 6 through a 220Ω resistor |
| Green LED Cathode (-) | GND |
| Red LED Anode (+) | Digital Pin 7 through a 220Ω resistor |
| Red LED Cathode (-) | GND |

**Note:** The LED resistors protect the LEDs from excessive current. Check your sensor module's pin labels before connecting it.

## Working Principle

The rain sensor detects water or moisture on its sensing surface and produces an analog output voltage.

The Arduino reads this output using analog pin `A0`. The reading is generally between `0` and `1023` on an Arduino Uno.

The program compares the sensor reading with a threshold value of `800`.

The logic in the supplied sketch is:

| Sensor Reading | Green LED | Red LED | Serial Monitor Message |
|---|---|---|---|
| Below 800 | ON | OFF | Doesn't need watering |
| 800 or above | OFF | ON | Time to water your plant |

The threshold can be adjusted depending on the sensor, its installation, and the desired detection level.

## Arduino Source Code

Upload the following code to your Arduino Uno:

```cpp
int rainPin = A0;
int greenLED = 6;
int redLED = 7;

// You can adjust the threshold value
int thresholdValue = 800;

void setup() {
  pinMode(rainPin, INPUT);
  pinMode(greenLED, OUTPUT);
  pinMode(redLED, OUTPUT);

  digitalWrite(greenLED, LOW);
  digitalWrite(redLED, LOW);

  Serial.begin(9600);
}

void loop() {
  // Read the input on analog pin 0
  int sensorValue = analogRead(rainPin);

  Serial.print(sensorValue);

  if (sensorValue < thresholdValue) {
    Serial.println(" - Doesn't need watering");

    digitalWrite(redLED, LOW);
    digitalWrite(greenLED, HIGH);
  }
  else {
    Serial.println(" - Time to water your plant");

    digitalWrite(redLED, HIGH);
    digitalWrite(greenLED, LOW);
  }

  delay(500);
}
```

## Code Explanation

### 1. Define the Pins

```cpp
int rainPin = A0;
int greenLED = 6;
int redLED = 7;
```

These variables define the Arduino pins used by the project.

- `rainPin`: Reads the analog output of the rain sensor.
- `greenLED`: Controls the green LED on digital pin 6.
- `redLED`: Controls the red LED on digital pin 7.

### 2. Set the Threshold Value

```cpp
int thresholdValue = 800;
```

This value determines when the program changes its watering indication.

You can adjust the threshold after observing the sensor readings under different conditions.

### 3. Configure the Pins

```cpp
pinMode(rainPin, INPUT);
pinMode(greenLED, OUTPUT);
pinMode(redLED, OUTPUT);
```

The sensor pin is configured as an input, while both LED pins are configured as outputs.

### 4. Start Serial Communication

```cpp
Serial.begin(9600);
```

This starts serial communication at 9600 baud so the sensor readings and messages can be viewed in the Arduino Serial Monitor.

### 5. Read the Sensor Value

```cpp
int sensorValue = analogRead(rainPin);
```

The Arduino reads the analog output from the sensor through pin `A0`.

### 6. Compare the Reading

```cpp
if (sensorValue < thresholdValue)
```

If the sensor reading is below 800, the program displays the message that watering is not needed and turns on the green LED.

Otherwise, it displays the watering message and turns on the red LED.

### 7. Add a Delay

```cpp
delay(500);
```

The Arduino waits 500 milliseconds before taking the next reading.

## Implementation Steps

1. Connect the rain sensor's VCC pin to Arduino 5V.
2. Connect the rain sensor's GND pin to Arduino GND.
3. Connect the sensor's analog output (AO) to Arduino A0.
4. Connect the green LED to digital pin 6 through a 220Ω resistor.
5. Connect the red LED to digital pin 7 through a 220Ω resistor.
6. Connect the LED cathodes to GND.
7. Connect the Arduino to your computer using a USB cable.
8. Open the Arduino IDE.
9. Select the correct Arduino board and COM port.
10. Copy and upload the source code.
11. Open the Serial Monitor and set the baud rate to 9600.
12. Observe the sensor readings and LED indications as the sensor's moisture conditions change.

## Expected Output

When the Arduino starts, it continuously reads the analog value from the rain sensor.

**Condition 1: Sensor reading below 800**

- Green LED turns ON.
- Red LED turns OFF.
- Serial Monitor displays: `Doesn't need watering`.

**Condition 2: Sensor reading 800 or above**

- Red LED turns ON.
- Green LED turns OFF.
- Serial Monitor displays: `Time to water your plant`.

Example Serial Monitor output:

```text
650 - Doesn't need watering
720 - Doesn't need watering
850 - Time to water your plant
910 - Time to water your plant
700 - Doesn't need watering
```

![Arduino Rain Sensor Serial Monitor Output](/images/projects/iot/arduino-rain-sensor-plant-watering-serial-monitor.png)

**Important:** These readings are examples, not guaranteed sensor values. Actual readings depend on the sensor module and its environment.

## Troubleshooting

| Problem | Possible Cause | Solution |
|---|---|---|
| Sensor always shows the same value | Incorrect wiring or faulty sensor | Check VCC, GND, and AO connections |
| LEDs do not turn ON | Incorrect LED polarity or wiring | Check LED orientation and resistor connections |
| Incorrect watering indication | Threshold is not suitable | Adjust `thresholdValue` |
| Serial Monitor shows no output | Incorrect baud rate or COM port | Select 9600 baud and the correct port |
| Sensor readings fluctuate | Water distribution or electrical noise | Check sensor connections and allow readings to stabilize |

## Applications

- Plant moisture and watering indication.
- Small home gardening projects.
- Basic smart gardening demonstrations.
- Arduino sensor learning experiments.
- Educational IoT projects.
- Simple environmental monitoring systems.

## Advantages

- Easy to build and understand.
- Uses inexpensive components.
- Provides visual indications through LEDs.
- Displays real-time sensor readings.
- Allows threshold adjustment.
- Suitable for Arduino beginners.

## Limitations

- The project only provides a watering indication; it does not automatically operate a water pump.
- The sensor reading depends on the sensor type and environmental conditions.
- The threshold value may need calibration.
- A rain sensor detects water on its sensing surface and does not directly measure the moisture level throughout the plant's soil.
- The project does not store historical sensor readings.

## Future Improvements

- Add an LCD or OLED display to show sensor readings.
- Add a relay module and water pump for automatic watering.
- Use a soil moisture sensor to measure soil moisture directly.
- Add Wi-Fi connectivity using an ESP8266 or ESP32.
- Send watering notifications to a mobile application.
- Store sensor readings for monitoring and analysis.

## Conclusion

The Arduino Rain Sensor Plant Watering Indicator demonstrates how an analog sensor can be used to control output devices based on a predefined threshold.

By reading the sensor through analog pin A0, the Arduino determines which LED should turn on and displays the corresponding message in the Serial Monitor.

This project is a useful introduction to analog sensor reading, conditional statements, and output control. It can also serve as a starting point for developing more advanced smart gardening and automatic irrigation systems.
