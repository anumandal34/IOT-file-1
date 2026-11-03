---
title: "Arduino Soil Moisture Monitoring System"
description: "A beginner-friendly Arduino project that reads an analog sensor value and uses two LEDs to indicate whether a plant needs watering."
category: "IoT"
level: "Beginner"
technologies:
  - Arduino Uno
  - Analog Sensor
  - LED Indicators
  - Serial Monitor
---

# Arduino Soil Moisture Monitoring System

## Introduction

The Arduino Soil Moisture Monitoring System is a simple project that reads an analog sensor value and indicates whether a plant needs watering.

The sensor is connected to analog pin A0 of the Arduino. Based on the sensor reading, the Arduino controls two LEDs:

- **Green LED:** Indicates that watering is not needed.
- **Red LED:** Indicates that it is time to water the plant.

The sensor reading and corresponding message are also displayed in the Arduino Serial Monitor.

![Arduino Soil Moisture Monitoring System](/images/projects/iot/arduino-soil-moisture-monitoring-system.png)

## Project Objectives

- Read analog values from a sensor using Arduino.
- Understand the `analogRead()` function.
- Compare sensor readings with a threshold value.
- Control LEDs using conditional statements.
- Display sensor readings in the Serial Monitor.
- Understand the basics of plant watering indicators.

## Components Required

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| Analog Soil Moisture Sensor | 1 |
| Green LED | 1 |
| Red LED | 1 |
| 220Ω Resistor | 2 |
| Breadboard | 1 |
| Jumper Wires | As required |
| USB Cable | 1 |

## Circuit Diagram

Connect the analog sensor and LEDs to the Arduino according to the following circuit diagram.

![Arduino Soil Moisture Sensor Circuit Diagram](/images/projects/iot/arduino-soil-moisture-monitoring-circuit.png)

## Circuit Connections

### Sensor Connections

| Sensor Pin | Arduino Pin |
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

**Note:** The source code uses analog pin A0 for the sensor. Connect the sensor's analog output (AO), not its digital output (DO). Check your module's voltage requirements before powering it.

## Working Principle

The analog sensor produces an output value that the Arduino reads through analog pin A0.

On an Arduino Uno, `analogRead()` normally returns a value between 0 and 1023.

The program compares this reading with a predefined threshold value of 800.

The supplied sketch follows this logic:

| Sensor Reading | Green LED | Red LED | Serial Monitor Message |
|---|---|---|---|
| Below 800 | ON | OFF | Doesn't need watering |
| 800 or above | OFF | ON | Time to water your plant |

The threshold can be adjusted according to the sensor and the conditions in which it is used.

## Arduino Source Code

