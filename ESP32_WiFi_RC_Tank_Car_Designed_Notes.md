# ESP32 WiFi-Based RC Tank Car Using Web Server

## 1. Introduction

An ESP32 WiFi-based RC Tank Car is a wireless robotic vehicle controlled through a web browser.

In this project, ESP32 creates a Wi-Fi access point and hosts a web page. The user connects a mobile phone or computer to this Wi-Fi network and opens the control page.

The web page contains four directional buttons and a speed slider. When the user presses a button, the browser sends a command to ESP32 using WebSocket communication.

ESP32 processes the command and controls two DC motors through a motor driver.

## 2. Objectives

- To create a Wi-Fi access point using ESP32.
- To control a robot car through a web browser.
- To understand HTTP and WebSocket communication.
- To control two DC motors using a motor driver.
- To control motor speed using PWM.
- To design a simple web-based remote-control interface.

## 3. Required Components

1. ESP32 development board
2. Two DC geared motors
3. Motor driver module (such as L298N, if suitable for the motors)
4. Robot tank chassis or car chassis
5. Wheels or tank tracks
6. Battery for motors
7. Jumper wires
8. Mobile phone or laptop
9. USB cable

## 4. Libraries Used

### 4.1 Arduino Library

```cpp
#include <Arduino.h>
```

Provides basic Arduino functions such as `pinMode()`, `digitalWrite()`, and `Serial`.

### 4.2 WiFi Library

```cpp
#include <WiFi.h>
```

Used to create a Wi-Fi access point on ESP32.

### 4.3 AsyncTCP Library

```cpp
#include <AsyncTCP.h>
```

Provides asynchronous TCP communication for ESP32.

### 4.4 ESPAsyncWebServer Library

```cpp
#include <ESPAsyncWebServer.h>
```

Used to create an asynchronous web server and handle browser requests and WebSocket connections.

### 4.5 C++ Libraries

```cpp
#include <iostream>
#include <sstream>
```

These libraries help process incoming text data.

- `iostream`: Provides standard input/output stream features.
- `sstream`: Allows string data to be processed as a stream.

**Note:** This program uses ESP32-specific libraries. The ESP8266 version requires different Wi-Fi and TCP libraries.

## 5. ESP32 Pin Connections

The program uses the following GPIO pins.

### 5.1 Right Motor Connections

| Motor Driver Pin | ESP32 GPIO |
|---|---|
| ENA | GPIO 22 |
| IN1 | GPIO 16 |
| IN2 | GPIO 17 |

### 5.2 Left Motor Connections

| Motor Driver Pin | ESP32 GPIO |
|---|---|
| ENB | GPIO 23 |
| IN3 | GPIO 18 |
| IN4 | GPIO 19 |

### 5.3 Motor Driver Connections

| Motor Driver | Connection |
|---|---|
| Motor A outputs | Right DC motor |
| Motor B outputs | Left DC motor |
| ENA | ESP32 GPIO 22 |
| ENB | ESP32 GPIO 23 |
| IN1, IN2 | ESP32 GPIO 16, 17 |
| IN3, IN4 | ESP32 GPIO 18, 19 |
| Motor power | External battery |
| GND | Common ground with ESP32 |

**Important:** If your L298N module has ENA and ENB jumpers installed, remove them when using ESP32 PWM control on those enable pins. Otherwise, the enable inputs may remain permanently enabled.

## 6. Wi-Fi Configuration

The following variables define the Wi-Fi network name and password.

```cpp
const char* ssid = "MyWiFiCar";
const char* password = "12345678";
```

| Variable | Meaning |
|---|---|
| SSID | Wi-Fi network name |
| Password | Wi-Fi network password |

The ESP32 creates a wireless network named `MyWiFiCar`.

The user connects a mobile phone or laptop to this network.

## 7. Motor Configuration

### 7.1 MOTOR_PINS Structure

```cpp
struct MOTOR_PINS
{
  int pinEn;
  int pinIN1;
  int pinIN2;
};
```

A structure is a user-defined data type in C++.

It stores three pin numbers for each motor:

