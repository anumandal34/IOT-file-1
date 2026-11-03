# Arduino pH Meter Using pH Sensor and 16×2 LCD

## 1. Introduction

An Arduino pH Meter is an electronic project used to measure the pH level of a liquid. It uses a pH sensor to detect the acidity or alkalinity of a solution.

The Arduino reads the analog signal from the pH sensor, calculates an estimated pH value, and displays the result on a 16×2 LCD.

## 2. Objectives

- To measure the pH value of liquids.
- To understand how a pH sensor works.
- To interface a pH sensor with Arduino.
- To display the pH value on an LCD.
- To identify acidic, neutral, and alkaline solutions.

## 3. Required Components

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| pH Sensor Module with Probe | 1 |
| 16×2 LCD Display | 1 |
| Breadboard | 1 |
| Jumper Wires | As required |
| USB Cable | 1 |

## 4. Required Library

**LiquidCrystal.h**

This is the Arduino library used to control a 16×2 LCD.

```cpp
#include <LiquidCrystal.h>
```

It allows Arduino to display text and numerical values on the LCD.

## 5. Pin Connections

### LCD Connections

| LCD Pin | Arduino Pin |
|---|---|
| RS | 2 |
| EN | 3 |
| D4 | 4 |
| D5 | 5 |
| D6 | 6 |
| D7 | 7 |
| VSS | GND |
| VDD | 5V |
| RW | GND |
| VO | Contrast adjustment |
| LED+ | Suitable backlight supply |
| LED- | GND |

### pH Sensor Connections

| pH Sensor Pin | Arduino Pin |
|---|---|
| VCC | As specified by sensor module |
| GND | GND |
| Analog Output (AO) | A0 |

**Note:** Check the sensor module's voltage requirements before connecting it.

## 6. Complete Arduino Code

```cpp
#include <LiquidCrystal.h>

LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

#define SensorPin A0

float calibration_value = 21.34 + 0.7;

unsigned long int avgValue;
int buf[10], temp;

void setup() {
  pinMode(SensorPin, INPUT);

  lcd.begin(16, 2);
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print("Welcome To");

  lcd.setCursor(0, 1);
  lcd.print("       pH Meter");

  delay(2000);
  lcd.clear();
}

void loop() {

  // Read 10 sensor samples
  for (int i = 0; i < 10; i++) {
    buf[i] = analogRead(SensorPin);
    delay(10);
  }

  // Sort the samples
  for (int i = 0; i < 9; i++) {
    for (int j = i + 1; j < 10; j++) {
      if (buf[i] > buf[j]) {
        temp = buf[i];
        buf[i] = buf[j];
        buf[j] = temp;
      }
    }
  }

  // Calculate the average of 6 middle samples
  avgValue = 0;

  for (int i = 2; i < 8; i++) {
    avgValue += buf[i];
  }

  // Convert ADC reading into voltage
  float phValue = (float)avgValue * 5.0 / 1024 / 6;

  // Calculate estimated pH value
  phValue = -5.70 * phValue + calibration_value;

  // Display pH value
  lcd.setCursor(0, 0);
  lcd.print(" pH Value: ");
  lcd.print(phValue);
  lcd.print("  ");

  // Display liquid condition
  lcd.setCursor(1, 1);

  if (phValue < 4)
    lcd.print(" Very Acidic ");

  else if (phValue >= 4 && phValue < 5)
    lcd.print("    Acidic   ");

  else if (phValue >= 5 && phValue < 7)
    lcd.print(" Acidic-ish  ");

  else if (phValue >= 7 && phValue < 8)
    lcd.print("   Neutral   ");

  else if (phValue >= 8 && phValue < 10)
    lcd.print("Alkaline-ish ");

  else if (phValue >= 10 && phValue < 11)
    lcd.print("  Alkaline   ");

  else if (phValue >= 11)
    lcd.print("Very alkaline");

  delay(1000);
}
```

## 7. Explanation of the Code

### A. Include LCD Library

```cpp
#include <LiquidCrystal.h>
```

This statement includes the LiquidCrystal library to operate the LCD display.

### B. Initialize LCD

```cpp
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
```

This creates an LCD object and assigns Arduino pins to the LCD.

### C. Define Sensor Pin

```cpp
#define SensorPin A0
```

The analog output of the pH sensor is connected to Arduino analog pin A0.

### D. Calibration Value

```cpp
float calibration_value = 21.34 + 0.7;
```

This stores the calibration constant used in the pH calculation. It is specific to the example code and must be adjusted through proper calibration of the actual sensor.

### E. Setup Function

```cpp
void setup() {
  pinMode(SensorPin, INPUT);
  lcd.begin(16, 2);
}
```

The setup function runs once when Arduino starts.

- Configures the sensor pin as input.
- Initializes the LCD with 16 columns and 2 rows.
- Displays the welcome message.

### F. Read Sensor Values

```cpp
for (int i = 0; i < 10; i++) {
  buf[i] = analogRead(SensorPin);
  delay(10);
}
```

Arduino reads 10 analog samples from the pH sensor and stores them in an array.

### G. Sort Sensor Values

```cpp
if (buf[i] > buf[j]) {
  temp = buf[i];
  buf[i] = buf[j];
  buf[j] = temp;
}
```

