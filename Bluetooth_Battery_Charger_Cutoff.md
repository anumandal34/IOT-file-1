. Introduction
This project uses an Arduino and a Bluetooth serial module to control a battery charger automatically. A computer or another Bluetooth-connected device sends the battery percentage to the Arduino. Based on the received percentage, the Arduino switches a relay ON or OFF.

The program uses two battery limits:
- Below 30%: Charger ON.
- At or above 90%: Charger OFF.
- From 30% to below 90%: Keep the previous relay state.
If Bluetooth communication stops for more than one minute after it has started, the program switches the charger OFF.
Important: This code does not measure battery voltage or percentage itself. It only receives a percentage value over Bluetooth. The sending device must calculate or obtain the battery percentage and transmit it as text followed by a newline.

2. Objectives
- Receive battery percentage through Bluetooth serial communication.
- Automatically turn a charger ON when the percentage is below 30%.
- Turn the charger OFF when the percentage reaches 90%.
- Keep the previous charger state between the two limits.
- Switch the charger OFF if Bluetooth data is not received for 60 seconds after communication has started.
3. Required Components
Component	Quantity	Purpose
Arduino Uno or compatible board	1	Main controller
Bluetooth serial module (such as HC-05/HC-06)	1	Receives battery percentage
Relay module	1	Controls charger power
Jumper wires	As needed	Connections
Battery charger and battery	1 each	Charging setup
Computer or sending device	1	Sends battery percentage


4. Pin Connections
Bluetooth module to Arduino
Bluetooth module	Arduino
TXD	Digital pin 2 (SoftwareSerial RX)
RXD	Digital pin 3 (SoftwareSerial TX)
GND	GND
VCC	Suitable supply for the module


The code creates SoftwareSerial BTserial(2, 3), where pin 2 is Arduino's receive pin and pin 3 is Arduino's transmit pin. Therefore, connect Bluetooth TXD to Arduino pin 2 and Bluetooth RXD to Arduino pin 3.

Voltage warning: Many Bluetooth modules use 3.3 V logic on RXD. Check the exact module specification. If its RXD pin is not 5 V tolerant, use a suitable voltage divider or level shifter between Arduino pin 3 and Bluetooth RXD. Do not assume every module has the same voltage requirements.
Relay module to Arduino
Relay module	Arduino
IN	Digital pin 13
VCC	As specified by the relay module
GND	GND


The relay contacts are placed in the charger circuit according to the relay module's wiring diagram and the charger's electrical requirements.
5. Important Program Settings
Constant / variable	Value	Meaning
BT_RECEIVE_TIMEOUT	60000 ms	Bluetooth data timeout (60 seconds)
BAUD	9600	Serial communication speed
relay	13	Relay control pin
MIN_CUT_OFF_BATTERY_PCT	30	Below this value, charger is switched ON
MAX_CUT_OFF_BATTERY_PCT	90	At or above this value, charger is switched OFF
started	false	Indicates whether Bluetooth data has been received
lastreceivedTimeInMili	0	Time when the most recent message was received
relayState	LOW	Initial relay output state


6. Complete Arduino Code
#include <SoftwareSerial.h>

#define BT_RECEIVE_TIMEOUT 60000
#define BAUD 9600
#define relay 13
#define MIN_CUT_OFF_BATTERY_PCT 30
#define MAX_CUT_OFF_BATTERY_PCT 90

String receivedString;
SoftwareSerial BTserial(2, 3); // RX | TX

bool started = false;
long lastreceivedTimeInMili = 0;
bool relayState = LOW;

void setup()
{
  BTserial.begin(BAUD);
  pinMode(relay, OUTPUT);
  digitalWrite(relay, relayState);
}

