# ESP32 PS5 Controller-Based RC Car

## 1. Introduction

An RC (Remote Control) Car is a vehicle that can be controlled wirelessly using a remote controller.

In this project, an ESP32 is connected to a PS5 controller through Bluetooth. The ESP32 receives input from the controller and controls two DC motors and one servo motor.

The two DC motors move the car forward and backward, while the servo motor controls the steering direction.

## 2. Objectives

- To connect a PS5 controller with ESP32.
- To understand wireless communication using Bluetooth.
- To control DC motor speed and direction.
- To control steering using a servo motor.
- To build a wireless remote-controlled car.

## 3. Required Components

1. ESP32 development board
2. PS5 wireless controller
3. Two DC geared motors
4. Motor driver module (such as L298N, if compatible with the motors)
5. Servo motor
6. Robot car chassis
7. Battery for motors
8. Suitable regulated power supply for the ESP32 and servo
9. Jumper wires

## 4. Libraries Used

### 4.1 PS5 Controller Library

```cpp
#include <ps5Controller.h>
```

This library allows ESP32 to communicate with a PS5 controller and receive button and analog stick information.

### 4.2 ESP32 Servo Library

```cpp
#include <ESP32Servo.h>
```

This library allows ESP32 to control a servo motor by generating the required control signal.

**Installation:** Open Arduino IDE, select Library Manager, search for the required library, and install it.

## 5. Pin Connections

### 5.1 Servo Motor Connections

| Servo Wire | ESP32 Pin |
|---|---|
| Signal | GPIO 25 |
| VCC | Suitable external regulated supply |
| GND | Common GND |

### 5.2 Right DC Motor Connections

| Motor Driver Signal | ESP32 Pin |
|---|---|
| Enable Right Motor (ENA) | GPIO 22 |
| Right Motor Input 1 | GPIO 16 |
| Right Motor Input 2 | GPIO 17 |

### 5.3 Left DC Motor Connections

| Motor Driver Signal | ESP32 Pin |
|---|---|
| Enable Left Motor (ENB) | GPIO 23 |
| Left Motor Input 1 | GPIO 18 |
| Left Motor Input 2 | GPIO 19 |

The motor driver connects to the two DC motors and their external motor power supply.

**Important:** The motor driver and ESP32 must share a common ground. Never connect DC motors directly to ESP32 GPIO pins.

## 6. Complete Arduino Code