```cpp
int rainPin = A0;
int greenLED = 6;
int redLED = 7;

// you can adjust the threshold value
int thresholdValue = 800;

void setup(){
  pinMode(rainPin, INPUT);
  pinMode(greenLED, OUTPUT);
  pinMode(redLED, OUTPUT);
  digitalWrite(greenLED, LOW);
  digitalWrite(redLED, LOW);
  Serial.begin(9600);
}

void loop() {
  // read the input on analog pin 0:
  int sensorValue = analogRead(rainPin);
  Serial.print(sensorValue);

  if(sensorValue < thresholdValue){
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

These variables define the Arduino pins used in the project.

- `rainPin`: Analog sensor input connected to A0.
- `greenLED`: Green LED connected to digital pin 6.
- `redLED`: Red LED connected to digital pin 7.

The original sketch names the sensor variable `rainPin`, although the uploaded filename is `soil_moisture_sensor.ino`.

### 2. Set the Threshold Value

```cpp
int thresholdValue = 800;
```

This variable stores the threshold used to determine which watering message and LED state should be displayed.

You can change this value after testing your sensor.

### 3. Configure the Pins

```cpp
pinMode(rainPin, INPUT);
pinMode(greenLED, OUTPUT);
pinMode(redLED, OUTPUT);
```

The sensor pin is configured as an input, while both LED pins are configured as outputs.

### 4. Set the Initial LED States

```cpp
digitalWrite(greenLED, LOW);
digitalWrite(redLED, LOW);
```

Both LEDs are switched OFF when the Arduino starts.

### 5. Start Serial Communication

```cpp
Serial.begin(9600);
```

This starts serial communication at 9600 baud so that sensor readings and messages can be displayed in the Serial Monitor.

### 6. Read the Sensor Value

```cpp
int sensorValue = analogRead(rainPin);
```

The Arduino reads the analog sensor output through pin A0 and stores the result in `sensorValue`.

### 7. Compare the Reading

```cpp
if(sensorValue < thresholdValue)
```

If the sensor reading is below 800, the program displays the message that watering is not needed and turns ON the green LED.

Otherwise, the program displays the watering message and turns ON the red LED.

### 8. Add a Delay

```cpp
delay(500);
```

The Arduino waits for 500 milliseconds before taking another sensor reading.

## Implementation Steps

1. Connect the sensor VCC pin to Arduino 5V.
2. Connect the sensor GND pin to Arduino GND.
3. Connect the sensor analog output (AO) to Arduino A0.
4. Connect the green LED to digital pin 6 through a 220Ω resistor.
5. Connect the red LED to digital pin 7 through a 220Ω resistor.
6. Connect both LED cathodes to GND.
7. Connect the Arduino to your computer using a USB cable.
8. Open the Arduino IDE.
9. Select the correct Arduino board and COM port.
10. Copy the source code into the Arduino IDE.
11. Upload the code to the Arduino.
12. Open the Serial Monitor and select 9600 baud.
13. Observe the sensor readings and LED indications as the sensor conditions change.

## Expected Output

After uploading the program, the Arduino continuously reads the sensor value and compares it with the threshold.

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

![Arduino Soil Moisture Sensor Serial Monitor Output](/images/projects/iot/arduino-soil-moisture-monitoring-serial-monitor.png)

*Note: These are example readings. Actual values depend on the sensor, its installation, and the surrounding conditions.*

## Troubleshooting

| Problem | Possible Cause | Solution |
|---|---|---|
| Sensor reading does not change | Incorrect wiring or faulty sensor | Check VCC, GND, and AO connections |
| LEDs do not turn ON | Incorrect LED polarity or wiring | Check LED orientation and resistor connections |
| Watering message is incorrect | Threshold is unsuitable | Adjust `thresholdValue` after testing |
| Serial Monitor shows no output | Incorrect baud rate or COM port | Select 9600 baud and the correct port |
| Sensor readings fluctuate | Unstable connections or changing sensor conditions | Check wiring and sensor placement |

## Applications

- Plant watering indication.
- Home gardening experiments.
- Basic smart gardening demonstrations.
- Arduino sensor learning projects.
- Educational IoT projects.
- Simple plant monitoring systems.

## Advantages

- Easy to build and understand.
- Uses inexpensive electronic components.
- Provides visual feedback through LEDs.
- Displays sensor readings in real time.
- Allows threshold adjustment.
- Suitable for beginners learning Arduino programming.

## Limitations

- The project only indicates whether watering is needed; it does not operate a water pump.
- The threshold may need calibration for the selected sensor.
- Sensor readings can vary depending on the sensor type and installation.
- The project does not store historical readings.
- The code does not provide wireless monitoring.

## Future Improvements

- Add an LCD or OLED display to show sensor readings.
- Add a relay module and water pump for automatic irrigation.
- Add Wi-Fi connectivity using ESP8266 or ESP32.
- Send watering notifications to a mobile application.
- Store sensor readings for long-term monitoring.
- Add multiple sensors to monitor several plants.

## Conclusion

The Arduino Soil Moisture Monitoring System demonstrates how an analog sensor can be used to control LEDs based on a predefined threshold.

The Arduino reads the sensor value through analog pin A0, compares it with the threshold, and displays the corresponding watering message in the Serial Monitor.

This project introduces important Arduino concepts such as analog input, conditional statements, digital output, and serial communication. It can also be expanded into a more advanced smart irrigation system.

**Source-code note:** The uploaded sketch uses the variable name `rainPin`. This document preserves the original code and its threshold logic. Confirm that your connected sensor is a soil moisture sensor before describing the measured value as soil moisture.
