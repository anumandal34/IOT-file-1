# ESP32 Four-Wheel Robot Car Using Serial Communication and PWM Motor Control

**Suggested filename:** `ESP32_Four_Wheel_Robot_Car_Serial_Communication_Notes.md`

## 1. Introduction

This project controls a four-wheel robot car using an ESP32 microcontroller and a motor driver. The ESP32 receives movement commands through serial communication and controls four DC motors independently.

The robot supports multiple movements, including forward, backward, left, right, diagonal movement, turning, and stopping.

It also supports adjustable motor speed using PWM (Pulse Width Modulation).

## 2. Objectives

- Understand ESP32 microcontroller programming.
- Learn serial communication using UART.
- Control four DC motors independently.
- Understand PWM-based motor speed control.
- Learn different robot movement techniques.
- Understand motor direction control.
- Explore wireless robot-control systems.

## 3. Required Components

1. ESP32 development board
2. Four DC geared motors
3. Motor driver modules suitable for four motors
4. Four-wheel robot chassis
5. Four wheels
6. External battery
7. Connecting wires
8. Serial communication transmitter/controller
9. USB cable
10. Arduino IDE

## 4. Software and Libraries

| Library/header | Purpose |
|---|---|
| `vector` | Stores motor pin configurations |
| `iostream` | Provides C++ input/output functionality |
| `sstream` | Splits incoming command strings |
| `Arduino.h` | Provides ESP32 Arduino functions |

The ESP32 Arduino core provides functions such as `pinMode()`, `digitalWrite()`, `Serial1`, and LEDC PWM functions.

## 5. ESP32 Pin Configuration

| Motor | IN1 | IN2 | Enable/PWM | PWM channel |
|---|---:|---:|---:|---:|
| Front right | GPIO 16 | GPIO 17 | GPIO 22 | 4 |
| Back right | GPIO 18 | GPIO 19 | GPIO 23 | 5 |
| Front left | GPIO 26 | GPIO 25 | GPIO 32 | 6 |
| Back left | GPIO 14 | GPIO 27 | GPIO 33 | 7 |

### Serial communication pins

| Serial connection | ESP32 pin |
|---|---|
| Serial1 RX | GPIO 4 |
| Serial1 TX | GPIO 15 |
| USB Serial | USB connection |

`Serial1.begin(115200, SERIAL_8N1, 4, 15)` configures UART1 to receive on GPIO 4 and transmit on GPIO 15.

## 6. Complete Arduino Code

