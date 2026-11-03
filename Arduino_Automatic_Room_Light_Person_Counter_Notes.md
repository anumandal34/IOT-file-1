# Arduino Automatic Room Light Control and Person Counter Using Two Ultrasonic Sensors

## 1. Introduction

The Automatic Room Light Control and Person Counter is an Arduino-based project that counts people entering and leaving a room.

It uses two ultrasonic sensors to detect the direction of movement. When a person enters the room, the counter increases by one. When a person leaves, the counter decreases by one.

The Arduino controls a relay according to the number of people inside the room. If at least one person is present, the light turns ON. If nobody is inside, the light turns OFF.

## 2. Objectives

- To count people entering and leaving a room.
- To detect movement using ultrasonic sensors.
- To control room lights automatically.
- To interface two ultrasonic sensors with Arduino.
- To understand relay-based switching.

## 3. Required Components

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| Ultrasonic Sensor (HC-SR04) | 2 |
| 16×2 LCD Display | 1 |
| Relay Module | 1 |
| Light/Bulb | 1 |
| Breadboard | 1 |
| Jumper Wires | As required |
| USB Cable | 1 |

## 4. Required Library

**LiquidCrystal.h**

This library is used to control the 16×2 LCD display.

```cpp
#include <LiquidCrystal.h>
```

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
| VSS | GND |
| VDD | 5V |
| RW | GND |

### B. Ultrasonic Sensor 1

| Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| TRIG | A1 |
| ECHO | A0 |

### C. Ultrasonic Sensor 2

| Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| TRIG | A3 |
| ECHO | A2 |

### D. Relay Module

| Relay Pin | Arduino Pin |
|---|---|
| VCC | 5V (if supported) |
| GND | GND |
| IN | 8 |

The relay's switching contacts connect to the light circuit according to the relay module's instructions.

## 6. Complete Arduino Code

```cpp
#include <LiquidCrystal.h>

// Initialize LCD
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

// Ultrasonic sensor 1
#define e_s1 A0
#define t_s1 A1

// Ultrasonic sensor 2
#define e_s2 A2
#define t_s2 A3

// Relay pin
int relay = 8;

// Distance variables
long dis_a = 0, dis_b = 0;

// Sensor flags
int flag1 = 0, flag2 = 0;

// Person counter
int person = 0;

// Ultrasonic sensor reading function
void ultra_read(int pin_t, int pin_e, long &ultra_time) {
  long time;

  pinMode(pin_t, OUTPUT);
  pinMode(pin_e, INPUT);

  digitalWrite(pin_t, LOW);
  delayMicroseconds(2);

  digitalWrite(pin_t, HIGH);
  delayMicroseconds(10);

  time = pulseIn(pin_e, HIGH);

  ultra_time = time / 29 / 2;
}

void setup() {

  Serial.begin(9600);

  pinMode(relay, OUTPUT);

  lcd.begin(16, 2);

  lcd.setCursor(0, 0);
  lcd.print("     Welcome    ");

  delay(1000);
  lcd.clear();
}

void loop() {

  // Read both ultrasonic sensors
  ultra_read(t_s1, e_s1, dis_a);
  delay(30);

  ultra_read(t_s2, e_s2, dis_b);
  delay(30);

  // Display sensor readings in Serial Monitor
  Serial.print("da:");
  Serial.println(dis_a);

  Serial.print("db:");
  Serial.println(dis_b);

  // Person entering detection
  if (dis_a < 90 && flag1 == 0) {
    flag1 = 1;

    if (flag2 == 0) {
      person = person + 1;
    }
  }

  // Person leaving detection
  if (dis_b < 90 && flag2 == 0) {
    flag2 = 1;

    if (flag1 == 0) {
      person = person - 1;
    }
  }

  // Reset flags after both sensors become clear
  if (dis_a > 90 && dis_b > 90 &&
      flag1 == 1 && flag2 == 1) {

    flag1 = 0;
    flag2 = 0;

    delay(1000);
  }

  // Display person count
  lcd.setCursor(0, 0);
  lcd.print("Have Person: ");
  lcd.print(person);
  lcd.print("  ");

  // Display light status
  lcd.setCursor(0, 1);
  lcd.print("Light is ");

  if (person > 0) {
    digitalWrite(relay, HIGH);
    lcd.print("On ");
  }
  else {
    digitalWrite(relay, LOW);
    lcd.print("Off");
  }
}
```

## 7. Explanation of the Code

### A. Include LCD Library

```cpp
#include <LiquidCrystal.h>
```

This includes the library required to control the LCD display.

### B. Initialize LCD

```cpp
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
```

This creates an LCD object and assigns the six Arduino pins used for communication.

### C. Define Ultrasonic Sensor Pins

