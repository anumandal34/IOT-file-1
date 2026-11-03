# Ultrasonic Sensor-Based Servo Motor Control

## 1. Introduction

The Ultrasonic Sensor-Based Servo Motor Control project uses an ultrasonic sensor to measure the distance between the sensor and an object. When an object comes within a specified distance (16 cm), the Arduino controls a servo motor to move between different angles.

This project demonstrates distance measurement, object detection, and automatic servo motor control using Arduino.

## 2. Objectives

- To measure object distance using an ultrasonic sensor.
- To detect objects within a specified distance.
- To control a servo motor automatically.
- To understand Arduino programming and sensor interfacing.
- To learn how ultrasonic sensors and servo motors work together.

## 3. Components Required

1. Arduino UNO
2. Ultrasonic Sensor (HC-SR04)
3. Servo Motor (SG90)
4. Jumper Wires
5. Breadboard
6. USB Cable
7. Computer with Arduino IDE

## 4. Pin Connections

### Ultrasonic Sensor (HC-SR04)

| Sensor Pin | Arduino Pin |
|---|---|
| VCC | 5V |
| GND | GND |
| TRIG | Digital Pin 3 |
| ECHO | Digital Pin 2 |

### Servo Motor

| Servo Wire | Arduino Connection |
|---|---|
| Signal (Orange/Yellow) | Digital Pin 4 |
| VCC (Red) | 5V (suitable supply) |
| GND (Brown/Black) | GND |

**Note:** Connect the servo power supply ground and Arduino ground together. A separate regulated 5V supply may be needed if the servo draws more current than the Arduino can safely provide.

## 5. Arduino Code

```cpp
#include <Servo.h> // Includes the servo library

Servo myservo1;

#define echopin 2 // Echo pin
#define trigpin 3 // Trigger pin

long ultra_time;
long dis_cm;
int set_cm = 16;

int Stop = 5;

void setup() {
  Serial.begin(9600);

  myservo1.attach(4);
  myservo1.write(90);

  pinMode(trigpin, OUTPUT);
  pinMode(echopin, INPUT);

  delay(1000);
}

void loop() {
  ultra_read();

  Serial.print("Dis :");
  Serial.println(dis_cm);

  if (dis_cm < set_cm && Stop == 0) {
    Stop = 5;

    myservo1.write(20);
    delay(500);

    myservo1.write(90);
    delay(200);

    myservo1.write(20);
    delay(1000);

    myservo1.write(90);
  }

  if (dis_cm > set_cm) {
    if (Stop > 0) {
      Stop = Stop - 1;
    }

    myservo1.write(90);
  }

  delay(100);
}

void ultra_read() {
  digitalWrite(trigpin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigpin, HIGH);
  delayMicroseconds(10);

  ultra_time = pulseIn(echopin, HIGH);

  dis_cm = ultra_time / 29 / 2;
}
```

## 6. Explanation of the Code

### A. Include Servo Library

```cpp
#include <Servo.h>
```

This library allows Arduino to control a servo motor by setting its angle.

### B. Create Servo Object

```cpp
Servo myservo1;
```

Creates a servo object named `myservo1` to control the motor.

### C. Define Ultrasonic Sensor Pins

```cpp
#define echopin 2
#define trigpin 3
```

- Echo pin receives the reflected ultrasonic signal.
- Trigger pin sends the ultrasonic signal.

### D. Declare Variables

```cpp
long ultra_time;
long dis_cm;
int set_cm = 16;
int Stop = 5;
```

- `ultra_time`: Stores the time taken by the ultrasonic pulse.
- `dis_cm`: Stores the calculated distance in centimeters.
- `set_cm`: Sets the object detection distance to 16 cm.
- `Stop`: Acts as a counter to control when the servo movement can happen again.

### E. Setup Function

```cpp
void setup() {
  Serial.begin(9600);

  myservo1.attach(4);
  myservo1.write(90);

  pinMode(trigpin, OUTPUT);
  pinMode(echopin, INPUT);

  delay(1000);
}
```

The `setup()` function runs only once when Arduino starts.

- `Serial.begin(9600)` starts serial communication.
- `myservo1.attach(4)` connects the servo signal to pin 4.
- `myservo1.write(90)` moves the servo to its initial 90-degree position.
- `pinMode()` configures the ultrasonic sensor pins.
- `delay(1000)` waits for one second.

### F. Loop Function

```cpp
void loop() {
  ultra_read();
}
```