```cpp
#include <vector>
#include <iostream>
#include <sstream>

#define UP 1
#define DOWN 2
#define LEFT 3
#define RIGHT 4
#define UP_LEFT 5
#define UP_RIGHT 6
#define DOWN_LEFT 7
#define DOWN_RIGHT 8
#define TURN_LEFT 9
#define TURN_RIGHT 10
#define STOP 0

#define FRONT_RIGHT_MOTOR 0
#define BACK_RIGHT_MOTOR 1
#define FRONT_LEFT_MOTOR 2
#define BACK_LEFT_MOTOR 3

#define FORWARD 1
#define BACKWARD -1

struct MOTOR_PINS
{
  int pinIN1;
  int pinIN2;
  int pinEn;
  int pwmSpeedChannel;
};

std::vector<MOTOR_PINS> motorPins =
{
  {16, 17, 22, 4},
  {18, 19, 23, 5},
  {26, 25, 32, 6},
  {14, 27, 33, 7},
};

const int PWMFreq = 1000;
const int PWMResolution = 8;

#define SIGNAL_TIMEOUT 1000
unsigned long lastRecvTime = 0;

void processCarMovement(int inputValue)
{
  switch(inputValue)
  {
    case UP:
      rotateMotor(FRONT_RIGHT_MOTOR, FORWARD);
      rotateMotor(BACK_RIGHT_MOTOR, FORWARD);
      rotateMotor(FRONT_LEFT_MOTOR, FORWARD);
      rotateMotor(BACK_LEFT_MOTOR, FORWARD);
      break;

    case DOWN:
      rotateMotor(FRONT_RIGHT_MOTOR, BACKWARD);
      rotateMotor(BACK_RIGHT_MOTOR, BACKWARD);
      rotateMotor(FRONT_LEFT_MOTOR, BACKWARD);
      rotateMotor(BACK_LEFT_MOTOR, BACKWARD);
      break;

    case LEFT:
      rotateMotor(FRONT_RIGHT_MOTOR, FORWARD);
      rotateMotor(BACK_RIGHT_MOTOR, BACKWARD);
      rotateMotor(FRONT_LEFT_MOTOR, BACKWARD);
      rotateMotor(BACK_LEFT_MOTOR, FORWARD);
      break;

    case RIGHT:
      rotateMotor(FRONT_RIGHT_MOTOR, BACKWARD);
      rotateMotor(BACK_RIGHT_MOTOR, FORWARD);
      rotateMotor(FRONT_LEFT_MOTOR, FORWARD);
      rotateMotor(BACK_LEFT_MOTOR, BACKWARD);
      break;

    case UP_LEFT:
      rotateMotor(FRONT_RIGHT_MOTOR, FORWARD);
      rotateMotor(BACK_RIGHT_MOTOR, STOP);
      rotateMotor(FRONT_LEFT_MOTOR, STOP);
      rotateMotor(BACK_LEFT_MOTOR, FORWARD);
      break;

    case UP_RIGHT:
      rotateMotor(FRONT_RIGHT_MOTOR, STOP);
      rotateMotor(BACK_RIGHT_MOTOR, FORWARD);
      rotateMotor(FRONT_LEFT_MOTOR, FORWARD);
      rotateMotor(BACK_LEFT_MOTOR, STOP);
      break;

    case DOWN_LEFT:
      rotateMotor(FRONT_RIGHT_MOTOR, STOP);
      rotateMotor(BACK_RIGHT_MOTOR, BACKWARD);
      rotateMotor(FRONT_LEFT_MOTOR, BACKWARD);
      rotateMotor(BACK_LEFT_MOTOR, STOP);
      break;

    case DOWN_RIGHT:
      rotateMotor(FRONT_RIGHT_MOTOR, BACKWARD);
      rotateMotor(BACK_RIGHT_MOTOR, STOP);
      rotateMotor(FRONT_LEFT_MOTOR, STOP);
      rotateMotor(BACK_LEFT_MOTOR, BACKWARD);
      break;

    case TURN_LEFT:
      rotateMotor(FRONT_RIGHT_MOTOR, FORWARD);
      rotateMotor(BACK_RIGHT_MOTOR, FORWARD);
      rotateMotor(FRONT_LEFT_MOTOR, BACKWARD);
      rotateMotor(BACK_LEFT_MOTOR, BACKWARD);
      break;

    case TURN_RIGHT:
      rotateMotor(FRONT_RIGHT_MOTOR, BACKWARD);
      rotateMotor(BACK_RIGHT_MOTOR, BACKWARD);
      rotateMotor(FRONT_LEFT_MOTOR, FORWARD);
      rotateMotor(BACK_LEFT_MOTOR, FORWARD);
      break;

    case STOP:
      rotateMotor(FRONT_RIGHT_MOTOR, STOP);
      rotateMotor(BACK_RIGHT_MOTOR, STOP);
      rotateMotor(FRONT_LEFT_MOTOR, STOP);
      rotateMotor(BACK_LEFT_MOTOR, STOP);
      break;

    default:
      rotateMotor(FRONT_RIGHT_MOTOR, STOP);
      rotateMotor(BACK_RIGHT_MOTOR, STOP);
      rotateMotor(FRONT_LEFT_MOTOR, STOP);
      rotateMotor(BACK_LEFT_MOTOR, STOP);
      break;
  }
}

void rotateMotor(int motorNumber, int motorDirection)
{
  if (motorDirection == FORWARD)
  {
    digitalWrite(motorPins[motorNumber].pinIN1, HIGH);
    digitalWrite(motorPins[motorNumber].pinIN2, LOW);
  }
  else if (motorDirection == BACKWARD)
  {
    digitalWrite(motorPins[motorNumber].pinIN1, LOW);
    digitalWrite(motorPins[motorNumber].pinIN2, HIGH);
  }
  else
  {
    digitalWrite(motorPins[motorNumber].pinIN1, LOW);
    digitalWrite(motorPins[motorNumber].pinIN2, LOW);
  }
}

void setUpPinModes()
{
  for (int i = 0; i < motorPins.size(); i++)
  {
    pinMode(motorPins[i].pinIN1, OUTPUT);
    pinMode(motorPins[i].pinIN2, OUTPUT);

    ledcSetup(motorPins[i].pwmSpeedChannel, PWMFreq, PWMResolution);
    ledcAttachPin(motorPins[i].pinEn, motorPins[i].pwmSpeedChannel);

    rotateMotor(i, STOP);
  }
}

void setup()
{
  setUpPinModes();

  Serial1.begin(115200, SERIAL_8N1, 4, 15);
  Serial.begin(115200);
}

void loop()
{
  String dataString;
  char key[500];
  int value;

  if (Serial1.available())
  {
    dataString = Serial1.readStringUntil('\n');

    std::string myData = dataString.c_str();
    std::istringstream ss(myData);

    std::string key, value;

    std::getline(ss, key, ',');
    std::getline(ss, value, ',');

    Serial.printf("Key [%s] Value[%s]\n",
                  key.c_str(), value.c_str());

    int valueInt = atoi(value.c_str());

    if (key == "MoveCar")
    {
      processCarMovement(valueInt);
    }
    else if (key == "Speed")
    {
      for (int i = 0; i < motorPins.size(); i++)
      {
        ledcWrite(motorPins[i].pwmSpeedChannel, valueInt);
      }
    }

    lastRecvTime = millis();
  }
}
```

