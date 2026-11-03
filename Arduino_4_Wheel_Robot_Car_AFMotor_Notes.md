# Arduino 4-Wheel Robot Car Using AFMotor Library

**Suggested filename:** `Arduino_4_Wheel_Robot_Car_AFMotor_Notes.md`

## 1. Introduction

This project controls a four-wheel robot car using serial commands. The Arduino receives commands through the Serial Monitor or a connected Bluetooth serial module and controls four DC motors using the Adafruit Motor Shield and the `AFMotor` library.

The robot can:
- Move forward.
- Move backward.
- Turn left.
- Turn right.
- Stop.
- Change motor speed.
- Respond to voice-control commands sent as serial data.

## 2. Components Required

1. Arduino Uno
2. Adafruit Motor Shield V1 (L293D-based)
3. Four DC geared motors
4. Four-wheel robot chassis
5. Bluetooth module (HC-05 or HC-06), if using Bluetooth
6. Battery pack
7. Jumper wires
8. USB cable

## 3. Library Used

### AFMotor Library

The `AFMotor` library controls DC motors connected to the Adafruit Motor Shield V1.

Install it using:

1. Open Arduino IDE.
2. Select **Sketch → Include Library → Manage Libraries**.
3. Search for `Adafruit Motor Shield library`.
4. Install the compatible AFMotor library for Motor Shield V1.

**Important:** This code uses the older Adafruit Motor Shield V1 library. It is not directly compatible with Motor Shield V2.

## 4. Motor Connections

The program uses four motor ports on the shield.

| Motor object | Shield motor port |
|---|---|
| motor1 | M1 |
| motor2 | M2 |
| motor3 | M3 |
| motor4 | M4 |

The shield connects to the Arduino through its shield interface. The motors connect to the shield's M1–M4 terminals.

## 5. Complete Arduino Code

```cpp
#include "AFMotor.h"

const int MOTOR_1 = 1;
const int MOTOR_2 = 2;
const int MOTOR_3 = 3;
const int MOTOR_4 = 4;

AF_DCMotor motor1(MOTOR_1, MOTOR12_64KHZ);
AF_DCMotor motor2(MOTOR_2, MOTOR12_64KHZ);
AF_DCMotor motor3(MOTOR_3, MOTOR12_64KHZ);
AF_DCMotor motor4(MOTOR_4, MOTOR12_64KHZ);

int state;
int Speed = 130;

void setup() {
  motor1.setSpeed(Speed);
  motor2.setSpeed(Speed);
  motor3.setSpeed(Speed);
  motor4.setSpeed(Speed);

  Serial.begin(9600);
  delay(500);
}

void loop() {
  if (Serial.available() > 0) {
    state = Serial.read();

    if (state > 10) {
      Speed = state;
    }
  }

  motor1.setSpeed(Speed);
  motor2.setSpeed(Speed);
  motor3.setSpeed(Speed);
  motor4.setSpeed(Speed);

  if (state == 1) {
    forword();
  }
  else if (state == 2) {
    backword();
  }
  else if (state == 3) {
    turnLeft();
  }
  else if (state == 4) {
    turnRight();
  }
  else if (state == 5) {
    Stop();
  }
  else if (state == 6) {
    turnLeft();
    delay(400);
    state = 5;
  }
  else if (state == 7) {
    turnRight();
    delay(400);
    state = 5;
  }

  delay(80);
}

void forword() {
  motor1.run(FORWARD);
  motor2.run(FORWARD);
  motor3.run(FORWARD);
  motor4.run(FORWARD);
}

void backword() {
  motor1.run(BACKWARD);
  motor2.run(BACKWARD);
  motor3.run(BACKWARD);
  motor4.run(BACKWARD);
}

void turnRight() {
  motor1.run(FORWARD);
  motor2.run(FORWARD);
  motor3.run(BACKWARD);
  motor4.run(BACKWARD);
}

void turnLeft() {
  motor1.run(BACKWARD);
  motor2.run(BACKWARD);
  motor3.run(FORWARD);
  motor4.run(FORWARD);
}

void Stop() {
  motor1.run(RELEASE);
  motor2.run(RELEASE);
  motor3.run(RELEASE);
  motor4.run(RELEASE);
}
```

## 6. Explanation of Important Code

### 6.1 Include the Library

```cpp
#include "AFMotor.h"
```