The `loop()` function runs repeatedly. It calls `ultra_read()` to measure the distance.

### G. Display Distance

```cpp
Serial.print("Dis :");
Serial.println(dis_cm);
```

Displays the measured distance in the Serial Monitor.

### H. Object Detection Condition

```cpp
if(dis_cm < set_cm && Stop == 0)
```

Checks two conditions:

1. The measured distance is less than 16 cm.
2. The `Stop` counter is equal to zero.

If both conditions are true, the servo performs its movement sequence.

### I. Servo Motor Movement

```cpp
myservo1.write(20);
delay(500);

myservo1.write(90);
delay(200);

myservo1.write(20);
delay(1000);

myservo1.write(90);
```

The servo moves through these positions:

1. Moves to 20 degrees.
2. Waits 500 milliseconds.
3. Moves to 90 degrees.
4. Waits 200 milliseconds.
5. Moves to 20 degrees again.
6. Waits 1000 milliseconds.
7. Returns to 90 degrees.

### J. Stop Counter

```cpp
if(dis_cm > set_cm) {
  if(Stop > 0) {
    Stop = Stop - 1;
  }

  myservo1.write(90);
}
```

When the object is farther than 16 cm, the counter decreases by one until it reaches zero. The servo returns to 90 degrees.

This counter helps prevent the servo from repeatedly activating while the object remains close.

**Important:** The counter decreases on each loop iteration while the object is farther than 16 cm. Since the loop has a 100 ms delay, it takes approximately half a second to decrease from 5 to 0.

### K. Ultrasonic Reading Function

```cpp
void ultra_read() {
  digitalWrite(trigpin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigpin, HIGH);
  delayMicroseconds(10);

  ultra_time = pulseIn(echopin, HIGH);

  dis_cm = ultra_time / 29 / 2;
}
```

This function measures the distance using the ultrasonic sensor.

- Sends a LOW signal for 2 microseconds.
- Sends a HIGH trigger pulse for 10 microseconds.
- `pulseIn()` measures the duration of the Echo signal.
- Divides the duration by approximately 29 and then by 2 to calculate distance in centimeters.

## 7. Working Principle

1. Arduino starts and initializes the ultrasonic sensor and servo motor.
2. The servo moves to its initial position of 90 degrees.
3. The ultrasonic sensor sends ultrasonic waves.
4. The waves reflect from an object and return to the sensor.
5. Arduino calculates the distance using the Echo pulse duration.
6. If the object is closer than 16 cm and the counter is zero, the servo performs its movement sequence.
7. When the object moves farther than 16 cm, the counter decreases.
8. The process repeats continuously.

## 8. Expected Output

| Object Distance | Servo Motor Behavior |
|---|---|
| Less than 16 cm | Performs the movement sequence when `Stop` is zero |
| Equal to 16 cm | No new movement is triggered |
| Greater than 16 cm | Returns to 90 degrees and decreases the counter |

The measured distance is also displayed in the Serial Monitor at 9600 baud.

## 9. Applications

- Automatic door systems
- Object detection systems
- Smart parking systems
- Robotics projects
- Automatic barriers
- Contactless control systems

## 10. Advantages

- Detects objects without physical contact.
- Provides automatic servo motor control.
- Easy to build and program.
- Useful for beginner-level robotics projects.
- Can be used in automation systems.

## 11. Limitations

- Ultrasonic readings can be affected by object shape and surface.
- Soft materials may absorb sound waves.
- Incorrect sensor positioning can cause inaccurate readings.
- Servo motors have limited rotation angles.
- The detection threshold may require calibration.

## 12. Safety and Improvements

- Use a suitable regulated power supply for the servo.
- Connect all circuit grounds together.
- Avoid forcing the servo beyond its mechanical limits.
- Add a timeout to `pulseIn()` to avoid waiting too long for an Echo signal.
- Handle zero or invalid distance readings before controlling the servo.

For example, replace the original pulse measurement with:

```cpp
ultra_time = pulseIn(echopin, HIGH, 30000);

if (ultra_time == 0) {
  dis_cm = 999; // No valid echo received
} else {
  dis_cm = ultra_time / 29 / 2;
}
```

## 13. Conclusion

The Ultrasonic Sensor-Based Servo Motor Control project demonstrates how an Arduino can measure distance and automatically control a servo motor. The ultrasonic sensor detects nearby objects, while the servo responds according to the programmed distance condition.

This project helps beginners understand sensor interfacing, distance measurement, conditional statements, functions, and servo motor control using Arduino.