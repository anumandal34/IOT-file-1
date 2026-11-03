---
title: "Arduino BMP180 Temperature, Pressure, and Altitude Monitoring System"
description: "Build an Arduino environmental monitoring system using the BMP180 barometric pressure sensor to measure temperature, atmospheric pressure, sea-level pressure, and estimated altitude."
category: "IoT"
level: "Beginner"
technologies:
  - Arduino
  - BMP180 Barometric Pressure Sensor
  - I2C Communication
  - C++
  - Arduino IDE
---

# Arduino BMP180 Temperature, Pressure, and Altitude Monitoring System

## Introduction

The Arduino BMP180 Temperature, Pressure, and Altitude Monitoring System is an IoT-based environmental monitoring project that uses the BMP180 barometric pressure sensor to measure temperature and atmospheric pressure.

The BMP180 sensor communicates with Arduino through the I2C communication protocol. Using the sensor readings, the Arduino calculates sea-level-compensated pressure and estimates altitude.

The measured information is displayed through the Arduino IDE Serial Monitor in different units, including Celsius, Fahrenheit, millibars, inches of mercury, meters, and feet.

This project is useful for learning about environmental sensors, atmospheric pressure, I2C communication, and basic weather monitoring systems.

![Arduino BMP180 Temperature Pressure Altitude Monitoring System](/images/projects/iot/arduino-bmp180-temperature-pressure-altitude-monitoring-system.png)

## Project Objectives

- Learn how to interface the BMP180 sensor with Arduino.
- Understand I2C communication.
- Measure temperature in Celsius and Fahrenheit.
- Measure absolute atmospheric pressure.
- Calculate sea-level-compensated pressure.
- Estimate altitude using pressure measurements.
- Display environmental measurements through the Serial Monitor.

## Components Required

| S.N. | Component | Quantity |
|---|---|---:|
| 1 | Arduino Uno | 1 |
| 2 | BMP180 Barometric Pressure Sensor Module | 1 |
| 3 | Breadboard | 1 |
| 4 | Jumper Wires | As required |
| 5 | USB Cable | 1 |

## Circuit Diagram

The BMP180 sensor communicates with Arduino using the I2C interface. Connect the sensor's SDA and SCL pins to the corresponding Arduino I2C pins.

![Arduino BMP180 Sensor Circuit Diagram](/images/projects/iot/arduino-bmp180-temperature-pressure-circuit.png)

## Circuit Connections

For Arduino Uno:

| BMP180 Sensor Pin | Arduino Pin |
|---|---|
| VIN / VCC | Module-compatible power supply |
| GND | GND |
| SDA | A4 |
| SCL | A5 |

**Important:** Check your BMP180 breakout board's specifications before connecting its power pin. Some modules support 5V input through an onboard regulator, while bare sensors require a lower voltage. Do not apply 5V directly to a bare BMP180 sensor.

## Required Arduino Library

This project uses the SparkFun BMP180 library.

To install it:

1. Open Arduino IDE.
2. Go to **Sketch → Include Library → Manage Libraries**.
3. Search for **SparkFun BMP180**.
4. Install the compatible SparkFun BMP180 library.
5. Ensure the library provides the `SFE_BMP180.h` header.

The Arduino Wire library is also used for I2C communication and is normally included with the Arduino IDE.

## Working Principle

The BMP180 sensor measures temperature and atmospheric pressure. Arduino uses these readings to calculate additional environmental information.

The program performs the following operations:

1. Initializes serial communication at 9600 baud.
2. Initializes the BMP180 sensor.
3. Displays the configured altitude.
4. Starts a temperature measurement.
5. Retrieves temperature in Celsius.
6. Converts temperature into Fahrenheit.
7. Starts a pressure measurement using oversampling setting 3.
8. Retrieves absolute atmospheric pressure.
9. Calculates sea-level-compensated pressure using the configured altitude.
10. Estimates altitude using the pressure measurement and calculated reference pressure.
11. Displays the results in the Serial Monitor.
12. Waits five seconds before repeating the process.

## Arduino Source Code

