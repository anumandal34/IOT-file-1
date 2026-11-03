1. Introduction
The Arduino GSM-Based Home Automation System allows a user to control four electrical loads by sending SMS commands from an authorized mobile phone. An Arduino UNO communicates with a SIM800L GSM module through serial communication. When an SMS command is received, the Arduino checks the sender's phone number and message, then switches the appropriate relay ON or OFF.

The system also supports commands to control all four loads together and to request their current status. The load states are saved in the Arduino's EEPROM so that the program can restore the saved states after a restart.

This project is an educational prototype. Mains-voltage wiring can cause fatal electric shock or fire and must only be designed and installed by a qualified person using suitable enclosures, isolation, fuses, and correctly rated components.
2. Objectives
- Control four electrical loads using SMS commands.
- Communicate between Arduino and SIM800L using serial communication.
- Allow commands only from configured phone numbers.
- Switch individual loads or all loads together.
- Send SMS confirmations after commands.
- Send the current state of all loads when requested.
- Store load states in EEPROM to restore them after a restart.
3. Required Components
S.N.	Component	Quantity	Purpose
1	Arduino UNO	1	Main controller
2	SIM800L GSM module	1	Receives and sends SMS
3	4-channel relay module	1	Switches four loads
4	SIM card with SMS service	1	GSM network connection
5	Suitable regulated power supply for SIM800L	1	Powers GSM module
6	DC power supply for Arduino	1	Powers Arduino
7	Jumper wires	As needed	Circuit connections
8	Loads / test indicators	Up to 4	Devices controlled by relays


4. Software Requirements
- Arduino IDE
- SoftwareSerial library (normally included with the Arduino AVR board package)
- EEPROM library (included with supported Arduino AVR board packages)
- Arduino UNO board package
5. Pin Connections
5.1 SIM800L to Arduino UNO
The sketch creates SoftwareSerial GSM(8, 9), where the first argument is Arduino RX and the second is Arduino TX.
SIM800L Pin	Arduino UNO Pin
TXD	D8 (Arduino software-serial RX)
RXD	D9 (Arduino software-serial TX; use suitable level shifting)
GND	GND (common ground)
VCC	Dedicated supply matching the exact SIM800L board requirements


Power warning: A bare SIM800L module commonly requires a low-voltage supply around 3.4–4.4 V and can draw high current bursts (often approaching 2 A). Do not power a bare module from the Arduino UNO 5 V pin. Follow the specifications of your exact breakout board, use a suitable supply, and connect grounds together. Check logic-level compatibility before connecting Arduino TX to SIM800L RX.

5.2 Relay Module to Arduino UNO
Relay Input	Arduino UNO Pin
IN1	D2
IN2	D3
IN3	D4
IN4	D5
VCC	According to relay-module specification
GND	GND


The sketch writes 0 to activate a load and 1 to deactivate it. This is typical of an active-LOW relay module. Confirm the behavior of your relay board before connecting loads.
6. System Block Diagram
       Authorized Mobile Phone
                 |
             SMS command
                 |
                 v
          +-------------+
          | SIM800L GSM |
          |   Module    |
          +-------------+
                 |
          Serial communication
                 |
                 v
          +-------------+
          | Arduino UNO |
          +-------------+
             |       |
             |       +--------> EEPROM
             |                  Save load states
             |
       +-----+-----+-----+-----+
       |     |     |     |
       v     v     v     v
     Relay1 Relay2 Relay3 Relay4
       |     |     |     |
       v     v     v     v
     Load1 Load2 Load3 Load4
7. SMS Commands
Commands must match the text expected by the program. The sender must be one of the configured phone numbers.
SMS Command	Action
load1on	Turn ON Load 1
load1off	Turn OFF Load 1
load2on	Turn ON Load 2
load2off	Turn OFF Load 2
load3on	Turn ON Load 3
load3off	Turn OFF Load 3
load4on	Turn ON Load 4
load4off	Turn OFF Load 4
allon	Turn ON all four loads
alloff	Turn OFF all four loads
loadstatus	Request the status of all four loads


Example SMS exchange
User sends:
load1on
System replies:
Ok Load 1 is On
User sends:
loadstatus
Possible system reply:
Load1 On
Load2 Off
Load3 On
Load4 Off
8. Complete Arduino Source Code
#include <EEPROM.h>
#include <SoftwareSerial.h>

// SIM800L TX and RX connections
SoftwareSerial GSM(8, 9);

String phone_no1 = "+923378655465";
String phone_no2 = "Enter Number2";

String RxString = "";
char RxChar = ' ';
int Counter = 0;
String GSM_Nr = "";
String GSM_Msg = "";