This compares and sorts the readings from smallest to largest.

Sorting helps reduce the effect of unusually high or low readings.

### H. Calculate Average Value

```cpp
for (int i = 2; i < 8; i++)
  avgValue += buf[i];
```

The program adds the six middle readings and calculates their average.

This reduces the effect of extreme readings.

### I. Convert Analog Reading into Voltage

```cpp
float phValue = (float)avgValue * 5.0 / 1024 / 6;
```

The Arduino analog reading is converted into an estimated voltage.

### J. Calculate pH Value

```cpp
phValue = -5.70 * phValue + calibration_value;
```

This formula converts the estimated voltage into a pH value using the example calibration constant and slope.

The formula must be calibrated for the actual pH sensor module.

### K. Display pH Value

```cpp
lcd.setCursor(0, 0);
lcd.print(" pH Value: ");
lcd.print(phValue);
```

This displays the calculated pH value on the first LCD row.

### L. Identify Liquid Condition

The `if-else if` statements classify the calculated value into categories:

- Below 4: Very acidic
- 4 to below 5: Acidic
- 5 to below 7: Acidic-ish
- 7 to below 8: Neutral
- 8 to below 10: Alkaline-ish
- 10 to below 11: Alkaline
- 11 and above: Very alkaline

These are the program's display categories, not a scientifically validated classification system.

## 8. Working Principle

The pH sensor detects the chemical condition of a liquid and produces an analog voltage.

Arduino reads this voltage through analog pin A0. It takes ten readings, sorts them, removes the two lowest and two highest readings, and averages the remaining six.

The program then uses its calibration formula to estimate the pH value. Finally, the LCD displays the estimated pH and its corresponding category.

## 9. Block Diagram

```text
      Liquid Solution
             |
             v
        pH Sensor
             |
        Analog Output
             |
             v
        Arduino Uno
             |
       pH Calculation
             |
             v
        16×2 LCD
             |
             v
      Display pH Value
      and Liquid Type
```

## 10. Flowchart

```text
       START
          |
          v
 Initialize LCD
 and Sensor Pin
          |
          v
   Read 10 Samples
          |
          v
    Sort Readings
          |
          v
 Average 6 Middle
     Readings
          |
          v
 Convert to Voltage
          |
          v
 Calculate pH Value
          |
          v
 Display pH Value
          |
          v
 Identify Liquid Type
          |
          v
 Display Condition
          |
          v
     Delay 1 sec
          |
          v
    Repeat Loop
```

## 11. Expected Output

When the Arduino starts, the LCD displays:

```text
Welcome To
       pH Meter
```

After two seconds, it displays the estimated pH value and category.

Example:

```text
 pH Value: 6.50
 Acidic-ish
```

Another possible example:

```text
 pH Value: 7.20
   Neutral
```

The values above are illustrative; actual readings depend on the liquid, sensor, and calibration.

## 12. Applications

1. Drinking water quality testing.
2. Agriculture and soil-related testing (using a suitable soil pH measurement method).
3. Aquarium water monitoring.
4. Laboratory experiments.
5. Hydroponic farming.
6. Swimming pool water monitoring.
7. Environmental water testing.

## 13. Advantages

- Easy to understand and implement.
- Displays readings in real time.
- Uses an LCD for clear output.
- Reduces some reading noise by averaging samples.
- Useful for educational experiments.

## 14. Limitations

- Sensor calibration is required.
- Sensor readings can change with temperature.
- Incorrect wiring can produce inaccurate readings.
- The example formula may not match every pH sensor.
- The device is not automatically a laboratory-grade pH meter.

## 15. Important Precautions

- Calibrate the probe with suitable standard buffer solutions, commonly pH 4.00, 7.00, and 10.00.
- Follow the sensor manufacturer's instructions for probe storage and cleaning.
- Do not immerse the electronic sensor module in liquid.
- Keep the probe clean and avoid contaminating samples.
- Check the module's operating voltage before connecting it.
- Do not use this unvalidated project for drinking-water safety decisions.

## 16. Viva Questions and Answers

**Q1. What is pH?**

pH is a measure of how acidic or alkaline a solution is.

**Q2. What is the pH range?**

The conventional aqueous pH scale is commonly shown from 0 to 14, although values outside this range are possible in some solutions.

**Q3. What is an acidic solution?**

A solution with pH below 7 is generally acidic.

**Q4. What is a neutral solution?**

A solution with pH 7 is neutral under standard conditions.

**Q5. What is an alkaline solution?**

A solution with pH above 7 is generally alkaline.

**Q6. Which Arduino pin reads the pH sensor?**

Analog pin A0.

**Q7. Which library is used for the LCD?**

The LiquidCrystal library.

**Q8. Why are ten readings taken?**

To reduce the effect of random fluctuations in sensor readings.

**Q9. Why are the readings sorted?**

To remove unusually high and low samples before averaging.

**Q10. Why is calibration necessary?**

Calibration helps the pH meter produce readings that match known standard pH solutions.

## 17. Conclusion

The Arduino pH Meter project measures the pH of a liquid using a pH sensor and displays an estimated value on a 16×2 LCD. It demonstrates analog input, sensor interfacing, data processing, calibration, and LCD output using Arduino.