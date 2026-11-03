# Arduino Bluetooth-Controlled Robot Car Using Dabble GamePad

**Suggested filename:** `Arduino_Bluetooth_Controlled_Robot_Car_Dabble_GamePad_Notes.md`

## 1. Introduction

This project controls a two-wheel robot car using a smartphone through the Dabble mobile application. The Dabble GamePad module provides virtual buttons to move the robot forward, backward, left, and right.

Arduino receives the button commands through Bluetooth and controls the two DC motors using a motor driver.

## 2. Objectives

- Control a robot car using a smartphone.
- Understand Bluetooth communication with Arduino.
- Learn how to control DC motors.
- Understand motor direction and speed control.
- Use the Dabble GamePad module.

## 3. Required Components

1. Arduino Uno
2. Bluetooth module (HC-05 or compatible serial Bluetooth module)
3. Two DC geared motors
4. Motor driver (L298N or compatible H-bridge)
5. Robot car chassis
6. Wheels
7. Battery
8. Connecting wires
9. Smartphone with Dabble application

## 4. Software and Libraries

**Arduino IDE:** Used to write and upload the program.

**Dabble Library:** Allows the smartphone GamePad to communicate with Arduino.

Install the Dabble library through the Arduino IDE Library Manager or the library's official installation instructions.

## 5. Pin Configuration

| Component | Arduino pin |
|---|---|
| Right motor enable | D5 |
| Right motor input 1 | D7 |
| Right motor input 2 | D8 |
| Left motor enable | D6 |
| Left motor input 1 | D9 |
| Left motor input 2 | D10 |
| Bluetooth TX | D2 (Arduino RX) |
| Bluetooth RX | D3 (Arduino TX) |

**Important:** The Bluetooth connections depend on the module and its voltage requirements. Use a voltage divider for the HC-05 RX input when connecting it to a 5V Arduino TX pin.

## 6. Complete Arduino Code

```cpp
#define CUSTOM_SETTINGS
#define INCLUDE_GAMEPAD_MODULE
#include <Dabble.h>

// Right motor
int enableRightMotor = 5;
int rightMotorPin1 = 7;
int rightMotorPin2 = 8;

// Left motor
int enableLeftMotor = 6;
int leftMotorPin1 = 9;
int leftMotorPin2 = 10;

void setup()
{
  pinMode(enableRightMotor, OUTPUT);
  pinMode(rightMotorPin1, OUTPUT);
  pinMode(rightMotorPin2, OUTPUT);

  pinMode(enableLeftMotor, OUTPUT);
  pinMode(leftMotorPin1, OUTPUT);
  pinMode(leftMotorPin2, OUTPUT);

  rotateMotor(0, 0);

  Dabble.begin(9600, 2, 3);
}

void loop()
{
  int rightMotorSpeed = 0;
  int leftMotorSpeed = 0;

  Dabble.processInput();

  if (GamePad.isUpPressed())
  {
    rightMotorSpeed = 255;
    leftMotorSpeed = 255;
  }

  if (GamePad.isDownPressed())
  {
    rightMotorSpeed = -255;
    leftMotorSpeed = -255;
  }

  if (GamePad.isLeftPressed())
  {
    rightMotorSpeed = 255;
    leftMotorSpeed = 0;
  }

  if (GamePad.isRightPressed())
  {
    rightMotorSpeed = 0;
    leftMotorSpeed = 255;
  }

  rotateMotor(rightMotorSpeed, leftMotorSpeed);
}

void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)
{
  if (rightMotorSpeed < 0)
  {
    digitalWrite(rightMotorPin1, LOW);
    digitalWrite(rightMotorPin2, HIGH);
  }
  else if (rightMotorSpeed >= 0)
  {
    digitalWrite(rightMotorPin1, HIGH);
    digitalWrite(rightMotorPin2, LOW);
  }

  if (leftMotorSpeed < 0)
  {
    digitalWrite(leftMotorPin1, LOW);
    digitalWrite(leftMotorPin2, HIGH);
  }
  else if (leftMotorSpeed >= 0)
  {
    digitalWrite(leftMotorPin1, HIGH);
    digitalWrite(leftMotorPin2, LOW);
  }

  analogWrite(enableRightMotor, abs(rightMotorSpeed));
  analogWrite(enableLeftMotor, abs(leftMotorSpeed));
}
```

## 7. Explanation of the Code

### A. Define Dabble settings

```cpp
#define CUSTOM_SETTINGS
#define INCLUDE_GAMEPAD_MODULE
#include <Dabble.h>
```

- `CUSTOM_SETTINGS`: Enables custom selection of Dabble modules.
- `INCLUDE_GAMEPAD_MODULE`: Includes the GamePad module.
- `Dabble.h`: Includes the Dabble library.

### B. Motor pin declarations

