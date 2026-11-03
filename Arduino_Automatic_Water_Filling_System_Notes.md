# Arduino Automatic Water Filling System Using Ultrasonic Sensor, IR Sensors, EEPROM and LCD

## 1. Introduction

The Automatic Water Filling System is an Arduino-based project designed to automate the process of filling water into a container or tank.

The system uses an ultrasonic sensor to measure the water level and IR sensors to detect the presence and position of a bottle or container.

Arduino controls the motor and water pump according to the sensor signals. A 16×4 LCD displays the tank level and filling time.

The EEPROM memory stores the configured filling time, allowing the system to remember the setting after power is disconnected.

## 2. Objectives

- To automate the water filling process.
- To measure the water level using an ultrasonic sensor.
- To control a water pump using Arduino.
- To detect containers using IR sensors.
- To set filling time using input buttons.
- To save settings in EEPROM memory.
- To display system information on an LCD.

## 3. Required Components

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| Ultrasonic Sensor (HC-SR04) | 1 |
| IR Sensors | 3 |
| 16×4 LCD Display | 1 |
| Water Pump | 1 |
| DC Motor | 1 |
| Motor Driver / Suitable Motor Circuit | 1 |
| Relay Module or Pump Driver | 1 |
| Buzzer | 1 |
| Push Buttons / Remote Inputs | 4 |
| Water Tank | 1 |
| Jumper Wires | As required |
| Power Supply | As required |

## 4. Required Libraries

### A. LiquidCrystal Library

```cpp
#include <LiquidCrystal.h>
```

This library controls the 16×4 LCD display.

### B. EEPROM Library

```cpp
#include <EEPROM.h>
```

This library stores and retrieves data from the Arduino's EEPROM memory.

## 5. Pin Connections

### A. LCD Connections

| LCD Pin | Arduino Pin |
|---|---|
| RS | 2 |
| EN | 3 |
| D4 | 4 |
| D5 | 5 |
| D6 | 6 |
| D7 | 7 |

The LCD is initialized as a 16-column, 4-row display.

### B. Ultrasonic Sensor Connections

| Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| TRIG | A5 |
| ECHO | A4 |

### C. Input / Remote Control Connections

| Input | Arduino Pin |
|---|---|
| Input 1 | A0 |
| Input 2 | A1 |
| Input 3 | A2 |
| Input 4 | A3 |

These four inputs are used for menu selection, value adjustment, and system control.

### D. IR Sensor Connections

| IR Sensor | Arduino Pin |
|---|---|
| IR Start | 10 |
| IR Fill | 11 |
| IR Stop | 12 |

### E. Output Connections

| Component | Arduino Pin |
|---|---|
| Buzzer | 13 |
| Water Pump Control | 8 |
| Motor PWM Control | 9 |

**Important:** The pump and motor must be connected through suitable driver circuits. Do not connect them directly to Arduino output pins.

## 6. Complete Arduino Code

