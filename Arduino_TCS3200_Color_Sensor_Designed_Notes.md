# Arduino TCS3200 Color Sensor Interfacing

## 1. Introduction

The TCS3200 (also called TCS230) is a color sensor that detects colors using light-sensitive photodiodes.

It can detect different colors by measuring the intensity of red, green, and blue light reflected from an object.

In this project, the sensor is connected to Arduino. Arduino reads the output frequency for red, green, and blue filters and displays the detected color on the Serial Monitor.

## 2. Objectives

- To interface a TCS3200 color sensor with Arduino.
- To understand RGB color detection.
- To measure the output frequency of the sensor.
- To identify red, green, and blue colors.
- To display sensor readings using the Serial Monitor.

## 3. Required Components

1. Arduino Uno
2. TCS3200 / TCS230 color sensor
3. Jumper wires
4. USB cable
5. Computer with Arduino IDE

## 4. About TCS3200 Color Sensor

The TCS3200 is an RGB color sensor that converts light intensity into a frequency signal.

It contains an array of photodiodes with different color filters:

- Red photodiodes
- Green photodiodes
- Blue photodiodes
- Clear photodiodes (without color filters)

The sensor uses these photodiodes to measure the intensity of different colors.

### Main Pins

| Pin | Function |
|---|---|
| VCC | Power supply |
| GND | Ground |
| S0 | Output frequency scaling selection |
| S1 | Output frequency scaling selection |
| S2 | Photodiode filter selection |
| S3 | Photodiode filter selection |
| OUT | Frequency output |
| OE | Output enable (usually active LOW) |

## 5. Arduino Connections

Connect the TCS3200 sensor to Arduino Uno as follows:

| TCS3200 Pin | Arduino Pin |
|---|---|
| S0 | D4 |
| S1 | D5 |
| S2 | D6 |
| S3 | D7 |
| OUT | D8 |
| VCC | 5V (if supported by your module) |
| GND | GND |
| OE | GND (if required by the module) |

**Note:** The sensor module's OE pin must be enabled, usually by connecting it to GND. Some modules already handle this internally. Check your module's documentation.

## 6. RGB Color Detection

RGB stands for:

- R = Red
- G = Green
- B = Blue

The sensor measures the intensity of each color separately.

| Color | S2 | S3 |
|---|---|---|
| Red | LOW | LOW |
| Green | HIGH | HIGH |
| Blue | LOW | HIGH |
| Clear | HIGH | LOW |

The program selects one filter at a time and reads the sensor output.

## 7. Complete Arduino Code

**Important:** Your original code contains `XX` placeholders in the `map()` function. These must be replaced with calibration values before the program can compile.

The following version uses example values. You must calibrate them for your sensor and lighting conditions.

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

// Stores the red, green and blue colors
int redColor = 0;
int greenColor = 0;
int blueColor = 0;

void setup()
{
  pinMode(S0, OUTPUT);
  pinMode(S1, OUTPUT);
  pinMode(S2, OUTPUT);
  pinMode(S3, OUTPUT);

  pinMode(sensorOut, INPUT);

  // Set frequency scaling to 20%
  digitalWrite(S0, HIGH);
  digitalWrite(S1, LOW);

  Serial.begin(9600);
}