```cpp
#include <ps5Controller.h>
#include <ESP32Servo.h>

Servo steeringServo;
int steeringPin = 25;

// Right motor
int enableRightMotor = 22;
int rightMotorPin1 = 16;
int rightMotorPin2 = 17;

// Left motor
int enableLeftMotor = 23;
int leftMotorPin1 = 18;
int leftMotorPin2 = 19;

const int PWMFreq = 1000;
const int PWMResolution = 8;

const int rightMotorPWMSpeedChannel = 4;
const int leftMotorPWMSpeedChannel = 5;

void notify()
{
  int throttle = map(
    ps5.data.analog.stick.ly,
    -127, 127, -255, 255
  );

  int steering = map(
    ps5.data.analog.stick.rx,
    -127, 127, 60, 120
  );

  throttle = constrain(throttle, -255, 255);
  steering = constrain(steering, 60, 120);

  rotateMotor(throttle, throttle);

  steeringServo.write(steering);
}

void onConnect()
{
  Serial.println("Connected!.");
}

void onDisConnect()
{
  rotateMotor(0, 0);
  steeringServo.write(90);

  Serial.println("Disconnected!.");
}

void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)
{
  if (rightMotorSpeed < 0)
  {
    digitalWrite(rightMotorPin1, LOW);
    digitalWrite(rightMotorPin2, HIGH);
  }
  else if (rightMotorSpeed > 0)
  {
    digitalWrite(rightMotorPin1, HIGH);
    digitalWrite(rightMotorPin2, LOW);
  }
  else
  {
    digitalWrite(rightMotorPin1, LOW);
    digitalWrite(rightMotorPin2, LOW);
  }

  if (leftMotorSpeed < 0)
  {
    digitalWrite(leftMotorPin1, LOW);
    digitalWrite(leftMotorPin2, HIGH);
  }
  else if (leftMotorSpeed > 0)
  {
    digitalWrite(leftMotorPin1, HIGH);
    digitalWrite(leftMotorPin2, LOW);
  }
  else
  {
    digitalWrite(leftMotorPin1, LOW);
    digitalWrite(leftMotorPin2, LOW);
  }

  ledcWrite(
    rightMotorPWMSpeedChannel,
    abs(rightMotorSpeed)
  );

  ledcWrite(
    leftMotorPWMSpeedChannel,
    abs(leftMotorSpeed)
  );
}

void setUpPinModes()
{
  steeringServo.attach(steeringPin);

  pinMode(enableRightMotor, OUTPUT);
  pinMode(rightMotorPin1, OUTPUT);
  pinMode(rightMotorPin2, OUTPUT);

  pinMode(enableLeftMotor, OUTPUT);
  pinMode(leftMotorPin1, OUTPUT);
  pinMode(leftMotorPin2, OUTPUT);

  // Set up PWM for motor speed
  ledcSetup(
    rightMotorPWMSpeedChannel,
    PWMFreq,
    PWMResolution
  );

  ledcSetup(
    leftMotorPWMSpeedChannel,
    PWMFreq,
    PWMResolution
  );

  ledcAttachPin(
    enableRightMotor,
    rightMotorPWMSpeedChannel
  );

  ledcAttachPin(
    enableLeftMotor,
    leftMotorPWMSpeedChannel
  );

  rotateMotor(0, 0);
  steeringServo.write(90);
}

void setup()
{
  setUpPinModes();

  Serial.begin(115200);

  ps5.attach(notify);
  ps5.attachOnConnect(onConnect);
  ps5.attachOnDisconnect(onDisConnect);

  ps5.begin("ac:36:1b:2e:ad:e5");

  while (ps5.isConnected() == false)
  {
    Serial.println("PS5 controller not found");
    delay(300);
  }

  Serial.println("Ready.");
}

void loop()
{
}
```

**Note:** This is your supplied program with a small safety improvement: the steering servo returns to its center position when the controller disconnects.

## 7. Explanation of the Code

### 7.1 Include Libraries

```cpp
#include <ps5Controller.h>
#include <ESP32Servo.h>
```

- `ps5Controller.h`: Receives data from the PS5 controller.
- `ESP32Servo.h`: Controls the servo motor.

### 7.2 Create Servo Object

```cpp
Servo steeringServo;
int steeringPin = 25;
```

- `Servo`: Servo class provided by the ESP32Servo library.
- `steeringServo`: Object used to control the servo.
- `steeringPin = 25`: GPIO 25 is connected to the servo signal wire.

### 7.3 Define Motor Pins

```cpp
int enableRightMotor = 22;
int rightMotorPin1 = 16;
int rightMotorPin2 = 17;
```

These pins control the right DC motor.

```cpp
int enableLeftMotor = 23;
int leftMotorPin1 = 18;
int leftMotorPin2 = 19;
```

These pins control the left DC motor.

The enable pins control motor speed using PWM, while the two input pins control motor direction.

### 7.4 Define PWM Settings

```cpp
const int PWMFreq = 1000;
const int PWMResolution = 8;
```

- `PWMFreq = 1000`: Sets the PWM frequency to 1000 Hz (1 kHz).
- `PWMResolution = 8`: Uses 8-bit PWM resolution.

An 8-bit PWM signal has values from 0 to 255.

```cpp
const int rightMotorPWMSpeedChannel = 4;
const int leftMotorPWMSpeedChannel = 5;
```

These variables identify the PWM channels used for the right and left motors in the original ESP32 Arduino core 2.x API.

### 7.5 Notify Function

```cpp
void notify()
```

The `notify()` function is called when the PS5 controller sends updated data.

It reads the analog stick positions and uses them to control the car.