- `pinEn`: Motor enable pin.
- `pinIN1`: First motor direction pin.
- `pinIN2`: Second motor direction pin.

### 7.2 Motor Pin Array

```cpp
std::vector<MOTOR_PINS> motorPins =
{
  {22, 16, 17},
  {23, 18, 19},
};
```

This creates a collection of motor pin configurations.

- First element: Right motor.
- Second element: Left motor.

`std::vector` is a C++ container that stores a collection of elements.

## 8. Movement Definitions

```cpp
#define UP 1
#define DOWN 2
#define LEFT 3
#define RIGHT 4
#define STOP 0
```

These definitions assign numbers to movement commands.

| Command | Value | Meaning |
|---|---:|---|
| UP | 1 | Move forward |
| DOWN | 2 | Move backward |
| LEFT | 3 | Turn left |
| RIGHT | 4 | Turn right |
| STOP | 0 | Stop the car |

### Motor Definitions

```cpp
#define RIGHT_MOTOR 0
#define LEFT_MOTOR 1

#define FORWARD 1
#define BACKWARD -1
```

These definitions identify the motors and their rotation directions.

## 9. PWM Configuration

```cpp
const int PWMFreq = 1000;
const int PWMResolution = 8;
const int PWMSpeedChannel = 4;
```

PWM stands for Pulse Width Modulation.

It is used to control the speed of DC motors.

| Variable | Meaning |
|---|---|
| PWMFreq | PWM frequency = 1000 Hz |
| PWMResolution | 8-bit resolution |
| PWMSpeedChannel | PWM channel number 4 |

An 8-bit PWM signal has values from 0 to 255.

## 10. Web Server and WebSocket

```cpp
AsyncWebServer server(80);
AsyncWebSocket wsCarInput("/CarInput");
```

### AsyncWebServer

Creates an asynchronous web server on port 80.

Port 80 is the standard HTTP port.

### AsyncWebSocket

Creates a WebSocket endpoint named `/CarInput`.

WebSocket allows continuous, two-way communication between the browser and ESP32.

For example, when the user presses the UP button, the browser sends a command to ESP32.

## 11. HTML, CSS, and JavaScript

The program contains a complete web page inside this declaration:

```cpp
const char* htmlHomePage PROGMEM = R"HTMLHOMEPAGE(
...
)HTMLHOMEPAGE";
```

This is a raw string containing HTML, CSS, and JavaScript.

`PROGMEM` stores the constant page content in program memory on supported Arduino architectures.

### 11.1 HTML

HTML stands for HyperText Markup Language.

It defines the structure of the web page.

The page contains:

- Project heading.
- Directional control buttons.
- Speed slider.

Example:

```html
<h1>Hash Include Electronics</h1>
<h2>WiFi Tank Control</h2>
```

These headings display the project title.

### 11.2 CSS

CSS stands for Cascading Style Sheets.

It controls the appearance of the web page.

Example:

```css
.arrows {
  font-size: 40px;
  color: red;
}
```

This makes the arrow symbols large and red.

```css
td.button {
  background-color: black;
  border-radius: 25%;
  box-shadow: 5px 5px #888888;
}
```

This styles the directional buttons.

### 11.3 Directional Buttons

Example:

```html
<td class="button"
    ontouchstart='sendButtonInput("MoveCar","1")'
    ontouchend='sendButtonInput("MoveCar","0")'>
  <span>&#8679;</span>
</td>
```

When the user touches the UP button:

- `ontouchstart`: Sends the forward command.
- `ontouchend`: Sends the stop command.

The other buttons work similarly.

### 11.4 Speed Slider

```html
<input type="range"
       min="0"
       max="255"
       value="150"
       id="Speed"
       oninput='sendButtonInput("Speed",value)'>
```

This creates a slider to control motor speed.

| Slider Value | Meaning |
|---|---|
| 0 | Motor power off |
| 100 | Low speed command |
| 150 | Medium speed command |
| 255 | Maximum speed command |

Actual motor speed depends on the motor, battery, load, and driver.