void loop()
{
  receivedString = "";

  if (BTserial.available() > 0)
  {
    started = true;
    receivedString = BTserial.readStringUntil('\\n');
    lastreceivedTimeInMili = millis();
  }

  // Keep charger on until the first Bluetooth message is received.
  if (!started)
  {
    digitalWrite(relay, LOW);
    return;
  }

  // Turn charger off if no Bluetooth data arrives for 60 seconds.
  if ((millis() - lastreceivedTimeInMili) > BT_RECEIVE_TIMEOUT)
  {
    digitalWrite(relay, HIGH);
    return;
  }

  if (receivedString != "")
  {
    int batteryPercentage = receivedString.toInt();

    if (batteryPercentage >= MAX_CUT_OFF_BATTERY_PCT)
    {
      relayState = HIGH;
    }
    else if (batteryPercentage < MIN_CUT_OFF_BATTERY_PCT)
    {
      relayState = LOW;
    }

    digitalWrite(relay, relayState);
  }
}
7. Code Explanation
7.1 Include the SoftwareSerial library
#include <SoftwareSerial.h>
This library allows the Arduino to communicate with a Bluetooth module using ordinary digital pins instead of the board's main hardware serial connection.
7.2 Define the settings
#define BT_RECEIVE_TIMEOUT 60000
#define BAUD 9600
#define relay 13
#define MIN_CUT_OFF_BATTERY_PCT 30
#define MAX_CUT_OFF_BATTERY_PCT 90
These definitions set the timeout, communication speed, relay pin and battery percentage limits. 60000 milliseconds equals 60 seconds.
7.3 Create the Bluetooth serial connection
SoftwareSerial BTserial(2, 3);
The first pin is the Arduino RX pin (2), and the second is the Arduino TX pin (3). The Bluetooth module's TXD connects to Arduino pin 2; its RXD connects to Arduino pin 3.
7.4 Set up the Arduino
void setup()
{
  BTserial.begin(BAUD);
  pinMode(relay, OUTPUT);
  digitalWrite(relay, relayState);
}
- Starts Bluetooth serial communication at 9600 baud.
- Sets pin 13 as an output.
- Sets the initial relay output to LOW.
7.5 Read the Bluetooth message
if (BTserial.available() > 0)
{
  started = true;
  receivedString = BTserial.readStringUntil('\\n');
  lastreceivedTimeInMili = millis();
}
- BTserial.available() checks whether data has arrived.
- readStringUntil('\\n') reads text until a newline character is received.
- started = true records that Bluetooth communication has begun.
- millis() records the time since the Arduino started running.
The sending device should transmit messages such as 45\n or 90\n.
7.6 Initial condition
if (!started)
{
  digitalWrite(relay, LOW);
  return;
}
Before the first Bluetooth message arrives, the program sets the relay output to LOW and exits the current loop iteration. In this program's intended relay logic, LOW means the charger is ON.
7.7 Bluetooth timeout
if ((millis() - lastreceivedTimeInMili) > BT_RECEIVE_TIMEOUT)
{
  digitalWrite(relay, HIGH);
  return;
}
If more than 60 seconds pass without a new Bluetooth message, the program sets the relay output to HIGH and exits the loop. This is intended to turn the charger OFF if the computer sleeps or the Bluetooth connection stops sending data.
7.8 Convert the received value
int batteryPercentage = receivedString.toInt();
This converts the received text into an integer. For example, the text "75" becomes the number 75.
7.9 Apply the battery limits
if (batteryPercentage >= MAX_CUT_OFF_BATTERY_PCT)
{
  relayState = HIGH;
}
else if (batteryPercentage < MIN_CUT_OFF_BATTERY_PCT)
{
  relayState = LOW;
}
The program applies these rules:
- 90% or higher: set relay output to HIGH (charger intended OFF).
- Below 30%: set relay output to LOW (charger intended ON).
- 30% to below 90%: do not change relayState; keep the previous state.
This behavior is called hysteresis. It helps prevent the charger from rapidly switching ON and OFF around one threshold.
7.10 Update the relay
digitalWrite(relay, relayState);
This writes the stored relay state to Arduino pin 13.
8. Working Principle
1. The Arduino starts and initializes Bluetooth communication.
2. The relay output is initially set to LOW, which this program intends to mean charger ON.
3. A computer or other device sends the battery percentage through Bluetooth.
4. The Arduino reads the percentage as text and converts it to an integer.
5. If the percentage is below 30%, the Arduino sets the relay output LOW.
6. If the percentage is 90% or higher, it sets the relay output HIGH.
7. Between 30% and below 90%, it keeps the previous relay state.
8. If no new Bluetooth message arrives for more than 60 seconds after communication has started, it sets the relay output HIGH.
9. Example Operation
Received percentage	Relay output	Intended charger action
No Bluetooth message received yet	LOW	ON
20%	LOW	ON
29%	LOW	ON
30%	Previous state	No change
55%	Previous state	No change
89%	Previous state	No change
90%	HIGH	OFF
95%	HIGH	OFF
No message for over 60 seconds after communication began	HIGH	OFF