```cpp
int enableRightMotor = 5;
int rightMotorPin1 = 7;
int rightMotorPin2 = 8;

int enableLeftMotor = 6;
int leftMotorPin1 = 9;
int leftMotorPin2 = 10;
```

These variables define the Arduino pins connected to the motor driver.

- Enable pins control motor speed using PWM.
- Two direction pins control the rotation direction of each motor.

### C. Setup function

```cpp
void setup()
{
  pinMode(enableRightMotor, OUTPUT);
  pinMode(rightMotorPin1, OUTPUT);
  pinMode(rightMotorPin2, OUTPUT);

  pinMode(enableLeftMotor, OUTPUT);
  pinMode(leftMotorPin1, OUTPUT);
  pinMode(leftMotorPin2, OUTPUT);

  rotateMotor(0, 0);

  Dabble.begin(9600, 2, 3);
}
```

The `setup()` function executes once when Arduino starts.

- `pinMode()`: Configures motor pins as outputs.
- `rotateMotor(0, 0)`: Stops both motors initially.
- `Dabble.begin(9600, 2, 3)`: Starts Dabble communication at 9600 baud using software serial pins RX 2 and TX 3.

### D. Loop function

```cpp
void loop()
{
  int rightMotorSpeed = 0;
  int leftMotorSpeed = 0;

  Dabble.processInput();
}
```

The `loop()` function runs continuously.

- Motor speeds are initially set to zero.
- `Dabble.processInput()` checks for new smartphone input.

### E. Forward movement

```cpp
if (GamePad.isUpPressed())
{
  rightMotorSpeed = 255;
  leftMotorSpeed = 255;
}
```

When the UP button is pressed, both motors rotate forward at full PWM speed.

### F. Backward movement

```cpp
if (GamePad.isDownPressed())
{
  rightMotorSpeed = -255;
  leftMotorSpeed = -255;
}
```

When the DOWN button is pressed, both motors rotate in the reverse direction.

### G. Left movement

```cpp
if (GamePad.isLeftPressed())
{
  rightMotorSpeed = 255;
  leftMotorSpeed = 0;
}
```

The right motor runs while the left motor stops. This makes the robot turn left.

### H. Right movement

```cpp
if (GamePad.isRightPressed())
{
  rightMotorSpeed = 0;
  leftMotorSpeed = 255;
}
```

The left motor runs while the right motor stops. This makes the robot turn right.

### I. Motor control function

```cpp
void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)
```

This function controls the direction and speed of both motors.

For the right motor:

```cpp
if (rightMotorSpeed < 0)
{
  digitalWrite(rightMotorPin1, LOW);
  digitalWrite(rightMotorPin2, HIGH);
}
else
{
  digitalWrite(rightMotorPin1, HIGH);
  digitalWrite(rightMotorPin2, LOW);
}
```

- Negative speed: Reverse direction.
- Zero or positive speed: Forward direction.

The same logic is used for the left motor.

### J. PWM speed control

```cpp
analogWrite(enableRightMotor, abs(rightMotorSpeed));
analogWrite(enableLeftMotor, abs(leftMotorSpeed));
```

- `analogWrite()`: Controls motor speed using PWM.
- `abs()`: Converts a negative speed into a positive value.
- `255`: Maximum PWM value on a typical Arduino Uno.
- `0`: Stops the motor.

## 8. Working Principle

1. Switch on the Arduino and Bluetooth module.
2. Open the Dabble application on the smartphone.
3. Connect the smartphone to the robot's Bluetooth module.
4. Open the GamePad module.
5. Press the UP, DOWN, LEFT, or RIGHT button.
6. Dabble sends the button information to Arduino.
7. Arduino processes the information.
8. Arduino sends direction and speed signals to the motor driver.
9. The motor driver controls both DC motors.
10. The robot moves according to the selected direction.

## 9. Movement Control Table

| GamePad button | Right motor | Left motor | Robot movement |
|---|---|---|---|
| UP | Forward | Forward | Forward |
| DOWN | Reverse | Reverse | Backward |
| LEFT | Forward | Stop | Turn left |
| RIGHT | Stop | Forward | Turn right |
| No button | Stop | Stop | Stop |

**Note:** The left and right turning behavior depends on the physical motor arrangement. If the robot turns in the opposite direction, check the motor wiring or reverse the relevant motor direction.

## 10. Block Diagram

```text
+----------------------+
| Smartphone           |
| Dabble GamePad App   |
+----------+-----------+
           |
           | Bluetooth
           |
+----------v-----------+
| Bluetooth Module     |
| HC-05 / Compatible   |
+----------+-----------+
           |
           | Serial Data
           |
+----------v-----------+
| Arduino Uno          |
| Processes Commands   |
+----------+-----------+
           |
           | Motor Signals
           |
+----------v-----------+
| Motor Driver         |
| L298N / H-Bridge     |
+----------+-----------+
           |
       +---+---+
       |       |
+------v--+ +--v-------+
| Right   | | Left     |
| Motor   | | Motor    |
+---------+ +----------+
```

