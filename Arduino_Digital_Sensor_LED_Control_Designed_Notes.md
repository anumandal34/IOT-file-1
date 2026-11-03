1. Introduction
This Arduino program reads a digital signal from a sensor connected to digital pin 7. When the sensor output is HIGH, the LED connected to pin 13 turns ON. When the sensor output is LOW, the LED turns OFF.

The sensor type is not specified in the code. Therefore, these notes describe it as a digital-output sensor. It could be a motion sensor, obstacle sensor, or another module that provides HIGH/LOW output.
2. Objectives
- Understand how Arduino reads a digital sensor.
- Learn the use of digitalRead() and digitalWrite().
- Control an LED according to a sensor's output.
- Display the sensor value in the Serial Monitor.
3. Required Components
Component	Quantity	Purpose
Arduino Uno (or compatible board)	1	Runs the program
Digital-output sensor module	1	Sends HIGH or LOW signal
LED	1	Shows the sensor state
220 Ω–330 Ω resistor	1	Limits LED current if using an external LED
Breadboard	1	Makes connections easier
Jumper wires	As needed	Connect components
USB cable	1	Uploads code and powers the board


Note: Arduino Uno's built-in LED is connected to pin 13 on many boards. If using the built-in LED, an external LED and resistor are not required.
4. Pin Connections
Arduino pin	Connect to
Digital pin 7	Sensor digital output (OUT)
5V or suitable supply	Sensor VCC, according to its specification
GND	Sensor GND
Digital pin 13	LED (built-in LED or external LED circuit)


For an external LED, connect pin 13 to the LED's anode through a 220 Ω–330 Ω resistor, and connect the cathode to GND. Check the sensor module's voltage requirements before connecting it.
5. Important Concepts
5.1 pinMode()
Sets whether a pin is used as an input or output.
pinMode(ledPin, OUTPUT);
pinMode(sensorPin, INPUT);
- OUTPUT: Arduino sends a signal to control the LED.
- INPUT: Arduino reads the signal from the sensor.
5.2 digitalRead()
Reads the digital state of a pin.
val = digitalRead(sensorPin);
It returns one of two states:
- HIGH: A HIGH logic signal is detected.
- LOW: A LOW logic signal is detected.
5.3 digitalWrite()
Sets a digital output pin to HIGH or LOW.
digitalWrite(ledPin, HIGH); // LED ON
digitalWrite(ledPin, LOW);  // LED OFF
5.4 Boolean variable
boolean val = 0;
A boolean variable stores either true or false. In Arduino code, 0 represents false and 1 represents true. The variable val stores the sensor's digital reading.
5.5 Serial communication
Serial.begin(9600);
Serial.println(val);
Serial.begin(9600) starts serial communication at 9600 baud. Serial.println(val) prints the sensor reading in the Serial Monitor, usually as 0 or 1.
6. Complete Arduino Code
int ledPin = 13;
int sensorPin = 7;
boolean val = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(sensorPin, INPUT);
  Serial.begin(9600);
}

void loop() {
  val = digitalRead(sensorPin);
  Serial.println(val);

  // When the sensor output is HIGH, the LED turns ON.
  if (val == HIGH) {
    digitalWrite(ledPin, HIGH);
  }
  else {
    digitalWrite(ledPin, LOW);
  }
}
This is the supplied program, formatted for readability. The comment has been clarified: the code checks for a digital HIGH signal; it does not itself measure an analog threshold.
7. Line-by-Line Explanation
Code	Explanation
int ledPin = 13;	Stores the LED pin number.
int sensorPin = 7;	Stores the sensor input pin number.
boolean val = 0;	Creates a Boolean variable for the sensor reading.
void setup()	Runs once when the Arduino starts or resets.
pinMode(ledPin, OUTPUT);	Sets pin 13 as an output.
pinMode(sensorPin, INPUT);	Sets pin 7 as an input.
Serial.begin(9600);	Starts serial communication.
void loop()	Repeats continuously.
digitalRead(sensorPin)	Reads the sensor's digital output.
Serial.println(val);	Prints the reading in the Serial Monitor.
if (val == HIGH)	Checks whether the sensor reading is HIGH.
digitalWrite(ledPin, HIGH);	Turns the LED ON.
else	Runs when the reading is not HIGH (LOW).
digitalWrite(ledPin, LOW);	Turns the LED OFF.


8. Working Process
1. Arduino starts and runs setup().
2. Pin 13 is configured as an output.
3. Pin 7 is configured as an input.
4. Serial communication starts at 9600 baud.
5. The loop() function reads the sensor output.
6. The reading is printed in the Serial Monitor.
7. If the reading is HIGH, the LED turns ON.
8. If the reading is LOW, the LED turns OFF.
9. These steps repeat continuously.
9. Logic Table
Sensor output	val	LED state
HIGH	1	ON
LOW	0	OFF


