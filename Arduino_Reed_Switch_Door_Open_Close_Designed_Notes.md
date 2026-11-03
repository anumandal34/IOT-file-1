1. Introduction
This Arduino project uses a reed switch to detect whether a door is open or closed. Two LEDs show the door status:
- The Open LED is connected to digital pin 8.
- The Closed LED is connected to digital pin 10.
- The reed switch is connected to digital pin 6.
When the reed switch input reads HIGH, the program turns ON the Closed LED and prints "Your Door is Closed". Otherwise, it turns ON the Open LED and prints "Your Door is Open".

Important: This interpretation assumes the reed switch and its wiring produce HIGH when the door is closed and LOW when it is open. The actual logic depends on how the switch is wired.
2. Objectives
- Understand the working principle of a reed switch.
- Detect a door's open and closed states.
- Control two LEDs using Arduino digital outputs.
- Display door status in the Serial Monitor.
- Learn digitalRead(), digitalWrite(), and if-else.
3. Required Components
Component	Quantity	Purpose
Arduino Uno or compatible board	1	Controls the project
Magnetic reed switch	1	Detects door position
Magnet	1	Activates the reed switch
LED for Open status	1	Indicates an open door
LED for Closed status	1	Indicates a closed door
220 Ω–330 Ω resistors	2	Limit LED current
Breadboard	1	Makes connections easier
Jumper wires	As needed	Connect components
USB cable	1	Uploads code and powers Arduino


4. Pin Connections
Component	Arduino pin
Open LED	Digital pin 8
Closed LED	Digital pin 10
Reed switch signal	Digital pin 6
Component ground	GND


Basic connection notes
- Connect each external LED's anode to its Arduino output pin through a 220 Ω–330 Ω resistor.
- Connect each LED's cathode to GND.
- Connect one side of the reed switch to the input circuit and the other side as required by the chosen wiring arrangement.
- The supplied sketch configures pin 6 as INPUT. It therefore requires the input to be driven to a defined HIGH or LOW level; a floating input can cause unstable readings.
Recommended simple wiring: For a reed switch that connects the input to GND when the magnet is nearby, connect one switch terminal to digital pin 6 and the other to GND, then change the code to pinMode(switchReed, INPUT_PULLUP);. With this arrangement, the input is usually LOW when the switch is closed and HIGH when it is open, so the status conditions must be adjusted. Do not combine this wiring recommendation with the original HIGH/closed logic without changing the program accordingly.
5. What Is a Reed Switch?
A reed switch is an electrical switch operated by a magnetic field. It usually contains two small metal contacts sealed inside a glass tube.

When a magnet comes close to the reed switch, its contacts change state. When the magnet moves away, the contacts return to their normal state.

In a door monitoring project, the reed switch is mounted on the door frame and a magnet is mounted on the door (or vice versa). Opening or closing the door changes the distance between them and changes the switch state.

<text color="secondary" size="sm">The exact contact behavior (normally open or normally closed) depends on the reed switch model.</text>
6. Important Arduino Concepts
6.1 Variables
int ledOpen = 8;
int ledClose = 10;
int switchReed = 6;
These variables store the Arduino pin numbers:
- ledOpen: pin 8 controls the Open LED.
- ledClose: pin 10 controls the Closed LED.
- switchReed: pin 6 reads the reed switch.
6.2 pinMode()
pinMode(ledOpen, OUTPUT);
pinMode(ledClose, OUTPUT);
pinMode(switchReed, INPUT);
- OUTPUT allows Arduino to control an LED.
- INPUT allows Arduino to read the reed switch signal.
6.3 digitalRead()
digitalRead(switchReed)
Reads the digital state of pin 6. It returns HIGH or LOW.
6.4 digitalWrite()
digitalWrite(ledOpen, HIGH);
digitalWrite(ledClose, LOW);
Sets an output pin HIGH or LOW. In this circuit, HIGH turns the corresponding LED ON and LOW turns it OFF (assuming the LEDs are connected in the usual active-HIGH arrangement).
6.5 if-else statement
The if-else statement selects which instructions to run based on the reed switch reading.
6.6 Serial communication
Serial.begin(9600);
Serial.println("Your Door is Closed");
Starts serial communication at 9600 baud and displays the door status in the Serial Monitor.
7. Complete Arduino Code
int ledOpen = 8;
int ledClose = 10;
int switchReed = 6;