#### Read throttle

```cpp
int throttle = map(
  ps5.data.analog.stick.ly,
  -127, 127, -255, 255
);
```

This reads the Y-axis value of the left analog stick.

- Push forward: Positive motor speed.
- Pull backward: Negative motor speed.
- Keep centered: Approximately zero motor speed.

The `map()` function converts the input range from -127 to 127 into an output range from -255 to 255.

#### Read steering

```cpp
int steering = map(
  ps5.data.analog.stick.rx,
  -127, 127, 60, 120
);
```

This reads the X-axis value of the right analog stick.

- Move left: Servo moves toward 60 degrees.
- Keep centered: Servo moves toward 90 degrees.
- Move right: Servo moves toward 120 degrees.

#### Limit values

```cpp
throttle = constrain(throttle, -255, 255);
steering = constrain(steering, 60, 120);
```

The `constrain()` function keeps values within the specified minimum and maximum limits.

#### Control motors and steering

```cpp
rotateMotor(throttle, throttle);
steeringServo.write(steering);
```

- `rotateMotor()`: Controls both DC motors.
- `steeringServo.write()`: Sets the steering servo angle.

Both motors receive the same throttle value, so the car moves forward or backward.

### 7.6 onConnect Function

```cpp
void onConnect()
{
  Serial.println("Connected!.");
}
```

This function executes when the PS5 controller successfully connects to ESP32.

It displays a connection message in the Serial Monitor.

### 7.7 onDisConnect Function

```cpp
void onDisConnect()
{
  rotateMotor(0, 0);
  steeringServo.write(90);

  Serial.println("Disconnected!.");
}
```

This function executes when the controller disconnects.

- `rotateMotor(0, 0)`: Stops both motors.
- `steeringServo.write(90)`: Returns the steering servo to its center position.
- `Serial.println()`: Displays the disconnection message.

This is an important safety feature.

### 7.8 rotateMotor Function

```cpp
void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)
```

This function controls the direction and speed of both DC motors.

**Right motor forward:**

```cpp
digitalWrite(rightMotorPin1, HIGH);
digitalWrite(rightMotorPin2, LOW);
```

**Right motor backward:**

```cpp
digitalWrite(rightMotorPin1, LOW);
digitalWrite(rightMotorPin2, HIGH);
```

**Right motor stopped:**

```cpp
digitalWrite(rightMotorPin1, LOW);
digitalWrite(rightMotorPin2, LOW);
```

The left motor uses the same logic with its own pins.

#### Control motor speed

```cpp
ledcWrite(
  rightMotorPWMSpeedChannel,
  abs(rightMotorSpeed)
);
```

`ledcWrite()` generates the PWM output for the motor speed.

`abs()` returns the absolute value of a number.

For example:

- `abs(255) = 255`
- `abs(-255) = 255`
- `abs(0) = 0`

The motor direction is controlled separately using the direction pins.

### 7.9 setUpPinModes Function

```cpp
void setUpPinModes()
```

This function initializes the hardware.

```cpp
steeringServo.attach(steeringPin);
```

Connects the servo control signal to GPIO 25.

```cpp
pinMode(enableRightMotor, OUTPUT);
pinMode(rightMotorPin1, OUTPUT);
pinMode(rightMotorPin2, OUTPUT);
```

Configures the right motor pins as outputs.

The left motor pins are configured similarly.

#### Configure PWM

```cpp
ledcSetup(
  rightMotorPWMSpeedChannel,
  PWMFreq,
  PWMResolution
);
```

Configures the right motor PWM channel.

```cpp
ledcAttachPin(
  enableRightMotor,
  rightMotorPWMSpeedChannel
);
```

Connects the right motor enable pin to its PWM channel.

The same setup is performed for the left motor.

Finally:

```cpp
rotateMotor(0, 0);
steeringServo.write(90);
```

Stops the motors and centers the steering servo at startup.

### 7.10 Setup Function