```cpp
#include <LiquidCrystal.h>
#include <EEPROM.h>

LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

#define echopin A4
#define trigpin A5

int cont = 0;

int in1 = A0;
int in2 = A1;
int in3 = A2;
int in4 = A3;

int remotePins[] = {A0, A1, A2, A3};

int set = 0;
int stop = 0, stop1;
int mode = 0;

int buz = 13;

int timer = 0;

int pump = 8;
int motor = 9;

int ir_start = 10;
int ir_fill = 11;
int ir_stop = 12;

int dist;
long duration;

int val1 = 0, val2 = 0, val3 = 0, val4 = 1;

long result = 0;

void setup() {

  for (int i = 0; i < 5; i++) {
    pinMode(remotePins[i], INPUT);
    digitalWrite(remotePins[i], HIGH);
  }

  pinMode(ir_fill, INPUT);
  pinMode(ir_start, INPUT);
  pinMode(ir_stop, INPUT);

  pinMode(trigpin, OUTPUT);
  pinMode(echopin, INPUT);

  pinMode(motor, OUTPUT);
  pinMode(pump, OUTPUT);

  pinMode(buz, OUTPUT);

  lcd.begin(16, 4);

  lcd.setCursor(0, 0);
  lcd.print("   WELCOME  To  ");

  lcd.setCursor(0, 1);
  lcd.print("Automatic  Water");

  lcd.setCursor(0, 2);
  lcd.print("  Filling");

  lcd.setCursor(0, 3);
  lcd.print("          System");

  delay(2000);

  Serial.begin(9600);

  Read();

  for (int i = 0; i < 5; i++) {
    dist = data1();
    data2();
    delay(100);
  }

  lcd.clear();
}

void loop() {

  if (stop == 0) {

    if (digitalRead(in1) == 0) {
      lcd.clear();

      mode = mode + 1;

      if (mode > 4) {
        mode = 0;

        Write();
        Read();

        lcd.setCursor(0, 0);
        lcd.print("  Ok Saved Data ");

        delay(1000);
      }

      delay(300);
    }

    if (digitalRead(in2) == 0) {

      if (mode == 1) {
        val1 = val1 + 1;
      }

      if (mode == 2) {
        val2 = val2 + 1;
      }

      if (mode == 3) {
        val3 = val3 + 1;
      }

      if (mode == 4) {
        val4 = val4 + 1;
      }

      if (val1 > 9) {
        val1 = 0;
      }

      if (val2 > 9) {
        val2 = 0;
      }

      if (val3 > 9) {
        val3 = 0;
      }

      if (val4 > 9) {
        val4 = 0;
      }

      delay(300);
    }
  }

  if (mode == 0) {

    if (digitalRead(in3) == 0) {
      stop = 0;
    }

    if (digitalRead(in4) == 0) {
      stop = 1;
    }

    lcd.setCursor(0, 0);
    lcd.print("   WELCOME  To  ");

    lcd.setCursor(0, 1);
    lcd.print("     A W F S    ");

    lcd.setCursor(0, 2);
    lcd.print("Tank Level=");
    lcd.print(dist);
    lcd.print("%   ");

    lcd.setCursor(0, 3);
    lcd.print("Fill Time =");
    lcd.print(result);
    lcd.print("   ");

  } else {

    lcd.setCursor(0, 0);
    lcd.print("Set Time= ");

    lcd.print(val4);
    lcd.print(val3);
    lcd.print(val2);
    lcd.print(val1);

    if (mode == 4) {
      lcd.setCursor(10, 1);
      lcd.print("-");
    }

    if (mode == 3) {
      lcd.setCursor(11, 1);
      lcd.print("-");
    }

    if (mode == 2) {
      lcd.setCursor(12, 1);
      lcd.print("-");
    }

    if (mode == 1) {
      lcd.setCursor(13, 1);
      lcd.print("-");
    }
  }

  if (stop == 1) {

    if (digitalRead(ir_stop) == 1) {

      analogWrite(motor, 200);

      if (digitalRead(ir_fill) == 0) {

        if (stop1 == 0) {
          stop1 = 1;

          analogWrite(motor, 0);

          delay(200);

          digitalWrite(pump, HIGH);

          delay(result);

          digitalWrite(pump, LOW);

          for (int i = 0; i < 10; i++) {
            dist = data1();
            data2();
            delay(200);
          }

          analogWrite(motor, 200);
        }
      }

      if (digitalRead(ir_start) == 0) {
        stop1 = 0;
      }

    } else {

      analogWrite(motor, 0);
      delay(300);
    }

  } else {

    analogWrite(motor, 0);
  }
}

void Read() {

  val1 = EEPROM.read(11);
  val2 = EEPROM.read(12);
  val3 = EEPROM.read(13);
  val4 = EEPROM.read(14);

  result = val4 * 1000 + val3 * 100 + val2 * 10 + val1;

  Serial.println(result);
}

void Write() {

  EEPROM.write(11, val1);
  EEPROM.write(12, val2);
  EEPROM.write(13, val3);
  EEPROM.write(14, val4);
}

void data2() {

  dist = map(dist, 2, 30, 100, 0);

  if (dist > 100) {
    dist = 100;
  }

  if (dist < 0) {
    dist = 0;
  }
}

long data1() {

  digitalWrite(trigpin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigpin, HIGH);
  delayMicroseconds(10);

  duration = pulseIn(echopin, HIGH);

  return duration / 29 / 2;
}
```