```cpp
#include <SFE_BMP180.h>
#include <Wire.h>

// Create an SFE_BMP180 object
SFE_BMP180 pressure;

#define ALTITUDE 1655.0 // Altitude in meters

void setup()
{
  Serial.begin(9600);
  Serial.println("REBOOT");

  // Initialize the sensor
  if (pressure.begin())
    Serial.println("BMP180 init success");
  else
  {
    Serial.println("BMP180 init fail\n\n");
    while(1);
  }
}

void loop()
{
  char status;
  double T,P,p0,a;

  // Display configured altitude
  Serial.println();
  Serial.print("provided altitude: ");
  Serial.print(ALTITUDE,0);
  Serial.print(" meters, ");
  Serial.print(ALTITUDE*3.28084,0);
  Serial.println(" feet");

  // Start temperature measurement
  status = pressure.startTemperature();

  if (status != 0)
  {
    delay(status);

    // Retrieve temperature
    status = pressure.getTemperature(T);

    if (status != 0)
    {
      Serial.print("temperature: ");
      Serial.print(T,2);
      Serial.print(" deg C, ");
      Serial.print((9.0/5.0)*T+32.0,2);
      Serial.println(" deg F");

      // Start pressure measurement
      status = pressure.startPressure(3);

      if (status != 0)
      {
        delay(status);

        // Retrieve pressure measurement
        status = pressure.getPressure(P,T);

        if (status != 0)
        {
          Serial.print("absolute pressure: ");
          Serial.print(P,2);
          Serial.print(" mb, ");
          Serial.print(P*0.0295333727,2);
          Serial.println(" inHg");

          // Calculate sea-level pressure
          p0 = pressure.sealevel(P,ALTITUDE);

          Serial.print("relative (sea-level) pressure: ");
          Serial.print(p0,2);
          Serial.print(" mb, ");
          Serial.print(p0*0.0295333727,2);
          Serial.println(" inHg");

          // Calculate altitude
          a = pressure.altitude(P,p0);

          Serial.print("computed altitude: ");
          Serial.print(a,0);
          Serial.print(" meters, ");
          Serial.print(a*3.28084,0);
          Serial.println(" feet");
        }
        else Serial.println("error retrieving pressure measurement\n");
      }
      else Serial.println("error starting pressure measurement\n");
    }
    else Serial.println("error retrieving temperature measurement\n");
  }
  else Serial.println("error starting temperature measurement\n");

  delay(5000);
}
```

## Code Explanation

### 1. Include Required Libraries

```cpp
#include <SFE_BMP180.h>
#include <Wire.h>
```

- `SFE_BMP180.h` provides functions for communicating with the BMP180 sensor.
- `Wire.h` provides I2C communication support.

### 2. Create the Sensor Object

```cpp
SFE_BMP180 pressure;
```

This creates an object named `pressure` that is used to initialize the sensor and read temperature and pressure measurements.

### 3. Define the Altitude

```cpp
#define ALTITUDE 1655.0
```

This defines the altitude in meters used for sea-level pressure compensation.

The original code uses 1655 meters, which is the altitude of SparkFun's headquarters in Boulder, Colorado.

For measurements at another location, replace this value with the approximate altitude above sea level of your installation location.

### 4. Initialize Serial Communication

```cpp
Serial.begin(9600);
Serial.println("REBOOT");
```

This starts serial communication at 9600 baud and displays a startup message.

### 5. Initialize the BMP180 Sensor

```cpp
if (pressure.begin())
  Serial.println("BMP180 init success");
else
{
  Serial.println("BMP180 init fail\n\n");
  while(1);
}
```

The `pressure.begin()` function initializes the BMP180 sensor.

If initialization succeeds, the Arduino displays a success message. Otherwise, it displays an error and stops the program.

### 6. Start Temperature Measurement

```cpp
status = pressure.startTemperature();
```

This requests a temperature measurement from the BMP180 sensor.

The function returns the number of milliseconds the Arduino must wait for the measurement to complete. A return value of zero indicates that the request failed.

### 7. Retrieve Temperature

```cpp
status = pressure.getTemperature(T);
```

This retrieves the measured temperature and stores it in variable `T`.

The program displays the temperature in Celsius and converts it into Fahrenheit:

```cpp
(9.0/5.0)*T+32.0
```

### 8. Start Pressure Measurement

```cpp
status = pressure.startPressure(3);
```

This starts a pressure measurement.

The value `3` selects the highest oversampling setting supported by this library, providing higher-resolution measurements with a longer conversion time.

### 9. Retrieve Atmospheric Pressure

```cpp
status = pressure.getPressure(P,T);
```

This retrieves the pressure measurement and stores it in variable `P`.

The previous temperature measurement `T` is also supplied because it is used by the pressure calculation.

The program displays pressure in:

- Millibars (mb).
- Inches of mercury (inHg).

### 10. Calculate Sea-Level Pressure