```cpp
void setup()
{
  setUpPinModes();

  Serial.begin(115200);

  ps5.attach(notify);
  ps5.attachOnConnect(onConnect);
  ps5.attachOnDisconnect(onDisConnect);

  ps5.begin("ac:36:1b:2e:ad:e5");
}
```

The `setup()` function runs once when ESP32 starts.

- `setUpPinModes()`: Initializes hardware pins, PWM, and servo.
- `Serial.begin(115200)`: Starts serial communication.
- `ps5.attach(notify)`: Registers the controller data callback.
- `ps5.attachOnConnect(onConnect)`: Registers the connection callback.
- `ps5.attachOnDisconnect(onDisConnect)`: Registers the disconnection callback.
- `ps5.begin()`: Starts the PS5 controller connection process using the supplied Bluetooth address.

### 7.11 Loop Function

```cpp
void loop()
{
}
```

The loop is empty because this program uses callback functions.

The PS5 library calls `notify()`, `onConnect()`, and `onDisConnect()` when the corresponding controller events occur.

## 8. Working Principle

1. ESP32 starts and initializes the motor pins, PWM, and servo.
2. ESP32 starts Bluetooth communication.
3. The PS5 controller connects to ESP32.
4. The user moves the left analog stick.
5. ESP32 reads the throttle value.
6. The motor driver receives direction and PWM signals.
7. Both DC motors move the car forward or backward.
8. The user moves the right analog stick.
9. ESP32 reads the steering value.
10. The servo changes its angle to control steering.
11. If the controller disconnects, the program stops the motors.

## 9. Block Diagram

```text
       PS5 Controller
              |
              |
       Bluetooth Signal
              |
              v
          ESP32 Board
          /         \
         /           \
        v             v
   Motor Driver    Servo Motor
       |                |
       v                v
  Two DC Motors     Steering
       |                |
       +-------+--------+
               |
               v
          RC Car Movement
```

## 10. Control Operation Table

| PS5 Controller Input | ESP32 Action | Car Movement |
|---|---|---|
| Left stick forward | Positive throttle | Forward |
| Left stick backward | Negative throttle | Backward |
| Left stick center | Zero throttle | Stop |
| Right stick left | Servo near 60° | Left steering |
| Right stick center | Servo near 90° | Straight steering |
| Right stick right | Servo near 120° | Right steering |
| Controller disconnect | Motors stop | Stop |

The exact steering direction depends on servo mounting and mechanical linkage.

## 11. Motor Control Logic

| Motor Speed | Motor Pin 1 | Motor Pin 2 | Result |
|---|---|---|---|
| Positive | HIGH | LOW | Forward |
| Negative | LOW | HIGH | Reverse |
| Zero | LOW | LOW | Stop |

The motor driver's input logic and wiring determine the actual physical direction. If a motor rotates in the opposite direction, check its wiring and driver connections.

## 12. PWM Speed Control

PWM stands for Pulse Width Modulation.

It controls the average power delivered to a DC motor by switching the output ON and OFF rapidly.

This program uses 8-bit PWM resolution.

| PWM Value | Approximate Duty Cycle |
|---|---:|
| 0 | 0% |
| 64 | 25% |
| 128 | 50% |
| 192 | 75% |
| 255 | 100% |

A higher PWM value generally produces a higher motor speed, depending on the motor, load, and supply voltage.

## 13. Important ESP32 Bluetooth and PWM Notes

### PS5 controller Bluetooth address

```cpp
ps5.begin("ac:36:1b:2e:ad:e5");
```

The address is a controller Bluetooth MAC address, not a value that should be copied blindly.

Use the correct controller address required by your PS5 library and pairing procedure. The controller must be paired or discoverable according to the library's instructions.

### ESP32 Arduino core version

Your code uses:

```cpp
ledcSetup();
ledcAttachPin();
ledcWrite(channel, duty);
```

These are the older ESP32 Arduino core 2.x LEDC APIs.

In ESP32 Arduino core 3.x, the LEDC API changed. For example, PWM can be attached to a pin using `ledcAttach()`, and duty can be written using the pin-based `ledcWrite()` API.