// Relay output pins
#define Relay1 2
#define Relay2 3
#define Relay3 4
#define Relay4 5

int load1, load2, load3, load4;

void setup() {
  pinMode(Relay1, OUTPUT);
  digitalWrite(Relay1, 1);

  pinMode(Relay2, OUTPUT);
  digitalWrite(Relay2, 1);

  pinMode(Relay3, OUTPUT);
  digitalWrite(Relay3, 1);

  pinMode(Relay4, OUTPUT);
  digitalWrite(Relay4, 1);

  Serial.begin(9600);
  GSM.begin(9600);

  Serial.println("Initializing....");

  initModule("AT", "OK", 1000);
  initModule("AT+CPIN?", "READY", 1000);
  initModule("AT+CMGF=1", "OK", 1000);
  initModule("AT+CNMI=2,2,0,0,0", "OK", 1000);

  Serial.println("Initialized Successfully");

  // Restore saved load states from EEPROM
  load1 = EEPROM.read(1);
  load2 = EEPROM.read(2);
  load3 = EEPROM.read(3);
  load4 = EEPROM.read(4);

  relays();

  delay(100);
}

void loop() {
  // Read incoming data from the GSM module
  RxString = "";
  Counter = 0;

  while (GSM.available()) {
    delay(1);
    RxChar = char(GSM.read());

    if (Counter < 200) {
      RxString.concat(RxChar);
      Counter++;
    }
  }

  // Check whether a new SMS notification is present
  if (Received(F("CMT:"))) {
    GetSMS();
  }

  // Accept commands only from authorized numbers
  if (GSM_Nr == phone_no1 || GSM_Nr == phone_no2) {

    if (GSM_Msg == "load1on") {
      load1 = 0;
      sendSMS(GSM_Nr, "Ok Load 1 is On");
    }

    if (GSM_Msg == "load1off") {
      load1 = 1;
      sendSMS(GSM_Nr, "Ok Load 1 is Off");
    }

    if (GSM_Msg == "load2on") {
      load2 = 0;
      sendSMS(GSM_Nr, "Ok Load 2 is On");
    }

    if (GSM_Msg == "load2off") {
      load2 = 1;
      sendSMS(GSM_Nr, "Ok Load 2 is Off");
    }

    if (GSM_Msg == "load3on") {
      load3 = 0;
      sendSMS(GSM_Nr, "Ok Load 3 is On");
    }

    if (GSM_Msg == "load3off") {
      load3 = 1;
      sendSMS(GSM_Nr, "Ok Load 3 is Off");
    }

    if (GSM_Msg == "load4on") {
      load4 = 0;
      sendSMS(GSM_Nr, "Ok Load 4 is On");
    }

    if (GSM_Msg == "load4off") {
      load4 = 1;
      sendSMS(GSM_Nr, "Ok Load 4 is Off");
    }

    if (GSM_Msg == "allon") {
      load1 = 0;
      load2 = 0;
      load3 = 0;
      load4 = 0;
      sendSMS(GSM_Nr, "Ok All Load is On");
    }

    if (GSM_Msg == "alloff") {
      load1 = 1;
      load2 = 1;
      load3 = 1;
      load4 = 1;
      sendSMS(GSM_Nr, "Ok All Load is Off");
    }

    if (GSM_Msg == "loadstatus") {
      String loadst = "";

      if (load1 == 0) {
        loadst = "Load1 On\r\n";
      } else {
        loadst = "Load1 Off\r\n";
      }

      if (load2 == 0) {
        loadst += "Load2 On\r\n";
      } else {
        loadst += "Load2 Off\r\n";
      }

      if (load3 == 0) {
        loadst += "Load3 On\r\n";
      } else {
        loadst += "Load3 Off\r\n";
      }

      if (load4 == 0) {
        loadst += "Load4 On";
      } else {
        loadst += "Load4 Off";
      }

      sendSMS(GSM_Nr, loadst);
    }

    // Save states and update relay outputs
    eeprom_write();
    relays();
  }

  // Clear the sender and message after processing
  GSM_Nr = "";
  GSM_Msg = "";
}

// Save load states in EEPROM
void eeprom_write() {
  EEPROM.write(1, load1);
  EEPROM.write(2, load2);
  EEPROM.write(3, load3);
  EEPROM.write(4, load4);
}

// Apply the stored states to the relay pins
void relays() {
  digitalWrite(Relay1, load1);
  digitalWrite(Relay2, load2);
  digitalWrite(Relay3, load3);
  digitalWrite(Relay4, load4);
}

// Send an SMS message
void sendSMS(String number, String msg) {
  GSM.print("AT+CMGS=\"");
  GSM.print(number);
  GSM.println("\"\r\n");

  delay(500);
  GSM.println(msg);
  delay(500);

  // Ctrl+Z (ASCII 26) sends the SMS
  GSM.write(byte(26));

  delay(5000);
}