### 11.5 JavaScript WebSocket Connection

```javascript
var webSocketCarInputUrl =
  "ws://" + window.location.hostname + "/CarInput";
```

This creates the WebSocket URL using the ESP32's current IP address.

```javascript
websocketCarInput = new WebSocket(webSocketCarInputUrl);
```

This establishes a WebSocket connection between the browser and ESP32.

### 11.6 Send Button Input

```javascript
function sendButtonInput(key, value)
{
  var data = key + "," + value;
  websocketCarInput.send(data);
}
```

This function sends commands from the web page to ESP32.

Examples:

```text
MoveCar,1
MoveCar,2
MoveCar,0
Speed,150
```

The first part identifies the command, and the second part contains its value.

## 12. Explanation of Main Functions

### 12.1 rotateMotor()

```cpp
void rotateMotor(int motorNumber, int motorDirection)
```

This function controls the direction of an individual motor.

**Forward direction:**

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, HIGH);
digitalWrite(motorPins[motorNumber].pinIN2, LOW);
```

**Backward direction:**

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, LOW);
digitalWrite(motorPins[motorNumber].pinIN2, HIGH);
```

**Stop direction:**

```cpp
digitalWrite(motorPins[motorNumber].pinIN1, LOW);
digitalWrite(motorPins[motorNumber].pinIN2, LOW);
```

### 12.2 moveCar()

```cpp
void moveCar(int inputValue)
```

This function controls the complete movement of the car.

It uses a `switch` statement to identify the received command.

**Forward:**

```cpp
case UP:
  rotateMotor(RIGHT_MOTOR, FORWARD);
  rotateMotor(LEFT_MOTOR, FORWARD);
  break;
```

Both motors rotate forward.

**Backward:**

```cpp
case DOWN:
  rotateMotor(RIGHT_MOTOR, BACKWARD);
  rotateMotor(LEFT_MOTOR, BACKWARD);
  break;
```

Both motors rotate backward.

**Left:**

```cpp
case LEFT:
  rotateMotor(RIGHT_MOTOR, FORWARD);
  rotateMotor(LEFT_MOTOR, BACKWARD);
  break;
```

The right motor moves forward and the left motor moves backward, causing the vehicle to turn left in place.

**Right:**

```cpp
case RIGHT:
  rotateMotor(RIGHT_MOTOR, BACKWARD);
  rotateMotor(LEFT_MOTOR, FORWARD);
  break;
```

The motors rotate in opposite directions, causing the vehicle to turn right in place.

**Stop:**

```cpp
case STOP:
  rotateMotor(RIGHT_MOTOR, STOP);
  rotateMotor(LEFT_MOTOR, STOP);
  break;
```

Both motors stop.

### 12.3 handleRoot()

```cpp
void handleRoot(AsyncWebServerRequest *request)
{
  request->send_P(200, "text/html", htmlHomePage);
}
```

This function sends the HTML home page to the browser.

- `200`: HTTP success status.
- `text/html`: Response content type.
- `htmlHomePage`: Web page content.

### 12.4 handleNotFound()

```cpp
void handleNotFound(AsyncWebServerRequest *request)
{
  request->send(404, "text/plain", "File Not Found");
}
```

This function handles requests for pages that do not exist.

HTTP status code 404 means the requested resource was not found.

### 12.5 onCarInputWebSocketEvent()

```cpp
void onCarInputWebSocketEvent(
  AsyncWebServer *server,
  AsyncWebSocketClient *client,
  AwsEventType type,
  void *arg,
  uint8_t *data,
  size_t len
)
```

This is one of the most important functions in the project.

It handles WebSocket events.

| Event | Meaning |
|---|---|
| WS_EVT_CONNECT | Browser connects |
| WS_EVT_DISCONNECT | Browser disconnects |
| WS_EVT_DATA | Browser sends data |
| WS_EVT_PONG | Receives a WebSocket pong |
| WS_EVT_ERROR | WebSocket error |

When a browser disconnects:

```cpp
moveCar(STOP);
```

The car is commanded to stop.

When data arrives, the function separates the command and value.