## 7. Explanation of the Code

### A. Header files

```cpp
#include <vector>
#include <iostream>
#include <sstream>
```

- `vector`: Stores the configuration of the four motors.
- `iostream`: Provides C++ input/output features.
- `sstream`: Helps split incoming text into separate parts.

### B. Movement definitions

```cpp
#define UP 1
#define DOWN 2
#define LEFT 3
#define RIGHT 4
#define UP_LEFT 5
#define UP_RIGHT 6
#define DOWN_LEFT 7
#define DOWN_RIGHT 8
#define TURN_LEFT 9
#define TURN_RIGHT 10
#define STOP 0
```

These definitions assign a numeric value to each movement command.

For example:

- `UP` represents forward movement.
- `DOWN` represents backward movement.
- `LEFT` represents left movement.
- `RIGHT` represents right movement.
- `STOP` represents stopping all motors.

### C. Motor identification

```cpp
#define FRONT_RIGHT_MOTOR 0
#define BACK_RIGHT_MOTOR 1
#define FRONT_LEFT_MOTOR 2
#define BACK_LEFT_MOTOR 3
```

These constants identify the four motors.

The motor index is used to access the correct motor configuration in the vector.

### D. Direction definitions

```cpp
#define FORWARD 1
#define BACKWARD -1
```

These constants represent the two directions of motor rotation.

- `FORWARD = 1`
- `BACKWARD = -1`

The program uses these values in the `rotateMotor()` function.

### E. MOTOR_PINS structure

```cpp
struct MOTOR_PINS
{
  int pinIN1;
  int pinIN2;
  int pinEn;
  int pwmSpeedChannel;
};
```

A structure groups related information together.

Each motor requires four configuration values:

1. `pinIN1`: First motor direction pin.
2. `pinIN2`: Second motor direction pin.
3. `pinEn`: Motor enable/PWM pin.
4. `pwmSpeedChannel`: ESP32 PWM channel.

### F. Motor configuration vector

```cpp
std::vector<MOTOR_PINS> motorPins =
{
  {16, 17, 22, 4},
  {18, 19, 23, 5},
  {26, 25, 32, 6},
  {14, 27, 33, 7},
};
```

This vector stores the pin configuration of all four motors.

| Index | Motor | IN1 | IN2 | Enable | PWM channel |
|---|---|---:|---:|---:|---:|
| 0 | Front right | 16 | 17 | 22 | 4 |
| 1 | Back right | 18 | 19 | 23 | 5 |
| 2 | Front left | 26 | 25 | 32 | 6 |
| 3 | Back left | 14 | 27 | 33 | 7 |

### G. PWM configuration

```cpp
const int PWMFreq = 1000;
const int PWMResolution = 8;
```

PWM means Pulse Width Modulation.

- Frequency: 1000 Hz.
- Resolution: 8 bits.
- PWM range: 0–255.

Examples:

| PWM value | Meaning |
|---:|---|
| 0 | 0% duty cycle |
| 64 | Approximately 25% |
| 128 | Approximately 50% |
| 192 | Approximately 75% |
| 255 | Approximately 100% |

The actual motor speed also depends on the motor, battery voltage, load, and driver.

### H. Signal timeout variable

```cpp
#define SIGNAL_TIMEOUT 1000
unsigned long lastRecvTime = 0;
```

- `SIGNAL_TIMEOUT`: Defines a timeout duration of 1000 milliseconds.
- `lastRecvTime`: Stores the time of the last received command.

