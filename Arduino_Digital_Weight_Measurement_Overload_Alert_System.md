# Arduino Digital Weight Measurement and Overload Alert System Using HX711

**Suggested filename:** `Arduino_Digital_Weight_Measurement_Overload_Alert_System.md`

## 1. Introduction

A Digital Weight Measurement System is an electronic project that measures the weight of an object using a load cell and an HX711 amplifier module.

The Arduino reads weight data from the HX711 module and displays the result on a 16×2 I2C LCD.

The system also provides an overload alert. The user can set a maximum weight limit using two input switches. If the measured weight exceeds the selected limit, the buzzer turns ON and the LCD displays an overload warning.

## 2. Objectives

- To measure the weight of objects using a load cell.
- To interface the HX711 module with Arduino.
- To display weight measurements on an LCD.
- To set a maximum weight limit.
- To detect overload conditions.
- To generate an alert using a buzzer.

## 3. Required Components

| Component | Quantity | Purpose |
|---|---:|---|
| Arduino Uno | 1 | Main controller |
| Load Cell | 1 | Measures weight |
| HX711 Module | 1 | Amplifies load-cell signals |
| 16×2 I2C LCD | 1 | Displays weight and alerts |
| Buzzer | 1 | Provides overload warning |
| Push Buttons / Switches | 2 | Set weight reference and limit |
| Jumper Wires | As required | Connections |
| Power Supply | 1 | Powers the system |

## 4. Pin Configuration

### A. Input Switches

| Component | Arduino Pin | Function |
|---|---|---|
| IN1 | A0 | Sets the maximum weight limit |
| IN2 | A1 | Sets the current weight as the zero reference |

### B. Other Components

| Component | Arduino Pin |
|---|---|
| Buzzer | D13 |
| I2C LCD SDA | A4 on Arduino Uno |
| I2C LCD SCL | A5 on Arduino Uno |

### C. HX711 Connections

The supplied code calls HX711 functions but does not show the HX711 pin definitions. Therefore, the exact DT and SCK pin connections cannot be identified from this code alone.

A typical HX711 connection requires:

- VCC
- GND
- DT (Data)
- SCK (Clock)

The DT and SCK pins must match the configuration in the HX711 library or the missing initialization code.

## 5. Libraries Used

```cpp
#include "HX711.h"
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
```

**HX711.h**
- Communicates with the HX711 amplifier.
- Reads weight information from the load cell.

**Wire.h**
- Provides I2C communication.
- Allows Arduino to communicate with the LCD.

**LiquidCrystal_I2C.h**
- Controls the I2C LCD.
- Displays weight measurements and warning messages.

## 6. Understanding the Variables

```cpp
int IN1 = A0;
int IN2 = A1;

int over_val;
int data;
int g_weight;
int Weight;

const int buzzer = 13;
```

| Variable | Purpose |
|---|---|
| `IN1` | Input for setting maximum weight |
| `IN2` | Input for setting the zero reference |
| `over_val` | Stores the maximum permitted weight |
| `data` | Stores the reference weight reading |
| `g_weight` | Calculated weight difference |
| `Weight` | Current HX711 weight reading |
| `buzzer` | Buzzer output pin |

## 7. HX711 Load Cell Working Principle

A load cell is a sensor that measures force or weight.

When an object is placed on the load cell, its internal strain gauges change electrical resistance. This produces a very small electrical signal.

The HX711 module amplifies and converts this signal into digital data that the Arduino can read.

### Working Process

1. An object is placed on the load cell.
2. The load cell produces a small electrical signal.
3. The HX711 amplifies the signal.
4. Arduino reads the digital measurement.
5. The program calculates the weight difference.
6. The LCD displays the weight.

## 8. LCD Initialization

```cpp
LiquidCrystal_I2C lcd(
  0x27, 2, 1, 0, 4, 5, 6, 7, 3, POSITIVE
);
```

This creates an LCD object.

- `0x27`: I2C address of the LCD module.
- The remaining arguments specify the LCD backpack's pin mapping for this particular library.
- `POSITIVE`: Configures the backlight polarity for the library.

**Note:** This constructor is used by certain versions of `LiquidCrystal_I2C`. Other versions use a different constructor, such as `LiquidCrystal_I2C lcd(0x27, 16, 2);`.

## 9. Setup Function Explanation