```cpp
#define e_s1 A0
#define t_s1 A1

#define e_s2 A2
#define t_s2 A3
```

- `e_s1` and `e_s2` are the Echo pins.
- `t_s1` and `t_s2` are the Trigger pins.
- Sensor 1 detects one side of the doorway.
- Sensor 2 detects the other side.

### D. Define Relay

```cpp
int relay = 8;
```

The relay module's input is connected to Arduino digital pin 8.

The relay switches the light ON or OFF.

### E. Declare Variables

```cpp
long dis_a = 0, dis_b = 0;

int flag1 = 0, flag2 = 0;

int person = 0;
```

| Variable | Purpose |
|---|---|
| `dis_a` | Distance measured by sensor 1 |
| `dis_b` | Distance measured by sensor 2 |
| `flag1` | Stores detection status of sensor 1 |
| `flag2` | Stores detection status of sensor 2 |
| `person` | Stores the number of people inside |

### F. Ultrasonic Reading Function

```cpp
void ultra_read(int pin_t, int pin_e, long &ultra_time)
```

This function measures the distance using an ultrasonic sensor.

It accepts three arguments:

- `pin_t`: Trigger pin.
- `pin_e`: Echo pin.
- `ultra_time`: Variable used to return the measured distance.

### G. Send Ultrasonic Signal

```cpp
digitalWrite(pin_t, LOW);
delayMicroseconds(2);

digitalWrite(pin_t, HIGH);
delayMicroseconds(10);
```

The Arduino sends a 10-microsecond HIGH pulse to the Trigger pin.

The ultrasonic sensor then sends an ultrasonic sound wave.

### H. Measure Echo Time

```cpp
time = pulseIn(pin_e, HIGH);
```

The `pulseIn()` function measures how long the Echo pin remains HIGH.

This time is used to calculate the distance between the sensor and an object.

### I. Calculate Distance

```cpp
ultra_time = time / 29 / 2;
```

This converts the measured time into an approximate distance in centimeters.

The division by 2 accounts for the sound traveling to the object and returning to the sensor.

### J. Setup Function

```cpp
void setup() {
  Serial.begin(9600);
  pinMode(relay, OUTPUT);
  lcd.begin(16, 2);
}
```

The setup function executes once when Arduino starts.

- Starts serial communication at 9600 baud.
- Configures the relay pin as output.
- Initializes the LCD.

### K. Read Both Sensors

```cpp
ultra_read(t_s1, e_s1, dis_a);
ultra_read(t_s2, e_s2, dis_b);
```

The Arduino reads the distances measured by both ultrasonic sensors.

### L. Detect a Person Entering

```cpp
if (dis_a < 90 && flag1 == 0) {
  flag1 = 1;

  if (flag2 == 0) {
    person = person + 1;
  }
}
```

When sensor 1 detects an object within 90 cm, the program sets `flag1` to 1.

If sensor 2 has not already detected the object, the person count increases by one.

### M. Detect a Person Leaving

```cpp
if (dis_b < 90 && flag2 == 0) {
  flag2 = 1;

  if (flag1 == 0) {
    person = person - 1;
  }
}
```

When sensor 2 detects an object first, the program decreases the person count by one, provided sensor 1 has not already detected it.

### N. Reset Sensor Flags

```cpp
if (dis_a > 90 && dis_b > 90 &&
    flag1 == 1 && flag2 == 1) {

  flag1 = 0;
  flag2 = 0;
}
```

When both sensors become clear after detecting a person, both flags are reset.

This prepares the system to detect the next person.

### O. Display Person Count

```cpp
lcd.setCursor(0, 0);
lcd.print("Have Person: ");
lcd.print(person);
```

This displays the number of people currently counted on the first LCD row.

### P. Automatic Light Control

```cpp
if (person > 0) {
  digitalWrite(relay, HIGH);
  lcd.print("On ");
}
else {
  digitalWrite(relay, LOW);
  lcd.print("Off");
}
```

- If the person count is greater than zero, the relay turns ON.
- If the person count is zero or less, the relay turns OFF.

**Note:** This code assumes the relay is active-HIGH. Some relay modules are active-LOW and need reversed output logic.

## 8. Working Principle

The system uses two ultrasonic sensors installed near a doorway.

1. When a person enters, sensor 1 detects the person first.
2. Sensor 2 detects the same person afterward.
3. The person counter increases by one.
4. When a person leaves, sensor 2 detects the person first.
5. Sensor 1 detects the person afterward.
6. The person counter decreases by one.
7. Arduino checks the total person count.
8. If the count is greater than zero, the light turns ON.
9. If the count is zero, the light turns OFF.

### Understanding the Direction