// Extract the sender's number and SMS text
void GetSMS() {
  GSM_Nr = RxString;

  int t1 = GSM_Nr.indexOf('"');
  GSM_Nr.remove(0, t1 + 1);
  t1 = GSM_Nr.indexOf('"');
  GSM_Nr.remove(t1);

  GSM_Msg = RxString;

  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  t1 = GSM_Msg.indexOf('"');
  GSM_Msg.remove(0, t1 + 1);
  GSM_Msg.remove(0, 1);
  GSM_Msg.trim();

  Serial.print("Number: ");
  Serial.println(GSM_Nr);

  Serial.print("SMS: ");
  Serial.println(GSM_Msg);
}

// Check whether a string is present in received data
boolean Received(String S) {
  if (RxString.indexOf(S) >= 0) {
    return true;
  } else {
    return false;
  }
}

// Initialize the GSM module using AT commands
void initModule(String cmd, char *res, int t) {
  while (1) {
    Serial.println(cmd);
    GSM.println(cmd);
    delay(100);

    while (GSM.available() > 0) {
      if (GSM.find(res)) {
        Serial.println(res);
        delay(t);
        return;
      } else {
        Serial.println("Error");
      }
    }

    delay(t);
  }
}
9. Code Explanation
9.1 Include Required Libraries
#include <EEPROM.h>
#include <SoftwareSerial.h>
- EEPROM.h provides functions to save and read data from the Arduino's EEPROM memory.
- SoftwareSerial.h creates a serial communication connection using ordinary digital pins.
9.2 Create Software Serial
SoftwareSerial GSM(8, 9);
This creates a serial connection for the SIM800L module.
- Arduino pin 8 is the software serial RX pin.
- Arduino pin 9 is the software serial TX pin.
9.3 Configure Authorized Phone Numbers
String phone_no1 = "+923378655465";
String phone_no2 = "Enter Number2";
These variables contain the phone numbers permitted to control the loads. Replace the example values with the intended numbers in international format.

The second value, Enter Number2, is a placeholder and must be replaced with a real number or removed from the authorization check.
9.4 Define Relay Pins
#define Relay1 2
#define Relay2 3
#define Relay3 4
#define Relay4 5
These definitions assign Arduino digital pins to the four relay channels.
9.5 Initialize the GSM Module
GSM.begin(9600);

initModule("AT", "OK", 1000);
initModule("AT+CPIN?", "READY", 1000);
initModule("AT+CMGF=1", "OK", 1000);
initModule("AT+CNMI=2,2,0,0,0", "OK", 1000);
The program sends AT commands to communicate with the SIM800L.
AT Command	Purpose
AT	Checks whether the GSM module responds
AT+CPIN?	Checks SIM-card readiness
AT+CMGF=1	Selects SMS text mode
AT+CNMI=2,2,0,0,0	Configures new SMS notifications to be sent to the serial interface


9.6 Read Saved States from EEPROM
load1 = EEPROM.read(1);
load2 = EEPROM.read(2);
load3 = EEPROM.read(3);
load4 = EEPROM.read(4);
The Arduino reads the previously stored state of each load from EEPROM addresses 1 to 4.
9.7 Receive an SMS
The loop() function checks whether data is available from the GSM module. It collects incoming characters in RxString.
if (Received(F("CMT:"))) {
  GetSMS();
}
The program looks for the CMT: notification, which indicates that a new SMS has been delivered directly to the serial interface.
9.8 Check the Sender
if (GSM_Nr == phone_no1 || GSM_Nr == phone_no2) {
  // Process commands
}
Only messages from either configured phone number are processed. This is a basic sender-number check, not strong authentication.
9.9 Control Individual Loads
For example:
if (GSM_Msg == "load1on") {
  load1 = 0;
  sendSMS(GSM_Nr, "Ok Load 1 is On");
}
The command load1on sets load1 to zero and sends a confirmation SMS. In this sketch, zero means the relay is activated (typically active-LOW).
9.10 Control All Loads
if (GSM_Msg == "allon") {
  load1 = 0;
  load2 = 0;
  load3 = 0;
  load4 = 0;
}
The allon command activates all four relay channels. The alloff command sets all four states to one.
9.11 Check Load Status
The loadstatus command builds a text message showing whether each load is ON or OFF, then sends that message to the requesting authorized number.
9.12 Save Data in EEPROM
EEPROM.write(1, load1);
EEPROM.write(2, load2);
EEPROM.write(3, load3);
EEPROM.write(4, load4);
EEPROM.write() stores each load state in non-volatile memory. The values can remain stored when the Arduino is powered off.
9.13 Update Relay Outputs
digitalWrite(Relay1, load1);
digitalWrite(Relay2, load2);
digitalWrite(Relay3, load3);
digitalWrite(Relay4, load4);
These statements apply the saved state values to the relay output pins.
9.14 Send an SMS
GSM.write(byte(26));
The value 26 is the ASCII Ctrl+Z character. It tells the SIM800L to submit the SMS message.
10. EEPROM State Logic
The sketch uses active-LOW relay logic.
Stored value	Relay input	Load state
0	LOW	ON (for active-LOW relay modules)
1	HIGH	OFF (for active-LOW relay modules)