The result in the 30%–89% range depends on the state previously set by a low or high reading.
10. Data Format Sent Over Bluetooth
The sender should transmit a numeric percentage followed by a newline character.

Examples:
25
65
90
Each value must end with \\n (newline), because the Arduino uses readStringUntil('\\n').
11. Important Improvements and Safety Notes
1. Verify relay polarity: The program assumes LOW turns the charger ON and HIGH turns it OFF. Relay modules differ; some are active-LOW and others active-HIGH. Test the module safely and adjust the logic if necessary.
2. Validate incoming data: String.toInt() returns 0 when the text does not begin with a valid number. In this program, malformed text could therefore be interpreted as 0% and turn the charger ON. A safer version should reject messages that are not valid integers from 0 to 100.
3. Check the timeout design: Before the first Bluetooth message, the code keeps the charger ON indefinitely. The 60-second timeout only becomes active after the first message. Consider whether this is the safe startup behavior for your battery and charger.
4. Keep the timeout state consistent: On timeout, the code writes HIGH directly but does not update relayState. If valid Bluetooth data arrives again, the stored state may be written back. A safer implementation should explicitly update relayState when timing out and define how communication recovery should work.
5. Use a proper battery-management system: Do not rely on this Arduino program as the only protection against overcharging, over-discharging, overheating or battery faults. Use a suitable charger and battery protection/BMS designed for the battery chemistry.
6. Electrical safety: Mains voltage can cause fatal electric shock or fire. Use a properly rated, enclosed relay module and suitable isolation, fusing and wiring. Have mains-side connections designed and checked by a qualified person. Never handle exposed live wiring.
7. Do not assume the computer's battery percentage is the battery being charged: Confirm that the sending software is reporting the correct battery and that its readings are reliable.
12. Advantages
- Automatic charger control based on received battery percentage.
- Wireless communication with a Bluetooth serial module.
- Two thresholds reduce unnecessary relay switching.
- Timeout behavior can switch the charger off when communication stops.
- Uses a simple Arduino program and a relay module.
13. Limitations
- The Arduino does not measure battery percentage directly.
- The system depends on the sending device and Bluetooth connection.
- The initial state keeps the charger ON until the first message arrives.
- Invalid input is not safely validated in the original code.
- The program is not a replacement for a battery-management or charging-protection circuit.
14. Conclusion
The Bluetooth-Based Automatic Battery Charger Cut-Off System receives a battery percentage from another device and controls a charger through a relay. It switches the charger ON below 30%, switches it OFF at 90% or above, and retains the previous state between those limits. A 60-second communication timeout is also included. For real battery charging, input validation, correct relay polarity, a suitable BMS and electrical safety protections are essential.
15. Revision Questions
1. What is the purpose of the SoftwareSerial library?
2. Which Arduino pins are used for Bluetooth communication?
3. What does BT_RECEIVE_TIMEOUT represent?
4. At what battery percentage does the program switch the charger OFF?
5. What happens when the battery percentage is between 30% and 90%?
6. Why does the program use readStringUntil('\\n')?
7. What is the purpose of millis()?
8. Why should incoming Bluetooth data be validated?
9. What is hysteresis, and how does it help this project?
10. Why is a battery-management system still necessary?