**Important:** The original code declares these variables but does not use them to stop the robot. A timeout check must be added to make this feature functional.

### I. processCarMovement() function

```cpp
void processCarMovement(int inputValue)
{
  switch(inputValue)
  {
    // Movement commands
  }
}
```

This function receives a movement command and selects the appropriate motor directions.

It uses a `switch` statement to check the command.

For example:

```cpp
case UP:
```

When the UP command arrives, all four motors rotate forward.

### J. Forward movement

```cpp
case UP:
  rotateMotor(FRONT_RIGHT_MOTOR, FORWARD);
  rotateMotor(BACK_RIGHT_MOTOR, FORWARD);
  rotateMotor(FRONT_LEFT_MOTOR, FORWARD);
  rotateMotor(BACK_LEFT_MOTOR, FORWARD);
  break;
```

All four motors rotate forward, so the robot moves forward.

### K. Backward movement

```cpp
case DOWN:
  rotateMotor(FRONT_RIGHT_MOTOR, BACKWARD);
  rotateMotor(BACK_RIGHT_MOTOR, BACKWARD);
  rotateMotor(FRONT_LEFT_MOTOR, BACKWARD);
  rotateMotor(BACK_LEFT_MOTOR, BACKWARD);
  break;
```

All four motors rotate backward.

### L. Left and right movement

The LEFT and RIGHT commands rotate the motors on opposite sides in opposite directions.

This creates a turning movement.

### M. Diagonal movement

The program also supports four diagonal commands:

- `UP_LEFT`
- `UP_RIGHT`
- `DOWN_LEFT`
- `DOWN_RIGHT`

These commands stop two motors and run the other two motors.

The result depends on the robot chassis and wheel arrangement. With ordinary fixed wheels, this may produce a curved or pivoting movement rather than true diagonal motion.

### N. TURN_LEFT and TURN_RIGHT

```cpp
case TURN_LEFT:
```

The right-side motors move forward, while the left-side motors move backward.

```cpp
case TURN_RIGHT:
```

The right-side motors move backward, while the left-side motors move forward.

This allows the robot to rotate around its center.

### O. rotateMotor() function

```cpp
void rotateMotor(int motorNumber, int motorDirection)
```

This function controls the direction of an individual motor.

For forward:

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, HIGH);
digitalWrite(motorPins[motorNumber].pinIN2, LOW);
```

For backward:

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, LOW);
digitalWrite(motorPins[motorNumber].pinIN2, HIGH);
```

For stop:

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, LOW);
digitalWrite(motorPins[motorNumber].pinIN2, LOW);
```

The function uses the motor index to select the correct pins from the vector.

### P. setUpPinModes() function

```cpp
void setUpPinModes()
{
  for (int i = 0; i < motorPins.size(); i++)
  {
    pinMode(motorPins[i].pinIN1, OUTPUT);
    pinMode(motorPins[i].pinIN2, OUTPUT);

    ledcSetup(motorPins[i].pwmSpeedChannel, PWMFreq, PWMResolution);
    ledcAttachPin(motorPins[i].pinEn, motorPins[i].pwmSpeedChannel);

    rotateMotor(i, STOP);
  }
}
```

This function configures all four motors.

- `for` loop: Repeats the configuration for each motor.
- `pinMode()`: Sets direction pins as outputs.
- `ledcSetup()`: Configures PWM frequency and resolution.
- `ledcAttachPin()`: Connects a PWM channel to an output pin.
- `rotateMotor(i, STOP)`: Stops the motor initially.

**ESP32 version note:** These `ledcSetup()` and `ledcAttachPin()` functions are used with the Arduino-ESP32 2.x core. Arduino-ESP32 3.x changed the LEDC API to functions such as `ledcAttach()` and `ledcWrite()` using pins rather than the old channel setup pattern. The original code may need modification for core 3.x.

### Q. setup() function

```cpp
void setup()
{
  setUpPinModes();

  Serial1.begin(115200, SERIAL_8N1, 4, 15);
  Serial.begin(115200);
}
```

The setup function runs once.

- `setUpPinModes()`: Configures all motor pins and PWM.
- `Serial1.begin()`: Starts UART1 communication.
- `Serial.begin()`: Starts USB serial communication for debugging.

`SERIAL_8N1` means:

- 8 data bits.
- No parity bit.
- 1 stop bit.

### R. loop() function

```cpp
void loop()
{
  String dataString;

  if (Serial1.available())
  {
    dataString = Serial1.readStringUntil('\n');
  }
}
```

The loop continuously checks for incoming serial data.

`Serial1.available()` checks whether data has arrived.

`Serial1.readStringUntil('\n')` reads the incoming message until a newline character is found.

### S. Split incoming data

```cpp
std::string myData = dataString.c_str();
std::istringstream ss(myData);

