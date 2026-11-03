1. Introduction
A motion detection system detects movement in a specific area. A common sensor used in beginner Arduino projects is the PIR (Passive Infrared) motion sensor.

A PIR sensor detects changes in infrared radiation in its field of view. When a person or warm object moves across the sensor's detection area, the sensor output may become HIGH.

In this program:
- The motion sensor is connected to digital pin 2.
- An LED is connected to digital pin 13.
- Arduino reads the sensor's digital output.
- The LED turns ON when motion is detected and OFF when motion is no longer detected.
- Messages are printed in the Serial Monitor when the motion state changes.
Note: This explanation assumes the sensor is a PIR module with a digital output. Check the model and its datasheet for its exact behavior and wiring.

2. Objectives
- Understand the working principle of a PIR motion sensor.
- Learn how to connect a motion sensor to Arduino.
- Understand digital input and digital output.
- Learn how to use digitalRead() and digitalWrite().
- Control an LED according to motion detection.
- Display motion status using the Serial Monitor.
3. Required Components
S.N.	Component	Quantity	Purpose
1	Arduino Uno or compatible board	1	Main controller
2	PIR motion sensor module	1	Detects movement
3	LED	1	Visual indication
4	220Ω–330Ω resistor	1	Limits LED current
5	Jumper wires	As needed	Connections
6	Breadboard	1 (optional)	Makes connections easier
7	USB cable	1	Programming and power


4. PIR Motion Sensor Overview
PIR stands for Passive Infrared. It detects changes in infrared energy from objects in its field of view. It does not send out infrared signals to detect movement; it senses infrared radiation.

Many PIR modules have three main pins:
PIR sensor pin	Arduino connection	Function
VCC	Module-rated supply (often 5V)	Power
OUT	Digital pin 2	Digital motion signal
GND	GND	Ground


LED connection
LED part	Arduino connection
Anode (long leg)	Digital pin 13 through a resistor
Cathode (short leg)	GND


Note: Some Arduino boards have a built-in LED on pin 13. If using the built-in LED, an external LED may not be required. Check the PIR module's voltage requirements and pin labels before wiring.

5. Important Concepts
5.1 Digital Input
A digital input reads one of two logic states:
- HIGH — logic HIGH.
- LOW — logic LOW.
In this program, digital pin 2 receives the output signal from the PIR sensor.
5.2 Digital Output
A digital output sends a HIGH or LOW signal to control a device.
digitalWrite(led, HIGH);
Turns the LED ON.
digitalWrite(led, LOW);
Turns the LED OFF.
5.3 digitalRead()
The digitalRead() function reads the logic state of a digital input pin.
val = digitalRead(sensor);
The result is stored in the variable val.
5.4 if-else Statement
The if-else statement makes a decision based on a condition.
if (val == HIGH)
{
  // Motion signal is HIGH
}
else
{
  // Motion signal is LOW
}
5.5 State Variable
The variable state stores the previous motion status.
- LOW means the program's previous state was no motion detected.
- HIGH means the program's previous state was motion detected.
It helps print a message only when the motion status changes, rather than printing the same message repeatedly during every loop.
6. Complete Arduino Program
The following is the supplied program, formatted for readability.
int led = 13;       // LED is connected to pin 13
int sensor = 2;     // PIR sensor output is connected to pin 2
int state = LOW;    // Previous motion state: no motion
int val = 0;        // Stores the sensor reading

void setup()
{
  pinMode(led, OUTPUT);       // Set LED pin as output
  pinMode(sensor, INPUT);     // Set sensor pin as input
  Serial.begin(9600);         // Start serial communication
}

