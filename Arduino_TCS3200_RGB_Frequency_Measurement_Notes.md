# Arduino TCS3200 Color Sensor – RGB Frequency Measurement

## 1. Introduction

The TCS3200 (TCS230) is an RGB color sensor used to detect the color of objects. It contains photodiodes with red, green, blue, and clear filters.

The sensor converts the intensity of detected light into a frequency signal. Arduino reads this signal and measures the LOW pulse duration using the `pulseIn()` function.

In this project, Arduino reads the red, green, and blue filter outputs and displays the measured values on the Serial Monitor.

## 2. Objectives

- To interface the TCS3200 color sensor with Arduino.
- To understand RGB color filters.
- To measure the sensor output pulse duration.
- To read red, green, and blue sensor values.
- To display sensor readings on the Serial Monitor.

## 3. Required Components

1. Arduino Uno
2. TCS3200 / TCS230 color sensor
3. Jumper wires
4. USB cable
5. Computer with Arduino IDE

## 4. About TCS3200 Sensor

The TCS3200 is a color sensor that detects light intensity using different photodiode filters.

It contains four types of photodiodes:

- Red filter photodiodes
- Green filter photodiodes
- Blue filter photodiodes
- Clear photodiodes

The sensor produces a square-wave output whose frequency depends on the detected light intensity.

## 5. Pin Configuration

| Sensor Pin | Function |
|---|---|
| VCC | Power supply |
| GND | Ground |
| S0 | Frequency scaling selection |
| S1 | Frequency scaling selection |
| S2 | Color filter selection |
| S3 | Color filter selection |
| OUT | Frequency signal output |
| OE | Output enable |

## 6. Arduino Connections

| TCS3200 Pin | Arduino Uno Pin |
|---|---|
| S0 | D4 |
| S1 | D5 |
| S2 | D6 |
| S3 | D7 |
| OUT | D8 |
| VCC | 5V, if supported by the module |
| GND | GND |
| OE | GND, if required |

**Note:** Check the voltage requirements and OE connection of your particular sensor module.

## 7. RGB Filter Selection

The S2 and S3 pins select which photodiode filter is active.

| Color Filter | S2 | S3 |
|---|---|---|
| Red | LOW | LOW |
| Green | HIGH | HIGH |
| Blue | LOW | HIGH |
| Clear | HIGH | LOW |

This program reads only the red, green, and blue filters.

## 8. Complete Arduino Code

```cpp
// TCS230 or TCS3200 pins wiring to Arduino
#define S0 4
#define S1 5
#define S2 6
#define S3 7
#define sensorOut 8

// Stores frequency read by the photodiodes
int redFrequency = 0;
int greenFrequency = 0;
int blueFrequency = 0;

void setup()
{
  // Setting the outputs
  pinMode(S0, OUTPUT);
  pinMode(S1, OUTPUT);
  pinMode(S2, OUTPUT);
  pinMode(S3, OUTPUT);

  // Setting the sensorOut as an input
  pinMode(sensorOut, INPUT);

  // Setting frequency scaling to 20%
  digitalWrite(S0, HIGH);
  digitalWrite(S1, LOW);

  // Begins serial communication
  Serial.begin(9600);
}

void loop()
{
  // Read RED filter
  digitalWrite(S2, LOW);
  digitalWrite(S3, LOW);

  redFrequency = pulseIn(sensorOut, LOW);

  Serial.print("R = ");
  Serial.print(redFrequency);
  delay(100);

  // Read GREEN filter
  digitalWrite(S2, HIGH);
  digitalWrite(S3, HIGH);

  greenFrequency = pulseIn(sensorOut, LOW);

  Serial.print(" G = ");
  Serial.print(greenFrequency);
  delay(100);

  // Read BLUE filter
  digitalWrite(S2, LOW);
  digitalWrite(S3, HIGH);

  blueFrequency = pulseIn(sensorOut, LOW);

  Serial.print(" B = ");
  Serial.println(blueFrequency);
  delay(100);
}
```

## 9. Explanation of the Code

