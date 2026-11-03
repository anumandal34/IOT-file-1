The Arduino Gas and Smoke Detector is an electronic project designed to detect the presence of smoke or combustible gases in the surrounding environment. It uses an MQ-2 gas sensor to measure changes in gas concentration and sends the sensor reading to an Arduino UNO.

The Arduino processes the reading and displays the smoke level on a 16×2 LCD. When the reading rises above a predefined threshold, the system activates a buzzer and red LED to provide an alert. Under normal conditions, the green LED remains ON.

This is a learning prototype. It is not a certified life-safety gas alarm.
2. Objectives
- Detect smoke or combustible gases using an MQ-2 sensor.
- Read the sensor's analog output using Arduino.
- Display the sensor reading on a 16×2 LCD.
- Activate a buzzer when the reading exceeds the threshold.
- Use red and green LEDs to indicate danger and normal conditions.
- Understand basic sensor interfacing and conditional programming.
3. Required Components
S.N.	Component	Quantity	Purpose
1	Arduino UNO	1	Main controller
2	MQ-2 gas sensor module	1	Detects smoke and combustible gases
3	16×2 LCD	1	Displays sensor level and status
4	Buzzer	1	Provides an audible alert
5	Red LED	1	Indicates alert condition
6	Green LED	1	Indicates normal condition
7	Resistors (e.g. 220–330 Ω for LEDs)	2	Limits LED current
8	Breadboard	1	Circuit prototyping
9	Jumper wires	As needed	Makes connections
10	USB cable / suitable power supply	1	Powers and programs Arduino


4. Software Requirements
- Arduino IDE
- LiquidCrystal library (commonly included with the Arduino IDE)
- Arduino UNO board package
5. Pin Connections
5.1 MQ-2 Sensor
MQ-2 Pin	Arduino UNO
AO (Analog Output)	A0
VCC	As required by the module (commonly 5V)
GND	GND


5.2 LCD Connections
The code uses the LCD in 4-bit mode.
LCD Pin	Arduino UNO
RS	2
EN	3
D4	4
D5	5
D6	6
D7	7
VSS	GND
VDD	5V
VO	Contrast potentiometer wiper
RW	GND
A / LED+	Backlight supply as specified by the LCD module
K / LED−	GND, as specified by the LCD module


5.3 LEDs and Buzzer
Component	Arduino UNO
Buzzer signal / positive	13
Green LED anode (through resistor)	8
Red LED anode (through resistor)	9
LED cathodes	GND


Wiring note: Check the pin labels and voltage requirements of your particular MQ-2 module, LCD, and buzzer. Use a current-limiting resistor for each LED. Do not connect an LED directly to an Arduino output without a resistor.

6. Block Diagram
       Smoke / Gas
            |
            v
       +----------+
       | MQ-2     |
       | Sensor   |
       +----------+
            |
       Analog signal
            |
            v
       +----------+
       | Arduino  |
       | UNO      |
       +----------+
         |   |   |
         |   |   +----------> Buzzer
         |   |
         |   +--------------> Red / Green LEDs
         |
         +------------------> 16x2 LCD
7. Working Principle
1. The MQ-2 sensor responds to smoke and certain combustible gases.
2. Its analog output is connected to Arduino pin A0.
3. Arduino reads the analog value, which on an Arduino UNO ranges from 0 to 1023.
4. The program subtracts an offset of 50 and prevents the adjusted value from going below zero.
5. The adjusted reading is displayed on the LCD.
6. If the adjusted reading is greater than 50, the LCD displays an alert, the buzzer turns ON, the red LED turns ON, and the green LED turns OFF.
7. If the adjusted reading is 50 or lower, the system displays normal status, turns the buzzer and red LED OFF, and turns the green LED ON.
8. Complete Arduino Source Code
#include <LiquidCrystal.h>

// LCD pins: RS, EN, D4, D5, D6, D7
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

// Pin definitions
const int sensorPin = A0;
const int buzzer = 13;
const int G_led = 8;
const int R_led = 9;

// Sensor variables
int read_value;
const int set = 50;

void setup() {
  pinMode(sensorPin, INPUT);
  pinMode(buzzer, OUTPUT);
  pinMode(R_led, OUTPUT);
  pinMode(G_led, OUTPUT);

  lcd.begin(16, 2);
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print("   WELCOME To   ");

  lcd.setCursor(0, 1);
  lcd.print("  GAS Detector  ");

  delay(2000);
  lcd.clear();
}