## 7. Explanation of the Code

### A. Include Libraries

```cpp
#include <LiquidCrystal.h>
#include <EEPROM.h>
```

- `LiquidCrystal.h` controls the LCD.
- `EEPROM.h` stores settings permanently in EEPROM memory.

### B. Initialize LCD

```cpp
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
```

This creates an LCD object and connects it to Arduino pins 2, 3, 4, 5, 6, and 7.

### C. Define Ultrasonic Sensor Pins

```cpp
#define echopin A4
#define trigpin A5
```

- Echo pin receives the reflected ultrasonic signal.
- Trigger pin sends the ultrasonic signal.

The sensor measures the distance between itself and the water surface.

### D. Define Input Pins

```cpp
int in1 = A0;
int in2 = A1;
int in3 = A2;
int in4 = A3;
```

These pins receive signals from four input buttons or a remote-control interface.

Their functions are:

- `in1`: Select the setting mode.
- `in2`: Increase the selected digit.
- `in3`: Set the system to its inactive state.
- `in4`: Activate the system.

### E. Define IR Sensor Pins

```cpp
int ir_start = 10;
int ir_fill = 11;
int ir_stop = 12;
```

These three IR sensors provide signals for the filling and conveyor control process.

The exact physical meaning of each sensor depends on its placement in the machine.

### F. Define Output Pins

```cpp
int pump = 8;
int motor = 9;
int buz = 13;
```

- Pump pin controls the water pump.
- Motor pin controls motor speed through PWM.
- Buzzer pin is configured as an output.

Note: The current code does not actually switch the buzzer ON or OFF.

### G. Define Filling Time Variables

```cpp
int val1 = 0, val2 = 0, val3 = 0, val4 = 1;

long result = 0;
```

These four variables represent the four digits of the filling-time setting.

For example:

| Variable | Digit |
|---|---:|
| val4 | 1 |
| val3 | 2 |
| val2 | 3 |
| val1 | 4 |

The resulting setting is 1234 milliseconds.

### H. Setup Function

```cpp
void setup() {
  pinMode(motor, OUTPUT);
  pinMode(pump, OUTPUT);
  pinMode(buz, OUTPUT);

  lcd.begin(16, 4);
  Serial.begin(9600);
}
```

The setup function runs once when Arduino starts.

It configures the input/output pins, initializes the LCD, and starts serial communication.

The original program also displays a welcome message and reads the saved filling-time settings.

### I. Read EEPROM Data

```cpp
void Read() {
  val1 = EEPROM.read(11);
  val2 = EEPROM.read(12);
  val3 = EEPROM.read(13);
  val4 = EEPROM.read(14);
}
```

The `EEPROM.read()` function retrieves previously stored values from EEPROM memory.

The addresses used are 11, 12, 13, and 14.

### J. Write EEPROM Data

```cpp
void Write() {
  EEPROM.write(11, val1);
  EEPROM.write(12, val2);
  EEPROM.write(13, val3);
  EEPROM.write(14, val4);
}
```

The `EEPROM.write()` function saves the four digits in EEPROM memory.

This allows the system to remember the configured filling time after power is disconnected.

### K. Calculate Filling Time

```cpp
result = val4 * 1000 +
         val3 * 100 +
         val2 * 10 +
         val1;
```

This combines the four digits into one number.

Example:

- val4 = 1
- val3 = 2
- val2 = 3
- val1 = 4

Result = 1234 milliseconds.

### L. Read Ultrasonic Sensor

```cpp
long data1() {
  digitalWrite(trigpin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigpin, HIGH);
  delayMicroseconds(10);

  duration = pulseIn(echopin, HIGH);

  return duration / 29 / 2;
}
```

This function measures the distance between the ultrasonic sensor and the water surface.

The `pulseIn()` function measures the Echo pulse duration.

The result is converted into centimeters.

### M. Calculate Tank Level

```cpp
void data2() {
  dist = map(dist, 2, 30, 100, 0);

  if (dist > 100) {
    dist = 100;
  }

  if (dist < 0) {
    dist = 0;
  }
}
```

The `map()` function converts the measured distance into an estimated percentage.