### 9.1 Define Sensor Pins

```cpp
#define S0 4
#define S1 5
#define S2 6
#define S3 7
#define sensorOut 8
```

These statements assign Arduino digital pins to the sensor.

- S0 and S1 control frequency scaling.
- S2 and S3 select the color filter.
- sensorOut receives the sensor's output signal.

### 9.2 Declare Variables

```cpp
int redFrequency = 0;
int greenFrequency = 0;
int blueFrequency = 0;
```

These variables store the measured LOW pulse durations for the three color filters.

The readings are expressed in microseconds because `pulseIn()` returns pulse duration in microseconds.

### 9.3 Setup Function

```cpp
void setup()
{
  pinMode(S0, OUTPUT);
  pinMode(S1, OUTPUT);
  pinMode(S2, OUTPUT);
  pinMode(S3, OUTPUT);

  pinMode(sensorOut, INPUT);

  digitalWrite(S0, HIGH);
  digitalWrite(S1, LOW);

  Serial.begin(9600);
}
```

The `setup()` function executes once when Arduino starts.

- `pinMode()`: Configures Arduino pins.
- `OUTPUT`: Makes a pin send signals.
- `INPUT`: Makes a pin receive signals.
- `digitalWrite()`: Sets a digital pin HIGH or LOW.
- `Serial.begin(9600)`: Starts serial communication at 9600 baud.

### 9.4 Frequency Scaling

```cpp
digitalWrite(S0, HIGH);
digitalWrite(S1, LOW);
```

These settings select 20% frequency scaling.

| S0 | S1 | Frequency Scaling |
|---|---|---|
| LOW | LOW | Power down |
| LOW | HIGH | 2% |
| HIGH | LOW | 20% |
| HIGH | HIGH | 100% |

### 9.5 Read Red Frequency

```cpp
digitalWrite(S2, LOW);
digitalWrite(S3, LOW);

redFrequency = pulseIn(sensorOut, LOW);
```

This selects the red photodiode filter.

The `pulseIn()` function measures how long the sensor output remains LOW.

### 9.6 Display Red Reading

```cpp
Serial.print("R = ");
Serial.print(redFrequency);
```

These statements display the red filter measurement in the Serial Monitor.

### 9.7 Read Green Frequency

```cpp
digitalWrite(S2, HIGH);
digitalWrite(S3, HIGH);

greenFrequency = pulseIn(sensorOut, LOW);
```

This selects the green photodiode filter and measures its LOW pulse duration.

### 9.8 Display Green Reading

```cpp
Serial.print(" G = ");
Serial.print(greenFrequency);
```

This displays the green filter measurement.

### 9.9 Read Blue Frequency

```cpp
digitalWrite(S2, LOW);
digitalWrite(S3, HIGH);

blueFrequency = pulseIn(sensorOut, LOW);
```

This selects the blue photodiode filter and measures its LOW pulse duration.

### 9.10 Display Blue Reading

```cpp
Serial.print(" B = ");
Serial.println(blueFrequency);
```

This displays the blue filter measurement and moves to a new line.

### 9.11 Delay Function

```cpp
delay(100);
```

This pauses the program for approximately 100 milliseconds before continuing.

## 10. Working Principle

1. Arduino initializes the sensor pins.
2. The sensor frequency scaling is set to 20%.
3. Arduino selects the red filter.
4. The sensor generates a frequency output.
5. Arduino measures its LOW pulse duration.
6. Arduino repeats the same process for the green filter.
7. Arduino reads the blue filter.
8. All three measurements are displayed on the Serial Monitor.
9. The process repeats continuously.

## 11. Block Diagram

```text
       Colored Object
             |
             v
      TCS3200 Sensor
             |
       RGB Filter Selection
             |
             v
          Arduino
             |
      Pulse Duration Reading
             |
             v
       Serial Monitor
             |
       R / G / B Values
```

## 12. Flowchart