Example:

```text
MoveCar,1
```

It separates this into:

- Key = `MoveCar`
- Value = `1`

Then it calls:

```cpp
moveCar(valueInt);
```

For speed commands, it uses:

```cpp
ledcWrite(PWMSpeedChannel, valueInt);
```

This changes the PWM duty cycle.

### 12.6 setUpPinModes()

```cpp
void setUpPinModes()
```

This function configures motor pins and PWM.

```cpp
ledcSetup(PWMSpeedChannel, PWMFreq, PWMResolution);
```

Sets up the PWM channel.

```cpp
ledcAttachPin(motorPins[i].pinEn, PWMSpeedChannel);
```

Connects the motor enable pins to the PWM channel.

The `for` loop initializes both motors.

Finally:

```cpp
moveCar(STOP);
```

Stops the motors during initialization.

### 12.7 setup()

```cpp
void setup()
```

This function executes once when ESP32 starts.

It:

1. Initializes motor pins.
2. Starts serial communication.
3. Creates the Wi-Fi access point.
4. Displays the IP address.
5. Registers the home page route.
6. Registers the WebSocket event handler.
7. Starts the web server.

### 12.8 loop()

```cpp
void loop()
{
  wsCarInput.cleanupClients();
}
```

This function runs continuously.

`cleanupClients()` removes inactive WebSocket clients and helps manage client connections.

## 13. Working Principle

1. ESP32 starts and initializes the motor pins.
2. ESP32 creates a Wi-Fi access point named `MyWiFiCar`.
3. A user connects a phone or laptop to that Wi-Fi network.
4. The user opens the ESP32 IP address in a web browser.
5. The ESP32 sends the HTML control page to the browser.
6. The browser establishes a WebSocket connection.
7. The user presses a directional button.
8. JavaScript sends a movement command to ESP32.
9. ESP32 receives and processes the command.
10. The motor driver controls both motors.
11. The speed slider sends PWM speed commands.
12. When the movement button is released, the browser sends STOP.

## 14. Block Diagram

```text
       Mobile Phone / Laptop
                 |
                 |
              Wi-Fi
                 |
                 v
             ESP32
          /          \
         /            \
        v              v
   Web Server       WebSocket
         \            /
          \          /
           v        v
         Motor Control
                |
                v
           Motor Driver
             /     \
            v       v
       Right Motor  Left Motor
             \     /
              \   /
               v
            RC Tank
```

## 15. Movement Control Table

| Button | Command | Right Motor | Left Motor |
|---|---|---|---|
| UP | 1 | Forward | Forward |
| DOWN | 2 | Backward | Backward |
| LEFT | 3 | Forward | Backward |
| RIGHT | 4 | Backward | Forward |
| STOP | 0 | Stop | Stop |

The actual turning direction depends on motor mounting and wiring.

## 16. How to Run the Project

1. Install the ESP32 board package in Arduino IDE.
2. Install compatible AsyncTCP and ESPAsyncWebServer libraries.
3. Connect the ESP32 to the motor driver.
4. Connect the motor driver to the motors and external battery.
5. Upload the program to ESP32.
6. Open the Serial Monitor at 115200 baud.
7. Find the displayed access point IP address.
8. Open Wi-Fi settings on your phone.
9. Connect to `MyWiFiCar`.
10. Enter the password `12345678`.
11. Open a browser and enter the displayed IP address.
12. Use the directional buttons and speed slider to control the car.

The typical ESP32 soft access point address is `192.168.4.1`, but use the address printed by your board.

## 17. Troubleshooting

| Problem | Possible Reason | Solution |
|---|---|---|
| Wi-Fi network not visible | ESP32 access point not started | Check the board and power supply |
| Web page not opening | Wrong IP address | Check Serial Monitor |
| Buttons do not work | WebSocket not connected | Refresh the page and reconnect |
| Motors do not move | Motor driver or battery problem | Check motor wiring and power |
| Speed slider does not work | PWM configuration issue | Check PWM API and enable pins |
| Car moves in the wrong direction | Motor polarity or wiring issue | Reverse the motor connections or logic |
| ESP32 restarts | Power supply is insufficient | Use a suitable regulated supply |