| Movement | Detection Order | Counter |
|---|---|---|
| Entering | Sensor 1 → Sensor 2 | Increases by 1 |
| Leaving | Sensor 2 → Sensor 1 | Decreases by 1 |

## 9. Block Diagram

```text
        Person Movement
               |
               v
        Two Ultrasonic
            Sensors
               |
               v
           Arduino
               |
       +-------+-------+
       |               |
       v               v
    16×2 LCD         Relay
       |               |
       v               v
 Person Count       Room Light
 and Status         ON / OFF
```

## 10. Flowchart

```text
          START
             |
             v
      Initialize Arduino,
        LCD and Relay
             |
             v
      Read Sensor 1
             |
             v
      Read Sensor 2
             |
             v
   Is Sensor 1 First?
       /          \
     Yes           No
      |             |
      v             v
 Increase      Is Sensor 2
 Person Count   First?
                    |
                 Yes
                    |
                    v
              Decrease
             Person Count
                    |
                    v
          Reset Sensor Flags
                    |
                    v
          Display Person Count
                    |
                    v
          Is Person Count > 0?
              /          \
            Yes           No
             |             |
             v             v
          Light ON      Light OFF
              \           /
               \         /
                  Repeat
```

## 11. Expected Output

### When Nobody Is Inside

```text
Have Person: 0
Light is Off
```

### When One Person Enters

```text
Have Person: 1
Light is On
```

### When Three People Are Inside

```text
Have Person: 3
Light is On
```

### When Everyone Leaves

```text
Have Person: 0
Light is Off
```

These are example outputs. Actual operation depends on sensor placement and movement.

## 12. Applications

1. Automatic room lighting systems.
2. Smart home automation.
3. Office room occupancy monitoring.
4. Classroom person counting.
5. Shopping mall entrance monitoring.
6. Energy-saving lighting systems.
7. Building automation.

## 13. Advantages

- Automatically controls room lighting.
- Helps save electrical energy.
- Counts people entering and leaving.
- Reduces the need for manual switching.
- Demonstrates sensor and relay interfacing.

## 14. Limitations

- Incorrect sensor placement can cause counting errors.
- Two people passing together may not be counted correctly.
- Sensor readings can be affected by obstacles and reflections.
- The system may lose count if power is interrupted.
- The initial person count is zero, so it must start with an empty room for accurate counting.
- The original program does not prevent the person count from becoming negative.

## 15. Important Improvements

For a more reliable project, consider these improvements:

1. Add a condition to prevent the person count from going below zero.
2. Use a timeout with `pulseIn()` so a missing echo does not block the program for too long.
3. Add filtering or confirmation readings to reduce false detections.
4. Use a suitable sensor arrangement so one person passes through the detection zones in sequence.
5. Add a startup procedure to establish the actual initial occupancy.
6. Add a safety timeout if sensor readings become invalid.

## 16. Safety Precautions

- Use a suitable relay module for the light's voltage and current.
- Never connect a mains-powered bulb directly to Arduino pins.
- Keep mains wiring insulated and enclosed.
- Use a regulated supply for the Arduino and sensors.
- Connect sensor grounds correctly.
- Test the project with a low-voltage lamp before using mains electricity.

## 17. Viva Questions and Answers

**Q1. What is the main purpose of this project?**

To count people entering and leaving a room and automatically control the light.

**Q2. Which sensors are used?**

Two ultrasonic sensors.

**Q3. What is the function of an ultrasonic sensor?**

It measures the distance between the sensor and an object using ultrasonic sound waves.

**Q4. What is the purpose of the Trigger pin?**

It sends an ultrasonic pulse from the sensor.

**Q5. What is the purpose of the Echo pin?**

It provides a signal whose duration represents the travel time of the ultrasonic wave.

**Q6. What is the function of `pulseIn()`?**

It measures the duration of a HIGH or LOW pulse on a pin.

**Q7. Why are two ultrasonic sensors used?**

To identify the direction of a person's movement.

**Q8. What is the function of a relay?**

A relay is an electrically controlled switch used to control another circuit.

**Q9. When does the light turn ON?**

When the person count is greater than zero.

**Q10. When does the light turn OFF?**

When the person count becomes zero.

**Q11. What is the purpose of `flag1` and `flag2`?**

They store the detection status of the two sensors and help identify movement direction.

**Q12. Which Arduino pin controls the relay?**

Digital pin 8.

## 18. Conclusion

The Automatic Room Light Control and Person Counter project uses two ultrasonic sensors, Arduino, an LCD, and a relay. It detects the direction of movement, counts people entering and leaving, and automatically controls the room light based on the number of people present. This project demonstrates the practical use of embedded systems, sensor interfacing, and home automation.