In this example:

- Distance of 2 cm corresponds to 100%.
- Distance of 30 cm corresponds to 0%.

The values must be adjusted according to the actual tank dimensions and sensor position.

### N. Select Filling-Time Mode

```cpp
if (digitalRead(in1) == 0) {
  mode = mode + 1;
}
```

When input 1 is activated, the program moves to the next setting mode.

The modes are:

| Mode | Selected Digit |
|---|---|
| 0 | Normal operation |
| 1 | First digit |
| 2 | Second digit |
| 3 | Third digit |
| 4 | Fourth digit |

After mode 4, the program returns to mode 0 and saves the settings.

### O. Increase Filling-Time Digit

```cpp
if (mode == 1) {
  val1 = val1 + 1;
}
```

When input 2 is activated, the currently selected digit increases by one.

If a digit becomes greater than 9, it returns to zero.

### P. Automatic Filling Operation

```cpp
digitalWrite(pump, HIGH);
delay(result);
digitalWrite(pump, LOW);
```

This is the main water-filling operation.

1. The pump turns ON.
2. It remains ON for the configured time.
3. The pump turns OFF.
4. The program updates the tank-level reading.

### Q. Motor Speed Control

```cpp
analogWrite(motor, 200);
```

This sends a PWM value of 200 to the motor-control pin.

On an Arduino Uno, PWM values range from 0 to 255.

- 0 means no PWM output.
- 200 means approximately 78% duty cycle.
- 255 means 100% duty cycle.

The motor driver must support PWM control.

## 8. Working Principle

The system works through a combination of distance measurement, object detection, and timed water filling.

1. Arduino starts and displays a welcome message.
2. It reads the saved filling-time settings from EEPROM.
3. The ultrasonic sensor measures the water level.
4. The user can change the filling time using the input buttons.
5. The selected settings are saved in EEPROM.
6. When the system is activated, the motor starts.
7. The IR sensors detect the container's position.
8. When the filling position is detected, the motor stops temporarily.
9. The water pump runs for the configured filling time.
10. The pump switches OFF.
11. The tank-level reading is updated.
12. The motor starts again for the next filling operation.

## 9. Block Diagram

```text
          Input Buttons
                |
                v
          Arduino Uno
                |
       +--------+---------+
       |        |         |
       v        v         v
   Ultrasonic  IR       EEPROM
    Sensor    Sensors   Memory
       |        |         |
       +--------+---------+
                |
                v
          Control Logic
                |
        +-------+-------+
        |       |       |
        v       v       v
       LCD    Motor    Pump
     Display  Driver   Driver
```

## 10. Flowchart

```text
            START
               |
               v
       Initialize Arduino
          and LCD
               |
               v
        Read EEPROM Data
               |
               v
       Measure Water Level
               |
               v
        Select Filling Time
               |
               v
       Save Settings in EEPROM
               |
               v
       Activate the System
               |
               v
          Start Motor
               |
               v
       Detect Container
          Using IR
               |
               v
       Is Filling Position
           Detected?
          /       \
        No         Yes
        |           |
        v           v
    Continue     Stop Motor
    Checking         |
                     v
                 Pump ON
                     |
                     v
              Wait for Set Time
                     |
                     v
                  Pump OFF
                     |
                     v
             Update Water Level
                     |
                     v
                Start Motor
                     |
                     v
                  Repeat
```

## 11. Expected LCD Output

### Welcome Screen

```text
   WELCOME  To
Automatic  Water
  Filling
          System
```

### Normal Operating Screen

```text
   WELCOME  To
     A W F S
Tank Level=75%
Fill Time =1234
```

### Filling-Time Setting Screen

```text
Set Time= 1234
          -
```

The dash indicates the digit currently selected for adjustment.

## 12. Applications

1. Automatic water bottle filling machines.
2. Small water packaging systems.
3. Automatic liquid dispensing systems.
4. Water tank monitoring.
5. Industrial filling processes.
6. Smart water management systems.
7. Small-scale manufacturing automation.

## 13. Advantages

- Reduces manual work.
- Provides automatic filling control.
- Allows adjustable filling time.
- Saves settings using EEPROM.
- Displays tank level on an LCD.
- Uses IR sensors to detect container position.