void setup() {
  pinMode(ledOpen, OUTPUT);
  pinMode(ledClose, OUTPUT);
  pinMode(switchReed, INPUT);
  Serial.begin(9600);
}

void loop() {
  if (digitalRead(switchReed) == HIGH) {
    digitalWrite(ledOpen, LOW);
    digitalWrite(ledClose, HIGH);
    Serial.println("Your Door is Closed");
  }
  else {
    digitalWrite(ledOpen, HIGH);
    digitalWrite(ledClose, LOW);
    Serial.println("Your Door is Open");
  }

  delay(1);
}
This is the supplied program with formatting improvements. Its status messages assume HIGH means closed and LOW means open.
8. Line-by-Line Explanation
Code	Explanation
int ledOpen = 8;	Defines pin 8 for the Open LED.
int ledClose = 10;	Defines pin 10 for the Closed LED.
int switchReed = 6;	Defines pin 6 for the reed switch.
void setup()	Runs once when Arduino starts or resets.
pinMode(ledOpen, OUTPUT);	Configures pin 8 as an output.
pinMode(ledClose, OUTPUT);	Configures pin 10 as an output.
pinMode(switchReed, INPUT);	Configures pin 6 as an input.
Serial.begin(9600);	Starts serial communication at 9600 baud.
void loop()	Repeats continuously.
digitalRead(switchReed)	Reads the reed switch state.
if (... == HIGH)	Checks whether the input is HIGH.
digitalWrite(ledOpen, LOW);	Turns OFF the Open LED.
digitalWrite(ledClose, HIGH);	Turns ON the Closed LED.
Serial.println(...)	Prints the closed-door message.
else	Runs when the input is LOW.
digitalWrite(ledOpen, HIGH);	Turns ON the Open LED.
digitalWrite(ledClose, LOW);	Turns OFF the Closed LED.
delay(1);	Pauses the program for approximately 1 millisecond.


9. Working Process
1. Arduino starts and runs the setup() function.
2. Pins 8 and 10 are configured as outputs.
3. Pin 6 is configured as an input.
4. Serial communication starts at 9600 baud.
5. The loop reads the reed switch.
6. If the reading is HIGH, the Closed LED turns ON and the Open LED turns OFF.
7. If the reading is LOW, the Open LED turns ON and the Closed LED turns OFF.
8. Arduino prints the corresponding door status.
9. The process repeats continuously.
10. Logic Table
Reed switch input	Open LED (pin 8)	Closed LED (pin 10)	Serial message
HIGH	OFF	ON	Your Door is Closed
LOW	ON	OFF	Your Door is Open


This table describes the program's logic. Whether a physically open or closed door produces HIGH depends on the switch type and wiring.
11. Block Diagram
       Door + Magnet
             |
             v
        Reed Switch
             |
             v
     Arduino Digital Pin 6
             |
       Read HIGH / LOW
             |
        +----+----+
        |         |
       HIGH      LOW
        |         |
        v         v
  Closed LED   Open LED
   Pin 10       Pin 8
        |         |
        v         v
  "Door is     "Door is
   Closed"      Open"
        \         /
         \       /
       Serial Monitor
12. Program Flowchart
          START
            |
            v
   Set Open LED as OUTPUT
  Set Closed LED as OUTPUT
   Set Reed Switch as INPUT
   Start Serial at 9600
            |
            v
    Read Reed Switch
            |
            v
      Is input HIGH?
        /       \
      YES        NO
       |          |
       v          v
  Open LED OFF  Open LED ON
  Closed LED ON Closed LED OFF
       |          |
       v          v
 Print "Door   Print "Door
  is Closed"    is Open"
       \          /
        \        /
         v      v
       Repeat loop()
13. Expected Serial Monitor Output
When the input is HIGH:
Your Door is Closed
Your Door is Closed
Your Door is Closed
When the input is LOW:
Your Door is Open
Your Door is Open
Your Door is Open
Open the Arduino IDE Serial Monitor and select 9600 baud.
14. Applications
- Door open/close status monitoring.
- Cabinet and cupboard door detection.
- Window open/close monitoring.
- Security alert prototypes.
- Smart home automation projects.
- Basic access and entry monitoring systems.
This simple sketch only displays status and controls LEDs. It does not send a remote notification or trigger an alarm.
15. Common Errors and Troubleshooting
Problem	Possible reason	Solution
LEDs do not turn ON	Incorrect LED wiring or polarity	Check LED direction and resistor.
Door status is reversed	HIGH/LOW logic differs from the assumed wiring	Swap the conditions or correct the wiring.
Status changes randomly	Input pin is floating	Use a suitable pull-up or pull-down arrangement.
Serial Monitor shows nothing	Wrong baud rate or USB connection	Select 9600 baud and check the cable/port.
Reed switch does not respond	Magnet is too far away or poorly aligned	Adjust the magnet and switch positions.
Both LEDs appear ON	Wiring error or unexpected output behavior	Check connections and test each LED separately.