Includes the AFMotor library so that Arduino can communicate with the motor shield.

### 6.2 Define Motor Numbers

```cpp
const int MOTOR_1 = 1;
const int MOTOR_2 = 2;
const int MOTOR_3 = 3;
const int MOTOR_4 = 4;
```

These numbers represent the four motor ports on the shield.

### 6.3 Create Motor Objects

```cpp
AF_DCMotor motor1(MOTOR_1, MOTOR12_64KHZ);
```

Creates a motor object for motor port M1.

The same process is repeated for M2, M3 and M4.

`MOTOR12_64KHZ` selects the PWM frequency setting for the M1/M2 motor group.

### 6.4 Set Initial Speed

```cpp
int Speed = 130;
```

Sets the initial motor speed to 130.

The motor speed range is:

- 0 = stopped by PWM.
- 130 = medium speed setting.
- 255 = maximum PWM setting.

Actual motor speed also depends on the battery, motor and load.

### 6.5 Setup Function

```cpp
void setup() {
  Serial.begin(9600);
  delay(500);
}
```

- `Serial.begin(9600)` starts serial communication at 9600 baud.
- `delay(500)` waits for half a second.

The motor speeds are also initialized in this function.

### 6.6 Check Serial Data

```cpp
if (Serial.available() > 0) {
  state = Serial.read();
}
```

- `Serial.available()` checks whether data has arrived.
- `Serial.read()` reads one byte from the serial connection.
- The received value is stored in `state`.

### 6.7 Change Motor Speed

```cpp
if (state > 10) {
  Speed = state;
}
```

If the received byte is greater than 10, it is treated as a speed value.

For example, a received byte of 200 sets the motor speed to 200.

**Important:** `Serial.read()` reads one byte, not a complete text number. Sending the text `130` sends three separate characters, not the single byte value 130. Use a controller that sends a raw byte for speed, or change the program to parse text commands.

## 7. Movement Commands

| Command | Action |
|---|---|
| 1 | Forward |
| 2 | Backward |
| 3 | Turn left |
| 4 | Turn right |
| 5 | Stop |
| 6 | Voice left |
| 7 | Voice right |
| 11–255 | Set speed, subject to byte range |

### Forward Movement

```cpp
void forword() {
  motor1.run(FORWARD);
  motor2.run(FORWARD);
  motor3.run(FORWARD);
  motor4.run(FORWARD);
}
```

All four motors rotate in the forward direction.

### Backward Movement

```cpp
void backword() {
  motor1.run(BACKWARD);
  motor2.run(BACKWARD);
  motor3.run(BACKWARD);
  motor4.run(BACKWARD);
}
```

All four motors rotate in the reverse direction.

### Right Turn

```cpp
void turnRight() {
  motor1.run(FORWARD);
  motor2.run(FORWARD);
  motor3.run(BACKWARD);
  motor4.run(BACKWARD);
}
```

The first two motors rotate forward, while the other two rotate backward.

This creates a turning motion. The exact physical direction depends on motor placement and wiring.

### Left Turn

```cpp
void turnLeft() {
  motor1.run(BACKWARD);
  motor2.run(BACKWARD);
  motor3.run(FORWARD);
  motor4.run(FORWARD);
}
```

The first two motors rotate backward, while the other two rotate forward.

### Stop

```cpp
void Stop() {
  motor1.run(RELEASE);
  motor2.run(RELEASE);
  motor3.run(RELEASE);
  motor4.run(RELEASE);
}
```

`RELEASE` stops driving the motors. It allows the motors to coast rather than actively applying braking.

## 8. Working Principle

1. Arduino starts and initializes the four motors.
2. Serial communication starts at 9600 baud.
3. Arduino checks whether a command has arrived.
4. It reads the received byte and stores it in `state`.
5. If the byte represents a speed value, it updates the motor speed.
6. If the byte represents a movement command, it calls the corresponding function.
7. The motor shield controls the four DC motors.
8. The robot performs the requested movement.

## 9. Block Diagram

```text
 Bluetooth Module / Serial Controller
                  |
                  v
             Arduino Uno
                  |
                  v
         Adafruit Motor Shield
                  |
        ---------------------
        |    |    |    |
       M1   M2   M3   M4
        |    |    |    |
        v    v    v    v
       Four DC Motors
        |
        v
       Robot Car
```