If you get errors such as `ledcSetup was not declared`, check your ESP32 board package version and update the code to match that version.

## 14. Troubleshooting

| Problem | Possible Reason | Solution |
|---|---|---|
| PS5 controller not found | Incorrect Bluetooth address | Check the controller address and pairing instructions |
| Motors do not move | Motor driver or battery issue | Check motor power and connections |
| Only one motor works | Incorrect motor wiring | Check both motor driver channels |
| Car moves backward | Motor direction is reversed | Check motor wiring or direction logic |
| Servo does not move | Incorrect servo connection | Check signal and power connections |
| ESP32 restarts | Insufficient power supply | Use suitable regulated supplies |
| PWM functions produce errors | ESP32 core API mismatch | Check whether you use core 2.x or 3.x |

## 15. Safety Precautions

- Do not connect DC motors directly to ESP32 GPIO pins.
- Use a motor driver rated for the motor voltage and current.
- Use a suitable external power supply for the motors.
- Supply the servo with an appropriate regulated voltage and sufficient current.
- Connect the ESP32, motor driver, and servo supply grounds together.
- Check the servo's mechanical steering range before operating the car.
- Test the car with its wheels lifted before placing it on the ground.
- Keep a safe way to disconnect motor power during testing.

## 16. Applications

1. Wireless remote-controlled cars.
2. Robotics projects.
3. Smart vehicle prototypes.
4. Bluetooth-based vehicle control.
5. Educational embedded-system projects.
6. Wireless motor control systems.
7. Robotics and automation learning.

## 17. Viva Questions and Answers

**Q1. What is ESP32?**

ESP32 is a microcontroller development platform with built-in wireless communication features, including Wi-Fi and Bluetooth.

**Q2. What is a PS5 controller?**

A PS5 controller is a wireless game controller used to send input commands to a device.

**Q3. What is the use of Bluetooth in this project?**

Bluetooth provides wireless communication between the PS5 controller and ESP32.

**Q4. What is PWM?**

PWM stands for Pulse Width Modulation. It is used to control the speed of DC motors.

**Q5. What is the purpose of a motor driver?**

A motor driver controls the direction and speed of DC motors using signals from the microcontroller.

**Q6. Why is a servo motor used?**

A servo motor is used to control the steering direction of the car.

**Q7. What is the purpose of `map()`?**

The `map()` function converts a value from one range into another range.

**Q8. What is the purpose of `constrain()`?**

It limits a value within a specified minimum and maximum range.

**Q9. What is the use of `rotateMotor()`?**

It controls the direction and speed of the two DC motors.

**Q10. What is the purpose of `onDisConnect()`?**

It stops the motors when the PS5 controller disconnects.

**Q11. What is the purpose of `abs()`?**

It returns the absolute value of a number.

**Q12. Why do we use `steeringServo.write(90)`?**

It sets the servo to approximately 90 degrees, which is used as the center steering position.

**Q13. What is the purpose of `notify()`?**

It receives updated controller input and uses it to control the car.

**Q14. Why do we use a motor driver instead of connecting motors directly to ESP32?**

DC motors require more current than ESP32 GPIO pins can safely provide. A motor driver handles the motor power.

**Q15. What happens when the PS5 controller disconnects?**

The disconnect callback stops both motors and centers the steering servo.

## 18. Conclusion

In this project, we use an ESP32 and a PS5 wireless controller to control a remote-controlled car. The left analog stick controls the speed and direction of the two DC motors, while the right analog stick controls the steering servo.

This project demonstrates Bluetooth communication, PWM motor control, servo interfacing, and embedded-system programming.

---

**Project Name:** ESP32 PS5 Controller-Based RC Car

**Microcontroller:** ESP32

**Controller:** PS5 Wireless Controller

**Programming Language:** C/C++ (Arduino)

**Communication:** Bluetooth

**Motor Control:** PWM

**Steering Control:** Servo Motor

**Main Output:** Wireless forward, backward, and steering control of an RC car.