```cpp
void setup() {
  pinMode(buzzer, OUTPUT);

  pinMode(IN1, INPUT);
  pinMode(IN2, INPUT);

  Init_Hx711();

  Serial.begin(9600);
  Serial.print("Ready!\n");

  Get_Maopi();

  lcd.begin(16, 2);
  lcd.clear();
}
```

### Explanation

**1. Buzzer configuration**

```cpp
pinMode(buzzer, OUTPUT);
```

Sets the buzzer pin as an output.

**2. Switch configuration**

```cpp
pinMode(IN1, INPUT);
pinMode(IN2, INPUT);
```

Configures both switches as input pins.

**3. HX711 initialization**

```cpp
Init_Hx711();
```

Initializes the HX711 module. This function is not defined in the supplied code, so its implementation must be available elsewhere in the project.

**4. Serial communication**

```cpp
Serial.begin(9600);
```

Starts serial communication at 9600 baud.

**5. Initial weight reading**

```cpp
Get_Maopi();
```

Calls a function that appears intended to capture the unloaded load-cell reading or zero reference. Its exact behavior depends on the missing function definition.

**6. LCD initialization**

```cpp
lcd.begin(16, 2);
```

Initializes a 16-column, 2-row LCD.

## 10. Main Loop Explanation

### A. Reading Weight

```cpp
Weight = Get_Weight();
g_weight = Weight - data;
```

- `Get_Weight()` reads the current weight measurement.
- `Weight` stores the reading.
- `data` stores the reference reading.
- `g_weight` stores the difference between the current and reference readings.

The exact unit and accuracy depend on how `Get_Weight()` is implemented and calibrated.

### B. Displaying Weight

```cpp
lcd.setCursor(0, 0);
lcd.print("Weight:");
lcd.print(g_weight);
lcd.print("g    ");
```

Displays the measured weight on the first LCD row.

Example:

```text
Weight:250g
```

The trailing spaces help overwrite some previous display characters.

### C. Setting the Reference Weight

```cpp
if (digitalRead(IN2) == LOW) {
  data = Weight;
}
```

When IN2 is activated, the current HX711 reading is saved in `data`.

The program subtracts this reading from future measurements.

This acts as a reference or zero adjustment.

### D. Setting Maximum Weight

```cpp
if (digitalRead(IN1) == LOW) {
  over_val = g_weight;
}
```

When IN1 is activated, the current calculated weight becomes the maximum allowed weight.

For example, if the current weight is 500 grams, the program stores 500 in `over_val`.

### E. Normal Weight Condition

```cpp
if (g_weight <= over_val) {
  lcd.setCursor(0, 1);
  lcd.print("Max Weight:");
  lcd.print(over_val);
  lcd.print("g    ");

  digitalWrite(buzzer, LOW);
}
```

If the measured weight is less than or equal to the maximum limit:

- The LCD displays the maximum weight.
- The buzzer remains OFF.

### F. Overload Condition

```cpp
else if (g_weight > over_val) {
  Serial.println("overload");

  lcd.setCursor(0, 1);
  lcd.print("...OverLoad!!...");

  digitalWrite(buzzer, HIGH);
}
```

If the measured weight exceeds the maximum limit:

- The Serial Monitor displays `overload`.
- The LCD displays an overload warning.
- The buzzer turns ON.

## 11. Working Principle

1. Arduino starts and initializes the HX711 module.
2. The LCD displays a welcome message.
3. The system obtains an initial load-cell reading.
4. The user activates IN2 to set the reference reading.
5. The user places an object on the load cell.
6. Arduino reads the weight through the HX711.
7. The LCD displays the calculated weight.
8. The user activates IN1 to store the current weight as the maximum limit.
9. Arduino compares the current weight with the stored limit.
10. If the weight is within the limit, the buzzer stays OFF.
11. If the weight exceeds the limit, the buzzer turns ON and the LCD displays an overload message.

## 12. System Flowchart

```text
          START
            |
            v
     Initialize Arduino
            |
            v
       Initialize HX711
            |
            v
       Initialize LCD
            |
            v
     Read Initial Weight
            |
            v
       Read Current Weight
            |
            v
     Calculate Weight Difference
            |
            v
        IN2 Activated?
          /       \
        Yes        No
         |          |
         v          |
   Save Reference   |
         |          |
         +----------+
            |
            v
        IN1 Activated?
          /       \
        Yes        No
         |          |
         v          |
   Save Maximum     |
      Weight        |
         |          |
         +----------+
            |
            v
     Compare Current Weight
       With Maximum Limit
            |
       +----+----+
       |         |
       v         v
   Within      Overload
    Limit       Condition
       |         |
       v         v
  Buzzer OFF  Buzzer ON
       |         |
       v         v
 Display Weight Display Warning
       |         |
       +----+----+
            |
            v
         Repeat
```