void loop()
{
  val = digitalRead(sensor);  // Read the sensor output

  if (val == HIGH)
  {
    digitalWrite(led, HIGH);  // Turn LED ON
    delay(100);               // Wait 100 milliseconds

    if (state == LOW)
    {
      Serial.println("Motion detected!");
      state = HIGH;           // Update motion state
    }
  }
  else
  {
    digitalWrite(led, LOW);   // Turn LED OFF
    delay(200);               // Wait 200 milliseconds

    if (state == HIGH)
    {
      Serial.println("Motion stopped!");
      state = LOW;            // Update motion state
    }
  }
}
7. Program Explanation
7.1 Declare the LED Pin
int led = 13;
- int declares an integer variable.
- led is the variable name.
- 13 is the Arduino digital pin connected to the LED.
7.2 Declare the Sensor Pin
int sensor = 2;
- sensor stores the pin number used by the motion sensor.
- Digital pin 2 receives the PIR module's output signal.
7.3 Declare the State Variable
int state = LOW;
- Stores the previous motion status.
- Initially set to LOW, meaning the program assumes no motion has been detected yet.
- It is used to detect a change in status.
7.4 Declare the Sensor Value Variable
int val = 0;
Stores the latest digital reading from the sensor.
7.5 The setup() Function
void setup()
{
  pinMode(led, OUTPUT);
  pinMode(sensor, INPUT);
  Serial.begin(9600);
}
The setup() function runs once when Arduino starts or resets.
Statement	Explanation
pinMode(led, OUTPUT);	Configures pin 13 as an output for the LED.
pinMode(sensor, INPUT);	Configures pin 2 as an input from the PIR sensor.
Serial.begin(9600);	Starts serial communication at 9600 baud.


7.6 The loop() Function
void loop()
{
  val = digitalRead(sensor);
}
The loop() function repeats continuously while Arduino is powered.

digitalRead(sensor) reads the sensor output and stores the result in val.
7.7 Check Whether Motion Is Detected
if (val == HIGH)
{
  digitalWrite(led, HIGH);
  delay(100);
}
When the sensor output is HIGH:
1. Arduino identifies a HIGH signal.
2. The LED is turned ON.
3. The program waits for 100 milliseconds.
4. It checks whether the previous state was LOW.
7.8 Print the Motion Detected Message
if (state == LOW)
{
  Serial.println("Motion detected!");
  state = HIGH;
}
This inner condition checks whether the previous state was LOW.

If it was LOW:
- Arduino prints Motion detected!.
- The state changes to HIGH.
As a result, the message is printed once when the program changes from no motion to motion detected.
7.9 When Motion Is Not Detected
else
{
  digitalWrite(led, LOW);
  delay(200);
}
When the sensor output is LOW:
1. Arduino identifies a LOW signal.
2. The LED is turned OFF.
3. The program waits for 200 milliseconds.
4. It checks whether the previous state was HIGH.
7.10 Print the Motion Stopped Message
if (state == HIGH)
{
  Serial.println("Motion stopped!");
  state = LOW;
}
If the previous state was HIGH:
- Arduino prints Motion stopped!.
- The state changes to LOW.
The message is printed once when the program changes from motion detected to no motion detected.
8. Understanding the Output
Open the Arduino IDE Serial Monitor and select 9600 baud.
Example output
Motion detected!
Motion stopped!
Motion detected!
Motion stopped!
These messages appear when the sensor output changes between HIGH and LOW.
LED behavior
PIR sensor output	LED status	Serial message
HIGH after LOW	ON	Motion detected!
Remains HIGH	ON	No repeated message
LOW after HIGH	OFF	Motion stopped!
Remains LOW	OFF	No repeated message


Note: A PIR module may keep its output HIGH for a period after detecting movement. Its delay and sensitivity settings can affect when the output returns to LOW.

9. System Working Process
9.1 Step-by-Step Working
1. Arduino starts.
2. Pin 13 is configured as an output.
3. Pin 2 is configured as an input.
4. Serial communication starts at 9600 baud.
5. Arduino reads the PIR sensor output using digitalRead().
6. If the output is HIGH, Arduino turns ON the LED.
7. If the previous state was LOW, Arduino prints Motion detected! and updates the state to HIGH.
8. If the sensor output is LOW, Arduino turns OFF the LED.
9. If the previous state was HIGH, Arduino prints Motion stopped! and updates the state to LOW.
10. The loop repeats continuously.
9.2 Block Diagram
        +----------------------+
        |   PIR Motion Sensor  |
        +----------------------+
                   |
                   | Digital Output
                   |
                   v
        +----------------------+
        |      Arduino         |
        |                      |
        | Read Digital Signal  |
        | Check Motion State   |
        +----------------------+
             |           |
             |           |
             v           v
    +-------------+  +----------------+
    |     LED     |  | Serial Monitor |
    | ON / OFF    |  | Status Message |
    +-------------+  +----------------+