std::string key, value;

std::getline(ss, key, ',');
std::getline(ss, value, ',');
```

This code separates the received message into two parts using a comma.

Example incoming message:

```text
MoveCar,1
```

After splitting:

- `key = "MoveCar"`
- `value = "1"`

Another example:

```text
Speed,180
```

After splitting:

- `key = "Speed"`
- `value = "180"`

### T. Convert string to integer

```cpp
int valueInt = atoi(value.c_str());
```

`atoi()` converts a numeric string into an integer.

Examples:

- `"1"` becomes `1`.
- `"180"` becomes `180`.
- `"255"` becomes `255`.

### U. Process movement command

```cpp
if (key == "MoveCar")
{
  processCarMovement(valueInt);
}
```

If the received key is `MoveCar`, Arduino calls the movement function.

### V. Control motor speed

```cpp
else if (key == "Speed")
{
  for (int i = 0; i < motorPins.size(); i++)
  {
    ledcWrite(motorPins[i].pwmSpeedChannel, valueInt);
  }
}
```

If the received key is `Speed`, the program updates the PWM duty cycle of all four motors.

For example, a speed command of 180 sets the PWM duty cycle to 180 out of 255.

The original program does not validate the speed value. A safer implementation should restrict it to 0–255.

## 8. Movement Control Table

| Command | Numeric value | Robot movement |
|---|---:|---|
| UP | 1 | Forward |
| DOWN | 2 | Backward |
| LEFT | 3 | Left |
| RIGHT | 4 | Right |
| UP_LEFT | 5 | Diagonal/curved upper-left movement |
| UP_RIGHT | 6 | Diagonal/curved upper-right movement |
| DOWN_LEFT | 7 | Diagonal/curved lower-left movement |
| DOWN_RIGHT | 8 | Diagonal/curved lower-right movement |
| TURN_LEFT | 9 | Rotate left |
| TURN_RIGHT | 10 | Rotate right |
| STOP | 0 | Stop all motors |

## 9. Example Serial Commands

The controller must send messages in the following format:

```text
MoveCar,1
MoveCar,2
MoveCar,3
MoveCar,4
MoveCar,0
Speed,130
Speed,180
Speed,255
```

Each message should end with a newline character (`\n`).

## 10. Block Diagram

```text
+------------------------+
| Remote Controller      |
| Sends Movement Commands|
+-----------+------------+
            |
            | UART Serial
            |
+-----------v------------+
| ESP32 Microcontroller  |
| Receives Commands      |
| Processes Movement     |
| Controls PWM Speed     |
+-----------+------------+
            |
            | Direction + PWM
            |
+-----------v------------+
| Motor Driver Modules   |
+-----------+------------+
            |
       +----+----+
       |         |
       v         v
+-------------+ +-------------+
| Right Motors| | Left Motors |
| Front/Back  | | Front/Back  |
+-------------+ +-------------+
```

## 11. Flowchart

```text
          START
            |
            v
      Initialize ESP32
            |
            v
    Configure Motor Pins
            |
            v
      Configure PWM
            |
            v
      Start Serial1
            |
            v
    Check Serial Data
            |
            v
     Data Available?
       /       \
     No         Yes
     |           |
     |           v
     |      Read Message
     |           |
     |           v
     |      Split Key/Value
     |           |
     |           v
     |      Convert Value
     |           |
     |           v
     |      Check Command
     |           |
     |      +----+----+
     |      |         |
     |   MoveCar    Speed
     |      |         |
     |      v         v
     |  Move Motors  Set PWM
     |      |         |
     +------+---------+
            |
            v
       Repeat Loop
