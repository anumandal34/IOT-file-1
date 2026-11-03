# Ultrasonic Vehicle Speed Measurement System

## 1. Introduction

The Ultrasonic Vehicle Speed Measurement System is an Arduino-based project that measures the speed of a moving vehicle using an ultrasonic sensor.

The ultrasonic sensor detects the vehicle at two different distances: 30 cm and 20 cm. Arduino records the time taken by the vehicle to move between these two detection points and calculates its speed in kilometers per hour (km/h).

The measured distance and vehicle speed are displayed on a 16×2 LCD. Green and red LEDs indicate the measurement status, while a buzzer provides sound notifications.

## 2. Objectives

- To measure the speed of a moving vehicle.
- To understand ultrasonic sensor operation.
- To calculate speed using distance and time.
- To display vehicle speed on an LCD.
- To use LEDs and a buzzer for status indication.
- To understand Arduino programming and sensor interfacing.

## 3. Components Required

1. Arduino UNO
2. Ultrasonic Sensor (HC-SR04)
3. 16×2 LCD Display
4. Green LED
5. Red LED
6. Buzzer
7. Push Button
8. 220Ω or 330Ω resistors for LEDs
9. Breadboard
10. Jumper Wires
11. USB Cable

## 4. Pin Connections

### A. LCD Display Connections

| LCD Pin | Arduino Pin |
|---|---|
| RS | 2 |
| E | 3 |
| D4 | 4 |
| D5 | 5 |
| D6 | 6 |
| D7 | 7 |
| VSS | GND |
| VDD | 5V |
| RW | GND |

Connect the LCD contrast pin (VO) to the middle terminal of a potentiometer. Connect the other potentiometer terminals to 5V and GND.

### B. Ultrasonic Sensor Connections

| HC-SR04 Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| TRIG | A1 |
| ECHO | A0 |

### C. Other Component Connections

| Component | Arduino Pin |
|---|---|
| Recheck Button | A5 |
| Green LED | 8 |
| Red LED | 9 |
| Buzzer | 10 |

**Note:** The button uses `INPUT_PULLUP`, so connect one terminal to A5 and the other terminal to GND. Connect each LED through a current-limiting resistor.

## 5. Arduino Code

```cpp
#include <LiquidCrystal.h>

LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

#define echo A0
#define trigger A1

#define Recheck A5

#define G_led 8
#define R_led 9
#define buz 10

// Variables
float Speed = 0, distance = 0, Time = 0;
int flag1 = 0, flag2 = 0;
int time1, time2;

// Buzzer configuration
const unsigned int BEEP_FREQUENCY = 3000;

void setup() {
  pinMode(Recheck, INPUT_PULLUP);

  pinMode(echo, INPUT);
  pinMode(trigger, OUTPUT);

  pinMode(R_led, OUTPUT);
  pinMode(G_led, OUTPUT);
  pinMode(buz, OUTPUT);

  lcd.begin(16, 2);
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print(" Vehicle Speed ");

  lcd.setCursor(0, 1);
  lcd.print(" Measurement ");

  delay(2000);
  lcd.clear();
}

void loop() {
  ultrasonicRead();

  if (distance <= 30 && flag1 == 0) {
    time1 = millis();
    flag1 = 1;
  }

  if (distance <= 20 && flag2 == 0) {
    time2 = millis();
    flag2 = 1;
  }

  if (flag1 == 1 && flag2 == 1) {
    flag1 = 2;
    flag2 = 2;

    if (time1 > time2) {
      Time = time1 - time2;
    }
    else if (time2 > time1) {
      Time = time2 - time1;
    }

    Time = Time / 1000;

    Speed = (10 / Time);
    Speed = Speed * 3600;
    Speed = Speed / 1000;

    digitalWrite(R_led, 0);
    digitalWrite(G_led, 1);

    tone(buz, BEEP_FREQUENCY, 200);
  }

  if (digitalRead(Recheck) == 0) {
    Speed = 0;
    flag1 = 0;
    flag2 = 0;

    digitalWrite(R_led, 1);
    digitalWrite(G_led, 0);

    tone(buz, BEEP_FREQUENCY, 400);
  }

  lcd.setCursor(0, 0);
  lcd.print("Dist. in cm ");
  lcd.print(distance);
  lcd.print("   ");

  lcd.setCursor(0, 1);
  lcd.print("Speed Km/Hr ");
  lcd.print(Speed);

  delay(10);
}

void ultrasonicRead() {
  digitalWrite(trigger, LOW);
  delayMicroseconds(2);

  digitalWrite(trigger, HIGH);
  delayMicroseconds(10);

  long time = pulseIn(echo, HIGH);

  distance = time / 28.5 / 2;
}
```