## 18. Important Technical Notes and Corrections

### 18.1 ESP32 PWM API Compatibility

Your code uses the older ESP32 Arduino core 2.x functions:

```cpp
ledcSetup();
ledcAttachPin();
ledcWrite(channel, duty);
```

ESP32 Arduino core 3.x uses a different LEDC API.

For example, it uses `ledcAttach()` and pin-based `ledcWrite()`.

Update the PWM code if you are using core 3.x.

### 18.2 Shared PWM Channel

Your program attaches both motor enable pins to the same PWM channel:

```cpp
const int PWMSpeedChannel = 4;
```

This means both motors receive the same speed command. That is appropriate for this simple tank-control project.

### 18.3 WebSocket Safety

The original program stops the motors when the browser disconnects. However, if the browser remains connected but a movement button release event is missed, the motors could continue moving.

For safer operation, add a command timeout or heartbeat mechanism so the ESP32 automatically stops the motors if fresh control commands are not received for a short period.

### 18.4 Motor Driver and Power Supply

- Never connect motors directly to ESP32 GPIO pins.
- Use a motor driver rated for the motors.
- Use a separate suitable motor supply.
- Connect ESP32 and motor-driver grounds together.
- Check the motor driver's current and voltage ratings.

## 19. Applications

1. Wi-Fi-controlled robot cars.
2. Tank robots.
3. Wireless robotics projects.
4. Educational IoT projects.
5. Remote vehicle prototypes.
6. Smart automation systems.
7. Web-based motor control.

## 20. Viva Questions and Answers

**Q1. What is ESP32?**

ESP32 is a microcontroller with built-in Wi-Fi and Bluetooth capabilities.

**Q2. What is an RC car?**

An RC car is a remote-controlled vehicle operated wirelessly.

**Q3. What is a Wi-Fi access point?**

A Wi-Fi access point allows other devices to connect to a wireless network.

**Q4. What is SSID?**

SSID stands for Service Set Identifier. It is the name of a Wi-Fi network.

**Q5. What is a web server?**

A web server receives HTTP requests and sends web pages or other responses to clients.

**Q6. What is WebSocket?**

WebSocket is a communication protocol that supports two-way communication between a browser and a server.

**Q7. What is the use of AsyncTCP?**

It provides asynchronous TCP communication for ESP32.

**Q8. What is ESPAsyncWebServer?**

It is a library used to create an asynchronous web server on ESP32.

**Q9. What is PWM?**

PWM stands for Pulse Width Modulation. It is used to control DC motor speed.

**Q10. What is the purpose of a motor driver?**

A motor driver controls the direction and speed of DC motors.

**Q11. What is the purpose of `moveCar()`?**

It controls the movement of the robot car based on the received command.

**Q12. What is the purpose of `rotateMotor()`?**

It controls the direction of an individual motor.

**Q13. What is the purpose of `handleRoot()`?**

It sends the HTML home page to the browser.

**Q14. What is the purpose of `cleanupClients()`?**

It cleans up inactive WebSocket clients.

**Q15. What happens when the browser disconnects?**

The WebSocket disconnect event commands the car to stop.

## 21. Conclusion

In this project, we created a Wi-Fi-controlled robot car using ESP32. The ESP32 creates its own wireless network and hosts a web page that allows the user to control the vehicle through a mobile phone or computer.

The directional buttons control motor movement, while the speed slider controls motor speed using PWM.

This project demonstrates Wi-Fi communication, web development, WebSocket communication, motor control, and embedded-system programming.

---

**Project Name:** ESP32 WiFi-Based RC Tank Car

**Microcontroller:** ESP32

**Communication:** Wi-Fi

**Web Technologies:** HTML, CSS, JavaScript

**Server:** ESPAsyncWebServer

**Communication Protocol:** WebSocket

**Motor Control:** PWM

**Programming Language:** C/C++ (Arduino)

**Output:** Wireless robot car controlled through a web browser.