## 11. Flowchart

```text
       START
         |
         v
 Initialize Motor Pins
         |
         v
 Initialize Dabble
         |
         v
 Read GamePad Input
         |
         v
 Is UP Pressed?
    | Yes
    v
 Both Motors Forward
    |
    v
 Is DOWN Pressed?
    | Yes
    v
 Both Motors Reverse
    |
    v
 Is LEFT Pressed?
    | Yes
    v
 Right Motor Forward
 Left Motor Stop
    |
    v
 Is RIGHT Pressed?
    | Yes
    v
 Right Motor Stop
 Left Motor Forward
    |
    v
 Apply Motor Speeds
         |
         v
 Repeat Loop
```

## 12. Important Functions

| Function | Purpose |
|---|---|
| `pinMode()` | Sets a pin as input or output |
| `digitalWrite()` | Sets a digital pin HIGH or LOW |
| `analogWrite()` | Produces PWM output |
| `Dabble.begin()` | Starts Dabble communication |
| `Dabble.processInput()` | Processes smartphone input |
| `GamePad.isUpPressed()` | Checks UP button |
| `GamePad.isDownPressed()` | Checks DOWN button |
| `GamePad.isLeftPressed()` | Checks LEFT button |
| `GamePad.isRightPressed()` | Checks RIGHT button |
| `rotateMotor()` | Controls motor direction and speed |
| `abs()` | Returns the absolute value |

## 13. Applications

1. Bluetooth-controlled robot cars.
2. Educational robotics projects.
3. Wireless vehicle control.
4. Beginner IoT and embedded-system projects.
5. Robotics practical demonstrations.
6. Smartphone-controlled experimental vehicles.

## 14. Advantages

- Easy smartphone control.
- Wireless communication.
- Simple motor-control logic.
- Low-cost educational project.
- Helps beginners understand robotics.

## 15. Limitations and Improvements

**Limitations in the original code:**

1. It does not explicitly handle Bluetooth disconnection.
2. It does not include a communication-loss safety timeout.
3. Turning uses one motor while stopping the other, so the turning movement may be less smooth.
4. It uses fixed full speed for all movements.
5. Simultaneous button presses can overwrite earlier motor commands because the code uses separate `if` statements.

**Suggested improvements:**

- Add a timeout that stops the robot if no valid command is received for a short period.
- Add adjustable motor speed.
- Add a STOP button.
- Use an `else if` structure or a defined priority for simultaneous button presses.
- Add obstacle detection using an ultrasonic sensor.
- Add battery-level monitoring.

## 16. Troubleshooting

| Problem | Possible solution |
|---|---|
| Bluetooth does not connect | Check pairing and module power |
| Robot does not move | Check motor driver and battery |
| Robot moves backward | Check motor wiring and direction logic |
| Robot turns in the wrong direction | Check left/right motor connections |
| One motor does not work | Check enable and direction pins |
| Dabble does not respond | Check library installation and baud rate |
| Arduino resets during movement | Use a suitable motor power supply |

## 17. Safety Precautions

- Do not connect DC motors directly to Arduino pins.
- Use a suitable motor driver.
- Use a separate power supply suitable for the motors.
- Connect Arduino and motor-driver grounds together.
- Check the voltage requirements of the Bluetooth module.
- Keep wheels lifted during the first motor test.
- Disconnect power before changing motor wiring.

## 18. Viva Questions and Answers

**Q1. What is Dabble?**

Dabble is a smartphone application and Arduino library that allows users to control hardware using mobile interfaces such as GamePad.

**Q2. What is the purpose of the GamePad module?**

It provides virtual buttons to control robot movement.

**Q3. What is Bluetooth communication?**

Bluetooth is a short-range wireless communication technology used to exchange data between devices.

**Q4. What is the purpose of a motor driver?**

A motor driver provides the current required to operate DC motors and controls their direction and speed.

**Q5. What is PWM?**

PWM stands for Pulse Width Modulation. It controls the average power delivered to a motor.

**Q6. What is the purpose of `analogWrite()`?**

It generates a PWM signal to control motor speed.

**Q7. Why is `abs()` used in this program?**

It converts negative motor-speed values into positive PWM values.

**Q8. What does `Dabble.processInput()` do?**

It reads and processes input received from the Dabble application.

**Q9. What is the maximum PWM value on a typical Arduino Uno?**

255.

**Q10. Why is a motor driver necessary?**

Arduino pins cannot supply the current normally required by DC motors, so a motor driver is used.

## 19. Conclusion

The Bluetooth-controlled robot car demonstrates how a smartphone can wirelessly control a robot using the Dabble GamePad application. Arduino receives the button commands and controls the direction and speed of two DC motors through a motor driver. This project helps beginners understand Bluetooth communication, Arduino programming, PWM, and basic robotics.