## 6. Explanation of the Code

### A. Include LCD Library

```cpp
#include <LiquidCrystal.h>
```

This library allows Arduino to communicate with the 16×2 LCD display.

### B. Create LCD Object

```cpp
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
```

Creates an LCD object and assigns the Arduino pins used to communicate with the display.

### C. Define Ultrasonic Sensor Pins

```cpp
#define echo A0
#define trigger A1
```

- `echo`: Receives the reflected ultrasonic signal.
- `trigger`: Sends ultrasonic pulses.

### D. Define Button, LEDs, and Buzzer

```cpp
#define Recheck A5
#define G_led 8
#define R_led 9
#define buz 10
```

- Recheck button is connected to A5.
- Green LED is connected to pin 8.
- Red LED is connected to pin 9.
- Buzzer is connected to pin 10.

### E. Declare Variables

```cpp
float Speed = 0, distance = 0, Time = 0;
int flag1 = 0, flag2 = 0;
int time1, time2;
```

- `Speed`: Stores the calculated vehicle speed.
- `distance`: Stores the measured distance.
- `Time`: Stores the time difference between the two detection points.
- `flag1`: Indicates whether the vehicle has reached the first detection distance.
- `flag2`: Indicates whether the vehicle has reached the second detection distance.
- `time1`: Stores the first detection time.
- `time2`: Stores the second detection time.

### F. Set Buzzer Frequency

```cpp
const unsigned int BEEP_FREQUENCY = 3000;
```

Sets the buzzer sound frequency to 3000 Hz.

### G. Setup Function

```cpp
void setup() {
  pinMode(Recheck, INPUT_PULLUP);

  pinMode(echo, INPUT);
  pinMode(trigger, OUTPUT);

  pinMode(R_led, OUTPUT);
  pinMode(G_led, OUTPUT);
  pinMode(buz, OUTPUT);
}
```

The `setup()` function runs once when Arduino starts.

It configures the button and ultrasonic sensor pins and sets the LEDs and buzzer as output devices.

The LCD is also initialized and displays a welcome message.

### H. Read Ultrasonic Sensor

```cpp
ultrasonicRead();
```

Calls the ultrasonic reading function to measure the distance between the sensor and the vehicle.

### I. First Detection Point

```cpp
if (distance <= 30 && flag1 == 0) {
  time1 = millis();
  flag1 = 1;
}
```

When the vehicle reaches 30 cm or closer:

- Arduino records the current time using `millis()`.
- The first flag changes from 0 to 1.

This indicates that the vehicle has crossed the first detection point.

### J. Second Detection Point

```cpp
if (distance <= 20 && flag2 == 0) {
  time2 = millis();
  flag2 = 1;
}
```

When the vehicle reaches 20 cm or closer:

- Arduino records the second detection time.
- The second flag changes from 0 to 1.

Now Arduino has recorded the time at both detection points.

### K. Calculate Time Difference

```cpp
if (flag1 == 1 && flag2 == 1) {
  flag1 = 2;
  flag2 = 2;
}
```

Checks whether both detection points have been reached.

When both flags are 1, the program changes them to 2 to prevent another measurement during the same detection cycle.

```cpp
if (time1 > time2) {
  Time = time1 - time2;
}
else if (time2 > time1) {
  Time = time2 - time1;
}
```

Calculates the difference between the first and second detection times.

```cpp
Time = Time / 1000;
```

Converts milliseconds into seconds.

### L. Calculate Vehicle Speed

```cpp
Speed = (10 / Time);
Speed = Speed * 3600;
Speed = Speed / 1000;
```

The distance between the two detection points is:

30 cm − 20 cm = 10 cm = 0.1 meter.

The program calculates speed using:

**Speed = Distance / Time**

The conversion gives the final speed in kilometers per hour.

The mathematical formula is:

**Speed (km/h) = 0.36 / Time (seconds)**

For example, if the vehicle takes 0.1 seconds to travel 10 cm:

Speed = 0.36 / 0.1 = 3.6 km/h.

### M. LED and Buzzer Indication

```cpp
digitalWrite(R_led, 0);
digitalWrite(G_led, 1);

tone(buz, BEEP_FREQUENCY, 200);
```

After speed calculation:

- Red LED turns OFF.
- Green LED turns ON.
- Buzzer produces a 3000 Hz sound for 200 milliseconds.

### N. Recheck Button

```cpp
if (digitalRead(Recheck) == 0) {
  Speed = 0;
  flag1 = 0;
  flag2 = 0;
}
```

The button is configured with `INPUT_PULLUP`.

When the button is pressed, its reading becomes LOW (0).

The program resets the speed and both detection flags so another measurement can be performed.