```cpp
p0 = pressure.sealevel(P,ALTITUDE);
```

This calculates pressure adjusted to sea level using the measured absolute pressure and the configured altitude.

Sea-level-compensated pressure is commonly used in weather reports to compare atmospheric pressure across locations at different elevations.

### 11. Calculate Altitude

```cpp
a = pressure.altitude(P,p0);
```

This estimates altitude from the pressure measurement and the supplied reference pressure.

The result is displayed in meters and feet.

**Note:** In this original sketch, `p0` is calculated from the current pressure reading and the configured `ALTITUDE`. Consequently, the calculated altitude is tied to that reference calculation and should not be treated as an independent GPS-like altitude measurement.

### 12. Repeat Measurements

```cpp
delay(5000);
```

This pauses the program for five seconds before starting another measurement cycle.

## Implementation Steps

1. Collect the Arduino Uno, BMP180 sensor, breadboard, and jumper wires.
2. Connect the sensor's GND pin to Arduino GND.
3. Connect SDA to Arduino A4.
4. Connect SCL to Arduino A5.
5. Connect the sensor power pin according to the module's voltage requirements.
6. Open Arduino IDE.
7. Install the SparkFun BMP180 library.
8. Copy the Arduino source code into the IDE.
9. Set the `ALTITUDE` value to the approximate altitude of your installation location.
10. Select the correct Arduino board and COM port.
11. Upload the program.
12. Open the Serial Monitor.
13. Set the baud rate to 9600.
14. Observe the temperature, pressure, and altitude-related readings.

## Expected Output

After uploading the program successfully, the Serial Monitor displays information similar to the following:

```text
REBOOT
BMP180 init success

provided altitude: 1655 meters, 5429 feet
temperature: 25.00 deg C, 77.00 deg F
absolute pressure: 833.00 mb, 24.60 inHg
relative (sea-level) pressure: 1013.25 mb, 29.92 inHg
computed altitude: 1655 meters, 5429 feet
```

**Note:** These are illustrative example readings, not guaranteed sensor results. Actual measurements depend on the environment, sensor calibration, and configured altitude.

![Arduino BMP180 Serial Monitor Output](/images/projects/iot/arduino-bmp180-serial-monitor-output.png)

## Troubleshooting

| Problem | Possible Cause | Solution |
|---|---|---|
| BMP180 init fail | Incorrect wiring | Check SDA, SCL, VCC, and GND. |
| Compilation error | Missing library | Install the compatible SparkFun BMP180 library. |
| No sensor response | Incorrect I2C connection | Check SDA and SCL connections. |
| Incorrect temperature | Sensor or wiring problem | Check the sensor connections and module. |
| Incorrect sea-level pressure | Wrong altitude value | Set the correct approximate installation altitude. |
| Serial Monitor displays unreadable text | Incorrect baud rate | Set the baud rate to 9600. |

## Applications

- Weather monitoring systems.
- Environmental monitoring stations.
- Barometric pressure measurement.
- Educational electronics projects.
- Basic altitude estimation experiments.
- IoT-based weather stations.

## Advantages

- Measures both temperature and atmospheric pressure.
- Supports I2C communication.
- Provides pressure readings in multiple units.
- Can calculate sea-level-compensated pressure.
- Can estimate altitude from pressure measurements.
- Suitable for beginner and intermediate Arduino projects.

## Limitations

- Pressure-based altitude estimates are affected by changes in weather conditions.
- Accurate sea-level pressure compensation requires a suitable altitude value.
- The sensor does not provide GPS location.
- The project displays readings through the Serial Monitor and does not provide remote monitoring by itself.
- Wiring and power requirements depend on the particular BMP180 breakout board.

## Future Improvements

- Add an LCD or OLED display.
- Connect an ESP8266 or ESP32 for Wi-Fi monitoring.
- Upload environmental readings to an IoT platform.
- Store pressure and temperature data for later analysis.
- Add a graphical dashboard for real-time monitoring.
- Combine the BMP180 with a GPS module for additional altitude and location information.

## Conclusion

The Arduino BMP180 Temperature, Pressure, and Altitude Monitoring System is a practical project for learning environmental sensor interfacing and I2C communication.

It demonstrates how Arduino can collect temperature and atmospheric pressure measurements, convert units, calculate sea-level-compensated pressure, and estimate altitude.

By completing this project, learners gain useful experience with sensor libraries, pressure measurement, environmental monitoring, and Arduino programming. The project also provides a foundation for developing more advanced IoT-based weather monitoring systems.