void loop() {
  // Read the gas sensor
  read_value = analogRead(sensorPin);

  // Apply the example offset
  read_value = read_value - 50;

  // Do not allow a negative reading
  if (read_value < 0) {
    read_value = 0;
  }

  // Display the adjusted sensor reading
  lcd.setCursor(0, 0);
  lcd.print("Smoke Level: ");
  lcd.print(read_value);
  lcd.print("  ");

  // Alert condition
  if (read_value > set) {
    lcd.setCursor(0, 1);
    lcd.print("Alert....!!!    ");

    digitalWrite(buzzer, HIGH);
    digitalWrite(R_led, HIGH);
    digitalWrite(G_led, LOW);
  }
  // Normal condition (includes a reading equal to the threshold)
  else {
    lcd.setCursor(0, 1);
    lcd.print(".....Normal.....");

    digitalWrite(buzzer, LOW);
    digitalWrite(R_led, LOW);
    digitalWrite(G_led, HIGH);
  }

  delay(100);
}
9. Code Explanation
9.1 Include the LCD Library
#include <LiquidCrystal.h>
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
- LiquidCrystal.h provides functions to control a compatible character LCD.
- The numbers define the Arduino pins connected to RS, EN, D4, D5, and D6–D7.
9.2 Declare Variables
const int sensorPin = A0;
const int buzzer = 13;
const int G_led = 8;
const int R_led = 9;

int read_value;
const int set = 50;
- sensorPin stores the analog input pin.
- buzzer stores the buzzer output pin.
- G_led and R_led store the green and red LED pins.
- read_value stores the processed sensor reading.
- set is the example alert threshold.
9.3 The setup() Function
The setup() function runs once after the Arduino starts. It configures the pins, initializes the LCD, and displays a welcome message for two seconds.
9.4 The loop() Function
The loop() function repeats continuously. It reads the sensor, adjusts the reading, updates the LCD, and checks whether the alert threshold has been exceeded.
9.5 Read the Sensor
read_value = analogRead(sensorPin);
analogRead() reads the voltage at A0 and returns a number from 0 to 1023 on an Arduino UNO.
9.6 Adjust the Reading
read_value = read_value - 50;

if (read_value < 0) {
  read_value = 0;
}
The program subtracts 50 as an example offset. If the result is negative, it sets the result to zero. This offset is not a gas-concentration calibration.
9.7 Check the Alert Condition
if (read_value > set) {
  // Alert outputs
} else {
  // Normal outputs
}
- If the adjusted reading is greater than 50, the alert outputs are activated.
- Otherwise, the normal outputs are selected.
- Using else also handles the exact-threshold case (read_value == 50).
10. Expected Output
Adjusted reading	LCD status	Buzzer	Red LED	Green LED
0–50	Normal	OFF	OFF	ON
Greater than 50	Alert	ON	ON	OFF


Example LCD messages:
Smoke Level: 20
.....Normal.....
Smoke Level: 85
Alert....!!!
The numbers above are illustrative examples, not calibrated gas measurements.
11. Advantages
- Simple and beginner-friendly design.
- Provides both visual and audible alerts.
- Displays sensor readings in real time.
- Uses commonly available electronic components.
- Helps learners understand analog sensors and Arduino programming.
12. Limitations
- The displayed value is a raw, adjusted sensor reading, not a concentration in ppm.
- The threshold of 50 and offset of 50 are example program settings and must not be treated as universal safety limits.
- MQ-2 readings are affected by warm-up time, calibration, temperature, humidity, sensor condition, and the gases present.
- The basic project does not send alerts to a phone or the internet.
- A buzzer or sensor failure may prevent the system from warning users.
13. Applications
- Arduino and electronics laboratory demonstrations.
- Learning projects about smoke and gas sensing.
- Basic prototypes for monitoring selected indoor environments.
- Demonstrations of warning systems using LEDs, LCDs, and buzzers.
Safety warning: This prototype is for education and experimentation only. Do not use it as a replacement for a certified smoke alarm, combustible-gas detector, or carbon-monoxide alarm. For real safety protection, install certified alarms appropriate to the hazard and follow their instructions.

14. Possible Future Improvements
- Add a Wi-Fi module or ESP32 to send notifications.
- Add a mobile dashboard to view sensor readings.
- Store readings for later analysis.
- Add a relay-controlled ventilation demonstration, with suitable electrical isolation and safety design.
- Improve sensor calibration and validate the system against appropriate reference equipment.
15. Conclusion
The Arduino Gas and Smoke Detector demonstrates how an MQ-2 sensor can be connected to an Arduino UNO to monitor changes in its analog output. The system displays an adjusted reading on a 16×2 LCD and uses a buzzer and LEDs to indicate alert and normal conditions. This project provides practical experience with sensors, input/output pins, LCD interfacing, and conditional statements in Arduino programming.
16. Quick Revision Questions
1. What is the purpose of the MQ-2 sensor?
2. Which Arduino pin reads the analog sensor output?
3. What is the range returned by analogRead() on an Arduino UNO?
4. Why is a threshold used in this project?
5. What happens when the adjusted reading is greater than the threshold?
6. What is the purpose of lcd.begin(16, 2)?
7. Why is this prototype not a certified gas alarm?
17. Project Information
Field	Details
Project Name	Arduino Gas and Smoke Detector
Platform	Arduino UNO
Programming Language	Arduino C/C++
Sensor	MQ-2
Display	16×2 LCD
Main Outputs	Buzzer, Red LED, Green LED
Project Level	Beginner