void loop()
{
  // Read RED
  digitalWrite(S2, LOW);
  digitalWrite(S3, LOW);

  redFrequency = pulseIn(sensorOut, LOW);

  // Example calibration values
  redColor = constrain(
    map(redFrequency, 70, 120, 255, 0),
    0, 255
  );

  Serial.print("R = ");
  Serial.print(redColor);
  delay(100);

  // Read GREEN
  digitalWrite(S2, HIGH);
  digitalWrite(S3, HIGH);

  greenFrequency = pulseIn(sensorOut, LOW);

  greenColor = constrain(
    map(greenFrequency, 100, 199, 255, 0),
    0, 255
  );

  Serial.print(" G = ");
  Serial.print(greenColor);
  delay(100);

  // Read BLUE
  digitalWrite(S2, LOW);
  digitalWrite(S3, HIGH);

  blueFrequency = pulseIn(sensorOut, LOW);

  blueColor = constrain(
    map(blueFrequency, 38, 84, 255, 0),
    0, 255
  );

  Serial.print(" B = ");
  Serial.print(blueColor);
  delay(100);

  // Detect the strongest color
  if (redColor > greenColor && redColor > blueColor)
  {
    Serial.println(" - RED detected!");
  }
  else if (greenColor > redColor && greenColor > blueColor)
  {
    Serial.println(" - GREEN detected!");
  }
  else if (blueColor > redColor && blueColor > greenColor)
  {
    Serial.println(" - BLUE detected!");
  }
  else
  {
    Serial.println(" - Color unclear or mixed!");
  }
}
```

**Important:** The example calibration values (70, 120, 100, 199, 38, and 84) are not universal. They may not work accurately with your sensor. Calibrate the sensor under the lighting and distance conditions in which you will use it.

## 8. Explanation of the Code

### 8.1 Define Sensor Pins

```cpp
#define S0 4
#define S1 5
#define S2 6
#define S3 7
#define sensorOut 8
```

These statements assign Arduino pins to the TCS3200 sensor.

- S0 and S1 control frequency scaling.
- S2 and S3 select the color filter.
- sensorOut receives the sensor's frequency signal.

### 8.2 Declare Variables

```cpp
int redFrequency = 0;
int greenFrequency = 0;
int blueFrequency = 0;
```

These variables store the frequency pulse measurements for red, green, and blue filters.

```cpp
int redColor = 0;
int greenColor = 0;
int blueColor = 0;
```

These variables store the converted RGB intensity values, normally in the range 0–255 after mapping and limiting.

### 8.3 Setup Function

```cpp
void setup()
{
  pinMode(S0, OUTPUT);
  pinMode(S1, OUTPUT);
  pinMode(S2, OUTPUT);
  pinMode(S3, OUTPUT);

  pinMode(sensorOut, INPUT);

  Serial.begin(9600);
}
```

The `setup()` function runs once when Arduino starts.

- `pinMode()`: Configures pins as input or output.
- `Serial.begin(9600)`: Starts serial communication at 9600 baud.

### 8.4 Frequency Scaling

```cpp
digitalWrite(S0, HIGH);
digitalWrite(S1, LOW);
```

This selects 20% output frequency scaling on the TCS3200.

The scaling settings are:

| S0 | S1 | Output Scaling |
|---|---|---|
| LOW | LOW | Power down |
| LOW | HIGH | 2% |
| HIGH | LOW | 20% |
| HIGH | HIGH | 100% |

### 8.5 Read Red Color

```cpp
digitalWrite(S2, LOW);
digitalWrite(S3, LOW);

redFrequency = pulseIn(sensorOut, LOW);
```

- S2 = LOW
- S3 = LOW

This selects the red photodiode filter.

`pulseIn()` measures the duration of a pulse in microseconds. In this program, it measures the LOW pulse duration of the sensor output.

A shorter pulse duration generally indicates a higher output frequency.

### 8.6 Convert Red Reading

```cpp
redColor = constrain(
  map(redFrequency, 70, 120, 255, 0),
  0, 255
);
```

The `map()` function converts the measured pulse duration into a color value.

Syntax:

```cpp
map(value, fromLow, fromHigh, toLow, toHigh);
```

Example:

```cpp
map(70, 70, 120, 255, 0);
```

Result:

```text
255
```

`constrain()` keeps the result between 0 and 255.

### 8.7 Read Green Color

```cpp
digitalWrite(S2, HIGH);
digitalWrite(S3, HIGH);

greenFrequency = pulseIn(sensorOut, LOW);
```

This selects the green photodiode filter and reads the sensor output.

### 8.8 Read Blue Color

```cpp
digitalWrite(S2, LOW);
digitalWrite(S3, HIGH);

blueFrequency = pulseIn(sensorOut, LOW);
```

This selects the blue photodiode filter and reads the sensor output.

### 8.9 Display RGB Values

```cpp
Serial.print("R = ");
Serial.print(redColor);

Serial.print(" G = ");
Serial.print(greenColor);

Serial.print(" B = ");
Serial.print(blueColor);
```

These statements display the RGB readings in the Serial Monitor.

### 8.10 Detect the Color

```cpp
if (redColor > greenColor && redColor > blueColor)
{
  Serial.println(" - RED detected!");
}
```

This condition checks whether the red value is greater than the green and blue values.

If it is, the program identifies red as the strongest detected color.

The same logic is used for green and blue.

The `else` condition handles ties or unclear color readings.

## 9. Working Principle

1. Arduino initializes the sensor pins.
2. The sensor's frequency scaling is set to 20%.
3. Arduino selects the red photodiode filter.
4. The sensor generates a frequency signal.
5. Arduino measures the LOW pulse duration using `pulseIn()`.
6. Arduino repeats the process for green and blue filters.
7. The measured values are converted into approximate RGB intensity values.
8. Arduino compares the three values.
9. The strongest color is displayed on the Serial Monitor.

## 10. Block Diagram

```text
       Colored Object
             |
             v
      TCS3200 Sensor
             |
       RGB Detection
             |
             v
         Arduino
             |
       Color Comparison
             |
             v
       Serial Monitor
             |
       RED / GREEN / BLUE