## 14. Limitations

- Filling is controlled by time, not by direct measurement of the liquid inside each container.
- Incorrect filling-time calibration may cause underfilling or overfilling.
- Ultrasonic readings can be affected by water movement and reflections.
- IR sensors can be affected by lighting, dirt, and object position.
- Power interruption may stop the filling operation.
- The original program has some pin and logic problems that must be corrected before hardware use.

## 15. Important Code Errors and Improvements

### A. Incorrect Array Size

Original code:

```cpp
int remotePins[] = {A0, A1, A2, A3};

for (int i = 0; i < 5; i++) {
  pinMode(remotePins[i], INPUT);
}
```

There are only four elements in the array, but the loop tries to access five elements.

Correct code:

```cpp
for (int i = 0; i < 4; i++) {
  pinMode(remotePins[i], INPUT_PULLUP);
}
```

### B. Input Pins and Pull-Up Resistors

The original program uses:

```cpp
pinMode(remotePins[i], INPUT);
digitalWrite(remotePins[i], HIGH);
```

On an Arduino Uno, this enables the internal pull-up resistor when the pin is configured as an input. However, it is clearer to use:

```cpp
pinMode(remotePins[i], INPUT_PULLUP);
```

With `INPUT_PULLUP`, a connected button should connect the input pin to GND when pressed.

### C. Motor Pin 9

The program uses:

```cpp
int motor = 9;
analogWrite(motor, 200);
```

This provides a PWM signal. A motor driver or suitable switching circuit is required between Arduino and the motor.

### D. EEPROM Initial Values

The first time the program runs, EEPROM addresses 11–14 may not contain valid digits.

A better program should check whether the stored values are valid and initialize them if necessary.

### E. Filling Time Unit

The `delay()` function accepts milliseconds.

For example:

```cpp
delay(1234);
```

means the pump remains ON for approximately 1.234 seconds.

### F. Buzzer Is Not Used

Although the program defines:

```cpp
int buz = 13;
```

it does not contain `digitalWrite(buz, HIGH)` or `digitalWrite(buz, LOW)`. Therefore, the buzzer does not currently provide an alert.

### G. Important Tank-Level Update Problem

The original code uses:

```cpp
dist = data1();
data2();
```

This is correct because `data1()` returns a distance in centimeters, and `data2()` converts that distance into a percentage.

However, the program only updates the level at startup and after a filling operation. It does not continuously update the display during every loop.

### H. Pump Safety

A timed filling system should include an independent maximum-fill safety mechanism, such as a float switch or a suitable level sensor. A software timer alone cannot guarantee that a container will not overflow.

## 16. Viva Questions and Answers

**Q1. What is the main purpose of this project?**

To automatically fill water containers using Arduino and sensor-based control.

**Q2. Which sensor measures the water level?**

An ultrasonic sensor.

**Q3. What is the function of an IR sensor?**

It detects the presence or position of an object.

**Q4. What is EEPROM?**

EEPROM is non-volatile memory that stores data even when the power is OFF.

**Q5. Which library is used for EEPROM?**

The EEPROM library.

**Q6. Which library is used for the LCD?**

The LiquidCrystal library.

**Q7. What is the function of the relay or driver circuit?**

It controls electrical loads such as a water pump or motor.

**Q8. What is PWM?**

PWM stands for Pulse Width Modulation. It controls the average power delivered to a load.

**Q9. What is the function of `pulseIn()`?**

It measures the duration of a HIGH or LOW pulse on a digital pin.

**Q10. What is the function of `map()`?**

It converts a number from one range into another range.

**Q11. Why is EEPROM used?**

To save filling-time settings so they remain available after power is disconnected.

**Q12. What does `delay(result)` do?**

It pauses the program for the number of milliseconds stored in `result`.

## 17. Conclusion

The Automatic Water Filling System is an Arduino-based automation project that uses an ultrasonic sensor, IR sensors, EEPROM, LCD, motor, and water pump. It measures the tank level, detects containers, allows the user to set filling time, and controls the filling operation automatically.

This project demonstrates sensor interfacing, memory storage, LCD communication, PWM motor control, and automation.