```

## 12. Important Functions

| Function | Purpose |
|---|---|
| `pinMode()` | Configures pins |
| `digitalWrite()` | Controls motor direction |
| `ledcSetup()` | Configures PWM on ESP32 core 2.x |
| `ledcAttachPin()` | Attaches a PWM channel to a pin on core 2.x |
| `ledcWrite()` | Sets PWM duty cycle |
| `Serial1.begin()` | Starts UART1 |
| `Serial1.available()` | Checks incoming serial data |
| `Serial1.readStringUntil()` | Reads a message until a delimiter |
| `std::getline()` | Splits a string |
| `atoi()` | Converts a numeric string to an integer |
| `processCarMovement()` | Selects robot movement |
| `rotateMotor()` | Controls one motor's direction |
| `setUpPinModes()` | Configures all motor pins |

## 13. Applications

1. Four-wheel robotic vehicles.
2. Remote-controlled robot cars.
3. Educational robotics projects.
4. ESP32-based automation.
5. Wireless vehicle control.
6. Robotics research and demonstrations.

## 14. Advantages

- Controls four motors independently.
- Supports multiple movement directions.
- Allows adjustable motor speed.
- Uses ESP32 UART communication.
- Uses structured motor configuration.
- Can be extended with Wi-Fi or Bluetooth control.

## 15. Limitations and Improvements

### Problems in the original code

1. **Timeout is not implemented:** `SIGNAL_TIMEOUT` is declared but never checked.
2. **PWM initial speed is not set:** The program configures PWM channels but does not write an initial duty cycle.
3. **No speed validation:** Invalid values outside 0–255 may be received.
4. **Blocking serial reading:** `readStringUntil()` may wait for a newline or timeout, delaying other operations.
5. **Unused variables:** `key[500]` and `value` are declared but not used in the outer loop.
6. **ESP32 compatibility:** The LEDC functions depend on the ESP32 Arduino core version.
7. **No message validation:** An incomplete or malformed command may be interpreted incorrectly.
8. **Motor driver requirements:** Each motor requires a suitable driver channel with enough current capacity.

### Recommended improvements

- Add a communication timeout that stops all motors.
- Set an initial PWM speed for all four motors.
- Validate speed values between 0 and 255.
- Validate the command before controlling the motors.
- Use a non-blocking serial parser.
- Add obstacle detection using ultrasonic sensors.
- Add battery-level monitoring.
- Add an emergency stop command.

## 16. Troubleshooting

| Problem | Possible solution |
|---|---|
| ESP32 does not receive commands | Check UART RX/TX connections |
| Robot does not move | Check motor-driver power and wiring |
| One motor does not work | Check its direction and enable pins |
| Speed does not change | Check PWM configuration and enable connections |
| Robot moves in the wrong direction | Reverse the motor direction wiring or logic |
| Commands are incomplete | Check newline termination and baud rate |
| Code does not compile | Check the ESP32 Arduino core version |
| ESP32 restarts during movement | Check motor supply and electrical noise |

## 17. Safety Precautions

- Do not connect motors directly to ESP32 GPIO pins.
- Use motor drivers with suitable voltage and current ratings.
- Use an external motor power supply.
- Connect ESP32 and motor-driver grounds together.
- Check the battery voltage before connecting it.
- Keep the wheels raised during initial testing.
- Stop the robot before changing wiring.
- Ensure the driver supports the current required by all four motors.

## 18. Viva Questions and Answers

**Q1. What is ESP32?**

ESP32 is a microcontroller development platform that supports wireless communication and GPIO control.

**Q2. What is UART?**

UART stands for Universal Asynchronous Receiver/Transmitter. It is used for serial communication between devices.

**Q3. What is PWM?**

PWM stands for Pulse Width Modulation. It controls the average power delivered to a motor.

**Q4. What is the purpose of the L298N motor driver?**

It controls the direction and speed of DC motors.

**Q5. Why are four motors used?**

Four motors provide drive power to the four wheels of the robot.

**Q6. What is a vector in C++?**

A vector is a dynamic array that can store multiple elements.

**Q7. What is a structure in C++?**

A structure groups related variables under one name.

**Q8. What is the purpose of `switch`?**

It selects one operation from multiple possible cases.

**Q9. What does `atoi()` do?**

It converts a numeric string into an integer.

**Q10. What is the purpose of `ledcWrite()`?**

It sets the PWM duty cycle on an ESP32 LEDC output.

**Q11. What is the purpose of `readStringUntil('\n')`?**

It reads serial data until a newline character is received.

**Q12. What is a signal timeout?**

It is a time limit after which the robot should stop if no new control signal arrives.

## 19. Conclusion

This project demonstrates how an ESP32 can control a four-wheel robot using serial commands. The program processes movement and speed commands, selects the required motor directions, and controls PWM outputs.

It supports forward, backward, left, right, diagonal, turning, and stopping movements. This project provides practical knowledge of ESP32 programming, C++ structures and vectors, UART communication, PWM, and robotics.