When the Arduino starts, it reads these values from EEPROM and applies them to the relay outputs.
Important: EEPROM addresses 1–4 may contain uninitialized or unexpected values on a new board. Initialize and validate stored values before using them to control relays. EEPROM also has a limited write endurance, so avoid writing repeatedly when a state has not changed.

11. Expected System Operation
1. Power the Arduino and SIM800L with suitable supplies.
2. The Arduino sends initialization AT commands to the GSM module.
3. The module registers with the cellular network and the SIM is ready.
4. An authorized user sends an SMS command.
5. The SIM800L forwards the new-message notification and content to Arduino.
6. Arduino extracts the sender's phone number and message.
7. Arduino checks whether the sender is authorized.
8. If the command is valid, Arduino updates the load states.
9. The relay outputs are updated and the new states are saved in EEPROM.
10. The GSM module sends a confirmation SMS.
12. Advantages
- Allows remote control without requiring a local Wi-Fi network.
- Can control four loads independently.
- Supports switching all loads together.
- Provides SMS confirmation and status reporting.
- Stores load states in non-volatile memory.
- Demonstrates GSM, serial communication, relay control, and EEPROM concepts.
13. Limitations
- Requires a working cellular network and an active SIM card.
- SMS delivery may be delayed or fail.
- The example code uses blocking delays and basic serial parsing.
- The sender-number check alone does not provide strong security.
- The second authorized phone number is only a placeholder in the supplied sketch.
- The code assumes a particular SIM800L response format and active-LOW relay behavior.
- Some mobile operators have retired or restricted 2G service. Confirm SIM800L network availability with the local operator before building the project.
14. Safety and Reliability Notes
- Do not connect household mains wiring directly to a breadboard or Arduino.
- Mains-powered loads must be installed by a qualified electrician using appropriately rated relays, fuses, enclosures, insulation, and isolation.
- Confirm relay contact ratings for the type of load being switched.
- Use a suitable power supply for the SIM800L; its current bursts can reset the Arduino if the supply is inadequate.
- Keep phone numbers private and do not publish real numbers in public project documentation.
- Add robust SMS parsing, command validation, and stronger authentication before any real deployment.
- Set safe relay defaults and validate EEPROM contents before enabling outputs.
- Test with low-voltage indicator loads before connecting any real equipment.
15. Possible Future Improvements
- Add an Android application for easier control.
- Add feedback sensors to confirm the actual load state.
- Add command acknowledgements and error reporting.
- Improve message parsing to handle incomplete or unexpected GSM responses.
- Add stronger authentication, such as a command PIN or passphrase.
- Use EEPROM.update() or write only when a state changes to reduce unnecessary EEPROM writes.
- Add a watchdog and recovery logic for communication failures.
- Use an ESP32 with a suitable cellular modem for a more advanced IoT version.
16. Conclusion
The Arduino GSM-Based Home Automation System demonstrates how SMS communication can be used to control electrical loads remotely. The SIM800L module receives commands and communicates with the Arduino UNO, which controls four relay channels. The system can switch individual loads, control all loads together, report their status, and save load states in EEPROM. This project provides practical experience with GSM communication, AT commands, serial interfaces, relay modules, and non-volatile memory.
17. Quick Revision Questions
1. What is the purpose of the SIM800L module?
2. What is GSM communication?
3. Why is SoftwareSerial used in this project?
4. What is the purpose of the AT+CMGF=1 command?
5. Which Arduino pins control the four relays?
6. What is the purpose of EEPROM?
7. What does the SMS command loadstatus do?
8. Why does the code use GSM.write(byte(26))?
9. What does a stored relay state of 0 mean in this sketch?
10. Why must the SIM800L have a suitable power supply?
18. Project Information
Field	Details
Project Name	Arduino GSM-Based Home Automation System
Controller	Arduino UNO
GSM Module	SIM800L
Communication	SMS
Programming Language	Arduino C/C++
Relay Channels	4
Storage	EEPROM
Project Level	Beginner to Intermediate