```

## 11. Flowchart

```text
          START
            |
            v
     Initialize Sensor
            |
            v
    Set Frequency Scaling
            |
            v
      Select RED Filter
            |
            v
     Read RED Frequency
            |
            v
     Select GREEN Filter
            |
            v
    Read GREEN Frequency
            |
            v
      Select BLUE Filter
            |
            v
     Read BLUE Frequency
            |
            v
       Compare RGB Values
            |
            v
      Identify Strongest
           Color
            |
            v
      Display Result
            |
            v
       Repeat Process
```

## 12. Expected Output

Open the Serial Monitor and select 9600 baud.

Example output:

```text
R = 230 G = 45 B = 30 - RED detected!

R = 35 G = 220 B = 40 - GREEN detected!

R = 25 G = 50 B = 240 - BLUE detected!
```

These are illustrative values, not guaranteed sensor readings.

## 13. Important Functions

| Function | Purpose |
|---|---|
| `pinMode()` | Sets a pin as input or output |
| `digitalWrite()` | Sets a digital pin HIGH or LOW |
| `pulseIn()` | Measures the duration of a pulse |
| `map()` | Converts one value range into another |
| `constrain()` | Limits a value within a range |
| `Serial.begin()` | Starts serial communication |
| `Serial.print()` | Displays data without a new line |
| `Serial.println()` | Displays data with a new line |
| `delay()` | Pauses program execution |

## 14. Calibration of the Color Sensor

Calibration is very important for accurate color detection.

The sensor readings depend on:

- Ambient light.
- Distance between the sensor and object.
- Surface reflection.
- Sensor module differences.
- Output frequency scaling.

### Calibration Steps

1. Upload a test program that prints `redFrequency`, `greenFrequency`, and `blueFrequency`.
2. Place a red object in front of the sensor.
3. Record the three readings.
4. Repeat with green and blue objects.
5. Find suitable minimum and maximum values for each color.
6. Replace the example values in the `map()` functions.
7. Test again under the same lighting conditions.

**Important:** The example mapping values in the code should not be assumed to be correct for every sensor.

## 15. Troubleshooting

| Problem | Possible Reason | Solution |
|---|---|---|
| No sensor readings | Incorrect wiring | Check all connections |
| Always detects one color | Incorrect calibration | Calibrate using known colors |
| Values change frequently | Changing ambient light | Use consistent lighting |
| No output in Serial Monitor | Wrong baud rate | Select 9600 baud |
| Sensor does not respond | OE pin disabled | Check OE connection |
| Readings are zero | No pulse received or timeout | Check OUT wiring and sensor power |

## 16. Precautions

- Connect all sensor pins correctly.
- Use a stable power supply.
- Keep the object at a consistent distance.
- Avoid strong changes in surrounding light.
- Calibrate before identifying colors.
- Check your sensor module's operating voltage.
- Do not assume RGB readings are perfectly accurate without calibration.

## 17. Applications

1. Color sorting machines.
2. Industrial automation.
3. Object identification.
4. Robotics projects.
5. Color-based detection systems.
6. Smart packaging systems.
7. Educational electronics projects.

## 18. Viva Questions and Answers

**Q1. What is TCS3200?**

TCS3200 is an RGB color sensor that detects colors by measuring reflected light intensity and producing a frequency output.

**Q2. What is RGB?**

RGB stands for Red, Green, and Blue.

**Q3. How many color filters are available in TCS3200?**

It has red, green, blue, and clear photodiode filters.

**Q4. What is the use of S0 and S1?**

They select the output frequency scaling.

**Q5. What is the use of S2 and S3?**

They select the photodiode color filter.

**Q6. What is the purpose of `pulseIn()`?**

It measures the duration of a HIGH or LOW pulse in microseconds.

**Q7. What is the purpose of `map()`?**

It converts a value from one range into another range.

**Q8. What is the use of `constrain()`?**

It limits a value within a specified minimum and maximum range.

**Q9. What is color calibration?**

Color calibration is the process of measuring known colors and adjusting the sensor's conversion values for more accurate detection.

**Q10. What is the difference between TCS230 and TCS3200?**

They are closely related color sensor devices with similar operating principles. Both use filtered photodiodes and frequency output.

**Q11. What is the output of the TCS3200?**

It produces a square-wave frequency signal related to the detected light intensity.

**Q12. Where is a color sensor used?**

It is used in color sorting, robotics, object detection, and industrial automation.

## 19. Conclusion

In this project, we interfaced the TCS3200 color sensor with Arduino to detect red, green, and blue colors. The sensor converts reflected light intensity into frequency signals, and Arduino measures these signals and compares the RGB values.

This project helps us understand sensor interfacing, frequency measurement, RGB color detection, and basic embedded-system programming.

---

**Project Name:** Arduino TCS3200 Color Sensor Interfacing

**Microcontroller:** Arduino Uno

**Sensor:** TCS3200 / TCS230

**Programming Language:** C/C++ (Arduino)

**Output:** RGB readings and detected color on Serial Monitor.