10. Block Diagram
      Digital Sensor
             |
             | OUT
             v
     Arduino Digital Pin 7
             |
       Read HIGH / LOW
             |
       +-----+-----+
       |           |
    HIGH          LOW
       |           |
       v           v
  LED ON        LED OFF
  Pin 13        Pin 13
       |
       v
  Serial Monitor
 (prints 1 or 0)
11. Program Flowchart
        START
          |
          v
  Set LED pin as OUTPUT
  Set sensor pin as INPUT
  Start Serial at 9600
          |
          v
  Read sensor on pin 7
          |
          v
  Print sensor value
          |
          v
    Is value HIGH?
       /      \
     YES       NO
      |         |
      v         v
  Turn LED   Turn LED
    ON         OFF
      \         /
       \       /
          v
   Repeat loop()
12. Expected Serial Monitor Output
When the sensor output is LOW, the Serial Monitor displays:
0
0
0
0
When the sensor output becomes HIGH, it displays:
1
1
1
1
The exact sequence depends on the sensor and its environment. Open the Arduino IDE Serial Monitor and select 9600 baud to view the output.
13. Applications
- Motion indication when used with a digital-output motion sensor.
- Obstacle detection when used with a digital obstacle sensor.
- Object-presence indication.
- Simple automatic LED control.
- Learning digital input and output in Arduino projects.
The actual application depends on the sensor module connected to pin 7.
14. Common Errors and Troubleshooting
Problem	Possible reason	Solution
LED never turns ON	Sensor output never becomes HIGH	Check sensor operation and wiring.
LED stays ON	Sensor output remains HIGH	Check sensor settings and environment.
Serial Monitor shows unexpected values	Wrong baud rate	Select 9600 baud.
Sensor reading changes randomly	Input may be floating or wiring is loose	Check the module's output and connections.
Arduino does not detect the sensor	Incorrect pin connection or power	Verify OUT, VCC, and GND connections.
External LED does not glow	LED polarity or wiring is incorrect	Check anode/cathode and resistor connection.


Input note: If the sensor has an open-collector/open-drain output or requires a pull-up, follow its datasheet. In some circuits, INPUT_PULLUP may be appropriate, but the logic can then be inverted. Do not change INPUT to INPUT_PULLUP without checking the sensor's output circuit.
15. Precautions
- Connect sensor VCC and GND correctly.
- Check the sensor's operating voltage before connecting it to Arduino.
- Use a current-limiting resistor with an external LED.
- Do not connect an LED directly across a power supply without suitable current limiting.
- Keep wires secure and avoid short circuits.
- Remember that HIGH and LOW behavior depends on the sensor module's output design.
16. Viva Questions and Answers
Q1. What is the purpose of this program?
It reads a digital sensor signal and controls an LED based on that signal.

Q2. Which pin is used for the LED?
Digital pin 13.

Q3. Which pin is used for the sensor?
Digital pin 7.

Q4. What is pinMode() used for?
It configures a pin as an input or output.

Q5. What is digitalRead()?
It reads the HIGH or LOW state of a digital input pin.

Q6. What is digitalWrite()?
It sets a digital output pin to HIGH or LOW.

Q7. What does HIGH mean in this program?
The sensor input is at a HIGH logic level, so the LED turns ON.

Q8. What does LOW mean in this program?
The sensor input is at a LOW logic level, so the LED turns OFF.

Q9. Why is Serial.begin(9600) used?
It starts serial communication at 9600 baud.

Q10. Why is Serial.println(val) used?
It displays the sensor reading in the Serial Monitor.

Q11. What is a Boolean variable?
A variable that stores either true or false.

Q12. How many times does loop() execute?
It repeats continuously while the Arduino is running.

Q13. What is the difference between HIGH and LOW?
They represent the two digital logic states.

Q14. Does this code read an analog value?
No. It uses digitalRead(), which reads a digital HIGH/LOW signal.

Q15. Does this code use a threshold value?
No. It checks whether the digital input is HIGH or LOW. No numeric threshold is defined.
17. Conclusion
This Arduino project demonstrates basic digital sensor interfacing. Arduino reads the sensor's output from pin 7 using digitalRead(). If the output is HIGH, it turns ON the LED on pin 13; otherwise, it turns the LED OFF. The program also prints the sensor state in the Serial Monitor. It is a useful beginner exercise for understanding digital inputs, digital outputs, conditional statements, and serial communication.
Document Information
Item	Details
Document title	Arduino Digital Sensor with LED Control
Programming language	Arduino C/C++
Board example	Arduino Uno
Sensor connection	Digital pin 7
LED connection	Digital pin 13
Serial baud rate	9600
File format	Markdown (.md)