```text
          START
            |
            v
     Initialize Pins
            |
            v
   Set Frequency Scaling
            |
            v
      Select RED Filter
            |
            v
    Read RED Pulse Time
            |
            v
     Select GREEN Filter
            |
            v
   Read GREEN Pulse Time
            |
            v
      Select BLUE Filter
            |
            v
    Read BLUE Pulse Time
            |
            v
     Display RGB Readings
            |
            v
       Repeat Process
```

## 13. Expected Output

Open the Serial Monitor and set the baud rate to 9600.

Example:

```text
R = 125 G = 140 B = 110
R = 120 G = 135 B = 105
R = 80 G = 150 B = 125
R = 95 G = 100 B = 160
```

These values are examples only. Actual readings depend on lighting, object color, sensor distance, and the module.

**Important:** The program measures pulse duration, not frequency directly. A shorter LOW pulse generally corresponds to a higher output frequency.

## 14. Important Functions

| Function | Purpose |
|---|---|
| `pinMode()` | Configures pin direction |
| `digitalWrite()` | Sets a digital pin HIGH or LOW |
| `pulseIn()` | Measures pulse duration in microseconds |
| `Serial.begin()` | Starts serial communication |
| `Serial.print()` | Prints data without a new line |
| `Serial.println()` | Prints data with a new line |
| `delay()` | Pauses the program |

## 15. Difference Between This Program and Color Detection

| Basic RGB Reading Program | RGB Color Detection Program |
|---|---|
| Reads raw pulse durations | Converts readings into RGB values |
| Displays red, green, blue measurements | Identifies the strongest color |
| Does not require mapping values | Requires calibration values |
| Used for sensor testing | Used for basic color identification |

## 16. Troubleshooting

| Problem | Possible Solution |
|---|---|
| No readings | Check sensor power and wiring |
| Readings remain zero | Check OUT pin and OE configuration |
| Readings fluctuate | Keep lighting and object distance stable |
| Serial Monitor is blank | Select 9600 baud |
| Wrong readings | Check S2 and S3 connections |

## 17. Precautions

- Connect the sensor pins correctly.
- Check the sensor's voltage requirements.
- Maintain a consistent distance from the object.
- Avoid changing the surrounding light during testing.
- Ensure the OE pin is enabled.
- Use calibration if accurate color identification is required.

## 18. Applications

1. Color detection systems.
2. Color sorting machines.
3. Robotics projects.
4. Industrial automation.
5. Object identification.
6. Educational electronics projects.

## 19. Viva Questions and Answers

**Q1. What is TCS3200?**

TCS3200 is a color sensor that measures light intensity using different photodiode filters.

**Q2. What does RGB stand for?**

RGB stands for Red, Green, and Blue.

**Q3. How many filter types does TCS3200 have?**

It has four filter types: red, green, blue, and clear.

**Q4. What is the purpose of S0 and S1?**

They select the output frequency scaling.

**Q5. What is the purpose of S2 and S3?**

They select the photodiode filter.

**Q6. What is `pulseIn()`?**

It is an Arduino function that measures the duration of a digital pulse.

**Q7. What unit does `pulseIn()` return?**

It returns the pulse duration in microseconds.

**Q8. What is frequency scaling?**

Frequency scaling changes the output frequency range of the sensor.

**Q9. What is the purpose of Serial Monitor?**

It displays sensor readings and other messages from Arduino.

**Q10. Does this program identify colors automatically?**

No. It only reads and displays the red, green, and blue filter pulse durations.

## 20. Conclusion

In this project, we interfaced the TCS3200 color sensor with Arduino to measure red, green, and blue filter output pulse durations. Arduino selects each color filter and reads the sensor output using the `pulseIn()` function.

This project helps us understand RGB sensing, photodiode filters, frequency measurement, and Arduino sensor interfacing.

---

**Project Name:** Arduino TCS3200 RGB Frequency Measurement

**Microcontroller:** Arduino Uno

**Sensor:** TCS3200 / TCS230

**Programming Language:** C/C++ (Arduino)

**Communication:** Serial Communication

**Output:** Red, Green, and Blue pulse-duration readings.