The red LED turns ON, the green LED turns OFF, and the buzzer produces a 400-millisecond notification.

### O. Display Distance and Speed

```cpp
lcd.setCursor(0, 0);
lcd.print("Dist. in cm ");
lcd.print(distance);
```

Displays the measured distance in centimeters on the first LCD row.

```cpp
lcd.setCursor(0, 1);
lcd.print("Speed Km/Hr ");
lcd.print(Speed);
```

Displays the calculated vehicle speed in kilometers per hour on the second LCD row.

### P. Ultrasonic Reading Function

```cpp
void ultrasonicRead() {
  digitalWrite(trigger, LOW);
  delayMicroseconds(2);

  digitalWrite(trigger, HIGH);
  delayMicroseconds(10);

  long time = pulseIn(echo, HIGH);

  distance = time / 28.5 / 2;
}
```

This function measures the distance using the ultrasonic sensor.

1. Sends a LOW signal for 2 microseconds.
2. Sends a HIGH signal for 10 microseconds.
3. `pulseIn()` measures the Echo pulse duration.
4. The program calculates the distance in centimeters.

## 7. Working Principle

1. Arduino initializes the LCD, ultrasonic sensor, LEDs, buzzer, and recheck button.
2. The ultrasonic sensor continuously measures the distance to the vehicle.
3. When the vehicle reaches 30 cm, Arduino records the first time.
4. When the vehicle reaches 20 cm, Arduino records the second time.
5. Arduino calculates the time difference.
6. It uses the known 10 cm distance to calculate vehicle speed.
7. The speed is converted into kilometers per hour.
8. The result is displayed on the LCD.
9. The green LED turns ON and the buzzer produces a short beep.
10. The user presses the recheck button to reset the measurement for another vehicle.

## 8. Expected Output

| Condition | System Response |
|---|---|
| Vehicle reaches 30 cm | First detection time recorded |
| Vehicle reaches 20 cm | Second detection time recorded |
| Both points detected | Speed calculated |
| Speed calculation completed | Green LED ON, red LED OFF, buzzer beeps |
| Recheck button pressed | Speed and detection flags reset |

The LCD displays the measured distance and calculated speed.

## 9. Applications

- Vehicle speed measurement demonstrations
- Robotics projects
- Educational traffic monitoring systems
- Small-scale vehicle experiments
- Arduino-based automation projects
- Basic speed detection experiments

## 10. Advantages

- Simple and low-cost project.
- Measures speed automatically.
- Displays results on an LCD.
- Uses commonly available Arduino components.
- Helps students understand speed calculation.
- Provides visual and sound notifications.

## 11. Limitations

- Ultrasonic readings can be affected by vehicle shape and surface.
- The sensor may not detect all objects reliably.
- The detection distance is limited.
- A single ultrasonic sensor may produce unstable readings for moving vehicles.
- Accurate speed measurement requires reliable detection at both points.
- This is an educational prototype, not a certified road-speed measuring device.

## 12. Important Improvements and Safety

### A. Use unsigned long for Timing

The original code declares:

```cpp
int time1, time2;
```

However, `millis()` returns an `unsigned long`. On an Arduino UNO, an `int` is only 16 bits, so it can overflow after approximately 32.8 seconds.

Use:

```cpp
unsigned long time1, time2;
```

For reliable timing, calculate the difference using unsigned subtraction:

```cpp
Time = (time2 - time1) / 1000.0;
```

This assumes the first detection happens before the second detection.

### B. Prevent Division by Zero

Before calculating speed, check that the time difference is greater than zero:

```cpp
if (Time > 0) {
  Speed = 0.36 / Time;
}
```

### C. Add an Ultrasonic Timeout

The original `pulseIn()` may wait for a relatively long time if no echo is received.

A timeout can be added:

```cpp
long time = pulseIn(echo, HIGH, 30000);
```

Check for a zero result before calculating distance.

### D. Improve Detection Reliability

The code uses one sensor to detect two distance thresholds. If the vehicle moves quickly, it may pass both thresholds between measurements. The recorded times may then be inaccurate.

For a more reliable system, use two separate sensors placed a known distance apart.

### E. Add Button Debouncing

The recheck button may register multiple presses because of electrical contact bouncing. A short debounce delay or proper button-state handling can improve reliability.

## 13. Conclusion

The Ultrasonic Vehicle Speed Measurement System is an Arduino-based project that measures the speed of a moving vehicle using ultrasonic distance detection and time calculation.

It records the time when a vehicle reaches two different distances, calculates the time difference, and determines the speed in kilometers per hour. The LCD displays the result, while LEDs and a buzzer provide status notifications.

This project helps beginners understand ultrasonic sensors, time measurement, mathematical calculations, LCD interfacing, and Arduino-based automation.