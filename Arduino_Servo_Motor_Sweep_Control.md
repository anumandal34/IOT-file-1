# Arduino Servo Motor Sweep Control

## 1. Introduction

The Arduino Servo Motor Sweep Control project demonstrates how to control a servo motor using an Arduino board.

The servo motor rotates gradually from 0 degrees to 180 degrees and then returns from 180 degrees to 0 degrees. This movement repeats continuously.

This project helps beginners understand servo motor control, Arduino programming, loops, and time delays.

## 2. Objectives

- To control a servo motor using Arduino.
- To understand servo motor rotation.
- To move a servo motor between 0° and 180°.
- To learn how to use the Servo library.
- To understand `for` loops and `delay()` functions.

## 3. Components Required

1. Arduino UNO
2. Servo Motor (SG90)
3. Jumper Wires
4. Breadboard (optional)
5. USB Cable
6. Computer with Arduino IDE

## 4. Pin Connections

| Servo Wire | Arduino Connection |
|---|---|
| Signal (Orange/Yellow) | Digital Pin 3 |
| VCC (Red) | 5V suitable supply |
| GND (Brown/Black) | GND |

**Note:** If the servo draws too much current, use a separate regulated 5V supply. Connect the external supply ground to Arduino GND.

## 5. Arduino Code

```cpp
#include <Servo.h>

Servo servo;

void setup()
{
  servo.attach(3);
}

void loop()
{
  for(int i = 0; i < 180; i++)
  {
    servo.write(i);
    delay(50);
  }

  for(int i = 180; i >= 0; i--)
  {
    servo.write(i);
    delay(50);
  }
}
```

## 6. Explanation of the Code

### A. Include Servo Library

```cpp
#include <Servo.h>
```

This library allows Arduino to control a servo motor by sending it position commands.

### B. Create Servo Object

```cpp
Servo servo;
```

Creates a servo object named `servo` that is used to control the motor.

### C. Setup Function

```cpp
void setup()
{
  servo.attach(3);
}
```

The `setup()` function runs only once when Arduino starts.

- `servo.attach(3)` connects the servo control signal to Arduino digital pin 3.

### D. Loop Function

```cpp
void loop()
{
}
```

The `loop()` function runs continuously after the setup function.

It contains two `for` loops that control the servo movement.

### E. Move Servo from 0° to 180°

```cpp
for(int i = 0; i < 180; i++)
{
  servo.write(i);
  delay(50);
}
```

This loop gradually moves the servo motor from 0 degrees toward 179 degrees.

- `int i = 0`: Starts the angle at 0.
- `i < 180`: Continues while the angle is less than 180.
- `i++`: Increases the angle by one degree.
- `servo.write(i)`: Sends the angle command to the servo.
- `delay(50)`: Waits 50 milliseconds between commands.

### F. Move Servo from 180° to 0°

```cpp
for(int i = 180; i >= 0; i--)
{
  servo.write(i);
  delay(50);
}
```

This loop moves the servo back from 180 degrees to 0 degrees.

- `int i = 180`: Starts at 180 degrees.
- `i >= 0`: Continues until the angle reaches 0.
- `i--`: Decreases the angle by one degree.
- `servo.write(i)`: Commands the servo to move to the selected angle.
- `delay(50)`: Waits 50 milliseconds between commands.

## 7. Working Principle

1. Arduino starts and initializes the servo motor.
2. The servo signal wire is connected to digital pin 3.
3. The first `for` loop gradually increases the angle from 0° to 179°.
4. The second `for` loop decreases the angle from 180° to 0°.
5. Each angle command is followed by a 50-millisecond delay.
6. After completing both movements, the loop starts again.
7. The servo continuously moves forward and backward.

## 8. Expected Output

| Stage | Servo Position |
|---|---|
| Starting | 0° |
| Forward movement | 0° to 179° |
| Reverse movement | 180° to 0° |
| Repetition | Starts again |

The servo continuously sweeps between its minimum and maximum commanded positions.

**Note:** The actual movement speed and angle range depend on the servo motor model.

## 9. Applications

- Robotics projects
- Automatic door systems
- Robotic arm movement
- Camera positioning systems
- Object tracking projects
- Smart home automation
- Educational electronics projects

## 10. Advantages

- Simple and beginner-friendly code.
- Easy to understand servo motor control.
- Requires only a few components.
- Demonstrates forward and backward movement.
- Useful for learning Arduino programming.

## 11. Limitations

- Standard hobby servos generally have a limited rotation range.
- The actual angle may differ slightly from the commanded angle.
- Servo movement can be affected by insufficient power.
- The 50-millisecond delay makes the movement relatively slow.
- This code does not use feedback to confirm the actual servo position.

## 12. Safety and Improvements

- Use a suitable power supply for the servo motor.
- Connect Arduino and external supply grounds together.
- Do not force the servo shaft beyond its mechanical limits.
- Avoid connecting a high-current servo directly to an Arduino output pin.
- Use a suitable servo model for the required rotation range.

## 13. Conclusion

The Arduino Servo Motor Sweep Control project demonstrates how to move a servo motor between 0 and 180 degrees using Arduino programming.

The Servo library makes it easy to control the motor, while `for` loops and `delay()` functions create gradual forward and backward movement.

This project is useful for beginners learning servo motor interfacing, loops, and basic robotics applications.