9.3 Flowchart
        +----------------+
        |     START      |
        +----------------+
                |
                v
        +----------------+
        | Initialize LED |
        | and Sensor     |
        +----------------+
                |
                v
        +----------------+
        | Read Sensor    |
        | Using          |
        | digitalRead()  |
        +----------------+
                |
                v
        +----------------+
        | Is val HIGH?   |
        +----------------+
           /        \
         YES         NO
          |           |
          v           v
   +-------------+ +-------------+
   | Turn LED ON | | Turn LED OFF|
   +-------------+ +-------------+
          |           |
          v           v
   +-------------+ +-------------+
   | Was state   | | Was state   |
   | LOW?        | | HIGH?       |
   +-------------+ +-------------+
          |           |
          v           v
   +-------------+ +-------------+
   | Print       | | Print       |
   | Motion      | | Motion      |
   | detected!   | | stopped!    |
   +-------------+ +-------------+
          |           |
          v           v
   +-------------+ +-------------+
   | Set state   | | Set state   |
   | HIGH        | | LOW         |
   +-------------+ +-------------+
          \           /
           \         /
            v       v
        +----------------+
        | Repeat Loop    |
        +----------------+
10. Applications
1. Security systems: Detects movement in a monitored area.
2. Automatic lighting: Turns lights ON when movement is detected.
3. Smart home systems: Provides motion input for home automation.
4. Energy saving: Helps control lights based on room occupancy.
5. Automatic doors: Can be part of a motion-triggered door system.
6. Robotics: Provides basic movement detection input for a project.
7. Learning projects: Demonstrates digital input, output, and conditional statements.
11. Common Errors and Precautions
Problem	Possible solution
LED does not turn ON	Check the LED polarity, resistor, pin 13, and sensor OUT connection.
Sensor always reads HIGH	Allow the PIR module to stabilize and check its wiring and settings.
Sensor does not detect movement	Check its supply, detection range, orientation, and warm-up instructions.
Serial Monitor shows no message	Select the correct port and set baud rate to 9600.
Messages appear repeatedly	Check the state variable and the sensor output behavior.
Unexpected detection	Adjust sensitivity and delay settings if the module supports them.


Important Precautions
- Check the PIR module's supply-voltage requirements.
- Connect Arduino GND and sensor GND together.
- Allow the sensor to warm up and stabilize after powering it.
- Avoid placing the sensor where unwanted heat changes or movement can cause false triggers.
- Use a resistor with an external LED.
- A basic PIR project is not a complete or certified security system.
12. Viva Questions and Answers
Q1. What is a motion sensor?

A motion sensor detects movement in a particular area.

Q2. What is the full form of PIR?

PIR stands for Passive Infrared.

Q3. Which pin is used for the LED in this program?

Digital pin 13.

Q4. Which pin is used for the motion sensor?

Digital pin 2.

Q5. What does digitalRead() do?

It reads the HIGH or LOW state of a digital input pin.

Q6. What does digitalWrite() do?

It sets a digital output pin to HIGH or LOW.

Q7. What is the purpose of pinMode()?

It configures a pin as INPUT or OUTPUT.

Q8. What is the purpose of Serial.begin(9600)?

It starts serial communication at 9600 baud.

Q9. What does HIGH mean in this program?

It means the PIR sensor output is HIGH, which the program treats as motion detected.

Q10. What does LOW mean in this program?

It means the PIR sensor output is LOW, which the program treats as no motion detected.

Q11. Why is the state variable used?

It remembers the previous motion status so that a message is printed only when the status changes.

Q12. What does delay(100) do?

It pauses the program for 100 milliseconds.

Q13. What does delay(200) do?

It pauses the program for 200 milliseconds.

Q14. What happens when motion is detected?

The LED turns ON, and the message Motion detected! is printed when the state changes from LOW to HIGH.

Q15. What happens when motion stops?

The LED turns OFF, and the message Motion stopped! is printed when the state changes from HIGH to LOW.
13. Conclusion
This Arduino project demonstrates how to build a basic motion detection system using a PIR sensor. The sensor is connected to digital pin 2, and the LED is connected to pin 13. Arduino reads the sensor signal using digitalRead() and controls the LED using digitalWrite(). The state variable remembers the previous motion status, allowing the program to print a message only when motion starts or stops.
📌 Document Information
Field	Details
Document title	Motion Detection Sensor with Arduino
File format	Markdown (.md)
Programming language	Arduino C/C++
Sensor type	PIR motion sensor (assumed)
Sensor output pin	Digital pin 2
LED pin	Digital pin 13
Serial baud rate	9600
Topic	Embedded Systems / Arduino / Sensors


End of Document