16. Improvements for a Practical Project
16.1 Reduce repeated Serial Monitor messages
The original code prints the status on every loop iteration. Since the loop repeats very quickly, the Serial Monitor may fill with repeated messages.

A practical improvement is to print only when the switch state changes. The example below keeps the same assumed logic (HIGH = closed, LOW = open):
int ledOpen = 8;
int ledClose = 10;
int switchReed = 6;

int previousState = -1;

void setup() {
  pinMode(ledOpen, OUTPUT);
  pinMode(ledClose, OUTPUT);
  pinMode(switchReed, INPUT);
  Serial.begin(9600);
}

void loop() {
  int currentState = digitalRead(switchReed);

  if (currentState != previousState) {
    if (currentState == HIGH) {
      digitalWrite(ledOpen, LOW);
      digitalWrite(ledClose, HIGH);
      Serial.println("Your Door is Closed");
    } else {
      digitalWrite(ledOpen, HIGH);
      digitalWrite(ledClose, LOW);
      Serial.println("Your Door is Open");
    }

    previousState = currentState;
  }

  delay(20);
}
Note: This improvement does not add switch debouncing. Mechanical reed switches can briefly bounce when their contacts change. For a reliable installation, consider suitable debounce logic and a defined input pull-up or pull-down circuit.
17. Precautions
- Confirm whether the reed switch is normally open (NO) or normally closed (NC).
- Ensure the input pin always has a defined HIGH or LOW state.
- Keep the magnet close enough to activate the reed switch.
- Use current-limiting resistors with external LEDs.
- Check all VCC, GND, and signal connections.
- Test the HIGH/LOW readings with the Serial Monitor before relying on the door labels.
- This is a learning prototype, not a certified security system.
18. Viva Questions and Answers
Q1. What is a reed switch?
A reed switch is a magnetic switch that changes its electrical contact state when a magnetic field is nearby.

Q2. What is the purpose of this project?
It detects a door's state and indicates it using two LEDs and Serial Monitor messages.

Q3. Which pin is used for the Open LED?
Digital pin 8.

Q4. Which pin is used for the Closed LED?
Digital pin 10.

Q5. Which pin is connected to the reed switch?
Digital pin 6.

Q6. What activates a reed switch?
A magnetic field, usually provided by a small magnet.

Q7. What does digitalRead() do?
It reads the HIGH or LOW state of a digital input pin.

Q8. What does digitalWrite() do?
It sets a digital output pin HIGH or LOW.

Q9. What happens when the input is HIGH in this code?
The Closed LED turns ON, the Open LED turns OFF, and the closed-door message is printed.

Q10. What happens when the input is LOW in this code?
The Open LED turns ON, the Closed LED turns OFF, and the open-door message is printed.

Q11. Why is Serial.begin(9600) used?
It starts serial communication at 9600 baud.

Q12. Why is delay(1) used?
It pauses the program for approximately one millisecond.

Q13. What is the difference between NO and NC reed switches?
A normally open switch has open contacts in its normal state; a normally closed switch has closed contacts in its normal state.

Q14. Why can a floating input cause problems?
It may read unpredictable HIGH and LOW values because it is not held at a defined logic level.

Q15. Does the original program send a mobile notification?
No. It only controls LEDs and prints messages to the Serial Monitor.
19. Conclusion
This project demonstrates how Arduino can monitor a door using a magnetic reed switch. Arduino reads the switch state on digital pin 6. Based on the reading, it turns ON either the Open LED on pin 8 or the Closed LED on pin 10 and prints the status in the Serial Monitor. It is a useful beginner project for learning digital input, digital output, conditional statements, and basic home-automation concepts.
Document Information
Item	Details
Document title	Arduino Reed Switch Door Open/Close Detection
Programming language	Arduino C/C++
Example board	Arduino Uno
Open LED	Digital pin 8
Closed LED	Digital pin 10
Reed switch	Digital pin 6
Serial baud rate	9600
File format	Markdown (.md)