## 13. Applications

- Electronic weighing machines.
- Package weight monitoring.
- Industrial weighing systems.
- Overload detection systems.
- Smart storage and inventory systems.
- Laboratory weight measurement.

## 14. Advantages

- Digital weight measurement.
- Real-time LCD display.
- Adjustable maximum weight limit.
- Automatic overload warning.
- Low-cost Arduino-based system.
- Simple operation.

## 15. Limitations and Improvements

### Important issues in the supplied code

**1. Missing HX711 functions**

The supplied code calls:

```cpp
Init_Hx711();
Get_Maopi();
Get_Weight();
```

However, their definitions are not included. The code is incomplete without these functions or the additional source file that defines them.

**2. Missing calibration details**

The program does not show how the load cell is calibrated. A calibration factor is required to convert raw HX711 readings into accurate weight units.

**3. Input pin configuration**

The code uses `INPUT` for IN1 and IN2. These pins need appropriate external pull-up or pull-down resistors to prevent floating inputs. Alternatively, use `INPUT_PULLUP` with switches connected to GND and adjust the logic.

**4. Weight variable type**

The variables use `int`. HX711 readings and calculated weights may exceed the range of a 16-bit integer on an Arduino Uno. Use suitable types such as `long` or `float`, depending on the missing HX711 functions.

**5. Overload threshold**

The maximum limit is stored only when IN1 is activated. A startup default or saved threshold should be added if the system needs to work consistently after restarting.

**6. Buzzer behavior**

The buzzer remains ON while the overload condition exists. A different alert pattern could be added if desired.

**7. Weight stability**

Load-cell measurements can fluctuate. Taking multiple readings and averaging them can improve stability.

### Possible Improvements

- Add accurate calibration using known weights.
- Save the maximum weight limit in EEPROM.
- Add a tare button.
- Add an OLED display.
- Add wireless monitoring using ESP32.
- Add filtering to reduce measurement fluctuations.
- Add a warning LED.

## 16. Troubleshooting

| Problem | Possible Solution |
|---|---|
| LCD does not display | Check I2C address and wiring |
| HX711 gives no reading | Check DT and SCK connections |
| Weight is inaccurate | Calibrate using known weights |
| Weight changes without an object | Check load-cell mounting and wiring |
| Buzzer does not sound | Check buzzer pin and polarity |
| Overload warning appears incorrectly | Check maximum weight setting |
| LCD shows unstable values | Average multiple readings |

## 17. Viva Questions and Answers

**Q1. What is a load cell?**

A load cell is a sensor that converts force or weight into an electrical signal.

**Q2. What is HX711?**

HX711 is a precision amplifier and ADC module commonly used with load cells.

**Q3. Why is HX711 used?**

It amplifies the small electrical signal from the load cell and converts it into digital data.

**Q4. What is the function of the LCD?**

It displays the measured weight and overload warning.

**Q5. What is the purpose of the buzzer?**

It produces an audible warning when the weight exceeds the maximum limit.

**Q6. What does `Get_Weight()` do?**

It reads the current weight measurement from the HX711, according to the function implementation.

**Q7. What is tare or zero adjustment?**

Tare is the process of setting the current unloaded or reference reading as zero.

**Q8. What happens when the weight exceeds the maximum limit?**

The LCD displays an overload warning and the buzzer turns ON.

**Q9. What is I2C?**

I2C is a communication protocol that allows devices to communicate using SDA and SCL lines.

**Q10. Why is calibration important?**

Calibration converts sensor readings into accurate weight measurements.

## 18. Conclusion

The Digital Weight Measurement and Overload Alert System is an Arduino-based project that uses a load cell, HX711 module, I2C LCD, and buzzer. It measures object weight, compares it with a user-defined maximum limit, and provides an alert when the limit is exceeded.

This project helps students understand sensor interfacing, analog signal measurement, I2C communication, LCD interfacing, and conditional control using Arduino.