## 10. Flowchart

```text
       START
         |
         v
 Initialize Motors
         |
         v
 Start Serial Communication
         |
         v
  Check Serial Data
         |
         v
   Data Available?
      /       \
    Yes        No
     |          |
     v          |
  Read Byte     |
     |          |
     v          |
 Is Speed Value?
   /       \
 Yes        No
  |          |
  v          |
Update       |
Speed        |
  |          |
  ------+-----
       |
       v
 Check Movement Command
       |
       v
 Select Motor Function
       |
       v
 Move / Turn / Stop
       |
       v
    Repeat Loop
```

## 11. Expected Output

When commands are received through the Serial Monitor or Bluetooth serial controller:

| Received command | Expected result |
|---|---|
| 1 | Car moves forward |
| 2 | Car moves backward |
| 3 | Car turns left |
| 4 | Car turns right |
| 5 | Car stops |
| 6 | Car turns left for approximately 400 ms, then sets state to stop |
| 7 | Car turns right for approximately 400 ms, then sets state to stop |
| 130 | Sets speed to 130 |

## 12. Important Functions

| Function | Purpose |
|---|---|
| `setup()` | Initializes the motors and serial communication |
| `loop()` | Repeatedly checks commands |
| `Serial.begin()` | Starts serial communication |
| `Serial.available()` | Checks received data |
| `Serial.read()` | Reads one byte |
| `setSpeed()` | Sets motor PWM speed |
| `run()` | Sets motor direction or release |
| `forword()` | Moves the car forward |
| `backword()` | Moves the car backward |
| `turnLeft()` | Turns the car left |
| `turnRight()` | Turns the car right |
| `Stop()` | Releases all motors |

## 13. Applications

1. Bluetooth-controlled robot cars.
2. Voice-controlled robot projects.
3. Robotics learning and practice.
4. Wireless vehicle control.
5. Educational IoT and embedded-system projects.
6. Small experimental mobile robots.

## 14. Troubleshooting

| Problem | Possible solution |
|---|---|
| Motors do not rotate | Check motor power and shield connections |
| Only some motors work | Check M1–M4 connections |
| Car moves in the wrong direction | Reverse the motor wires or adjust direction commands |
| Bluetooth does not work | Check pairing, baud rate and serial connections |
| Car moves slowly | Check battery voltage and motor load |
| Arduino resets | Use a suitable motor power supply and check current requirements |

## 15. Safety and Important Notes

- Do not connect DC motors directly to Arduino GPIO pins.
- Use a suitable external motor supply connected to the motor shield.
- Make sure the motor supply voltage and current match the motors and shield.
- Connect the power supply correctly and observe polarity.
- Keep the robot wheels off the ground during the first test.
- Check the motor shield version before installing the library.
- The original code does not automatically stop when serial communication is lost. Add a command timeout for safer operation.
- The code uses the `AFMotor` library for the Adafruit Motor Shield V1, not the V2 library.

## 16. Viva Questions and Answers

**Q1. What is AFMotor?**

AFMotor is an Arduino library used to control DC motors through the Adafruit Motor Shield V1.

**Q2. What is the purpose of the motor shield?**

It allows Arduino to control motors by providing motor-driving circuitry.

**Q3. What is PWM?**

PWM stands for Pulse Width Modulation. It controls motor power by changing the duty cycle of a signal.

**Q4. What is the speed range in this program?**

The speed setting ranges from 0 to 255.

**Q5. What is the purpose of `Serial.begin(9600)`?**

It starts serial communication at 9600 baud.

**Q6. What does `Serial.read()` do?**

It reads one byte of received serial data.

**Q7. What does `FORWARD` mean?**

It makes the motor rotate in the forward direction.

**Q8. What does `BACKWARD` mean?**

It makes the motor rotate in the reverse direction.

**Q9. What is the purpose of `RELEASE`?**

It stops actively driving the motor, allowing it to coast.

**Q10. What is the purpose of the `delay(400)` command?**

It pauses program execution for approximately 400 milliseconds.

## 17. Conclusion

This project demonstrates how to control a four-wheel robot car using Arduino, an Adafruit Motor Shield and serial commands. It supports forward, backward, left, right and stop operations, as well as basic speed adjustment. It is a useful beginner project for learning Arduino programming, motor control, serial communication and robotics.