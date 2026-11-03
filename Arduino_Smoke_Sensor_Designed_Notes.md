1. Introduction
A smoke sensor is an electronic sensor used to detect smoke or certain gases in the surrounding air. Many Arduino-compatible smoke and gas sensor modules provide an analog output that changes according to the sensor's response to the air.

In this program, Arduino reads the sensor's analog output through pin A0 and displays the reading in the Serial Monitor.
Important: The supplied program only reads and displays an analog value. It does not currently compare the reading with the threshold or activate an alarm.

2. Objectives
- Understand how an analog smoke sensor works with Arduino.
- Learn how to read an analog value using analogRead().
- Understand the purpose of pinMode(), Serial.begin(), and delay().
- Display sensor readings in the Serial Monitor.
- Understand how a threshold can be used to create a basic alert.
3. Required Components
S.N.	Component	Quantity	Purpose
1	Arduino Uno or compatible board	1	Main controller
2	Analog smoke/gas sensor module	1	Senses smoke or target gases
3	Jumper wires	As needed	Electrical connections
4	USB cable	1	Programming and power
5	Breadboard	1 (optional)	Makes connections easier


4. Smoke Sensor Overview
A smoke or gas sensor module responds to certain gases or smoke particles in the air. The exact gases detected and the response depend on the sensor model.

Many modules provide these pins:
Sensor pin	Arduino Uno connection	Function
VCC	Module-rated supply voltage	Power
GND	GND	Ground
AO	A0	Analog output
DO	Optional digital input	Digital threshold output, if provided


Note: Check the exact sensor model and its datasheet. Some modules require a warm-up period and calibration. Do not assume that every smoke sensor has the same output behavior or safe operating voltage.

5. Important Concepts
5.1 Analog Signal
An analog signal can vary over a range of values. The sensor's AO pin provides a changing voltage that Arduino can measure.
5.2 analogRead()
The Arduino Uno has a 10-bit analog-to-digital converter (ADC). With the default reference, analogRead() typically returns a value from 0 to 1023.
int analogSensor = analogRead(A0);
This reads the voltage at analog pin A0 and stores the converted value in analogSensor.
5.3 Threshold Value
A threshold is a selected value used to decide when a sensor reading should trigger an action.
int sensorThres = 400;
In the supplied code, sensorThres is declared but not used. The program does not yet check whether the sensor reading is above or below 400.

A threshold must be chosen through testing and calibration for the specific sensor and environment. A value of 400 is only an example, not a universal smoke alarm setting.
5.4 Serial Monitor
The Serial Monitor displays data sent by Arduino. It helps developers observe sensor readings and troubleshoot their projects.
Serial.begin(9600);
This starts serial communication at a baud rate of 9600.
6. Complete Arduino Program
This is the supplied program, formatted for readability. Its current function is to read the analog sensor value and print it.
int smokeA0 = A0;

// Example threshold value (not used in this version)
int sensorThres = 400;

void setup()
{
  pinMode(smokeA0, INPUT);

  Serial.begin(9600);
}

void loop()
{
  int analogSensor = analogRead(smokeA0);

  Serial.print("Pin A0: ");
  Serial.println(analogSensor);

  // The threshold comparison can be added here.

  delay(100);
}
7. Program Explanation
7.1 Declare the Sensor Pin
int smokeA0 = A0;
- int declares an integer variable.
- smokeA0 is the variable name.
- A0 is Arduino's analog input pin 0.
- The variable stores the pin identifier used by the program.
7.2 Declare the Threshold
int sensorThres = 400;
- Declares an integer variable named sensorThres.
- Its example value is 400.
- A threshold can be used to compare against the sensor reading.
- In this supplied program, the variable is not used yet.
7.3 The setup() Function
void setup()
{
  pinMode(smokeA0, INPUT);

  Serial.begin(9600);
}
The setup() function runs once when Arduino starts or resets.
Statement	Explanation
pinMode(smokeA0, INPUT);	Configures the selected pin as an input.
Serial.begin(9600);	Starts serial communication at 9600 baud.


For an analog input, analogRead() can be used without explicitly setting pinMode() to INPUT, but this line makes the intended pin direction clear.
7.4 The loop() Function
void loop()
{
  int analogSensor = analogRead(smokeA0);
}
The loop() function repeats continuously while Arduino is powered.
- analogRead(smokeA0) reads the sensor voltage at A0.
- The returned ADC value is stored in analogSensor.
7.5 Print the Sensor Reading
Serial.print("Pin A0: ");
Serial.println(analogSensor);
- Serial.print() prints the text without moving to a new line.
- Serial.println() prints the sensor reading and then moves to the next line.
Example output:
Pin A0: 215
Pin A0: 219
Pin A0: 224
Pin A0: 231
These are illustrative readings; actual values depend on the sensor, environment, power supply, and calibration.
7.6 Delay
delay(100);
Pauses the program for 100 milliseconds before the next loop iteration.

This gives an approximate reading interval of 0.1 seconds, not including the time required for the other instructions.
8. Understanding the Output
Open the Arduino IDE Serial Monitor and select 9600 baud.

The program displays a label followed by the latest analog reading.
Example reading	What it tells you
215	Current analog reading is 215
400	Current analog reading is 400
520	Current analog reading is 520


These numbers are examples only. A higher reading does not universally mean more smoke. The direction and scale of response depend on the sensor module and its circuit. Test and calibrate the exact sensor before interpreting readings.

Optional: Add a Basic Threshold Check
The following is an optional extension. It adds a comparison and prints a message when the reading is greater than the example threshold.
int smokeA0 = A0;
int sensorThres = 400;

void setup()
{
  pinMode(smokeA0, INPUT);
  Serial.begin(9600);
}

void loop()
{
  int analogSensor = analogRead(smokeA0);

  Serial.print("Pin A0: ");
  Serial.println(analogSensor);

  if (analogSensor > sensorThres)
  {
    Serial.println("Threshold exceeded!");
  }
  else
  {
    Serial.println("Reading is below threshold.");
  }

  delay(100);
}
Important: This is only a learning demonstration. The comparison direction and threshold must be determined from the exact sensor's datasheet and calibration tests. It is not a certified smoke detector or life-safety alarm.
9. System Working Process
9.1 Step-by-Step Working
1. Arduino starts.
2. The sensor input pin is configured.
3. Serial communication starts at 9600 baud.
4. Arduino reads the analog voltage from A0.
5. The ADC converts the voltage into a digital reading.
6. Arduino prints the reading in the Serial Monitor.
7. The program waits for 100 milliseconds.
8. The loop repeats and reads the sensor again.
9.2 Block Diagram
        +----------------------+
        |   Smoke/Gas Sensor   |
        +----------------------+
                   |
                   | Analog Output (AO)
                   |
                   v
        +----------------------+
        |     Arduino A0       |
        |  Analog-to-Digital   |
        |      Converter       |
        +----------------------+
                   |
                   v
        +----------------------+
        |   Arduino Program    |
        |  Read Sensor Value   |
        +----------------------+
                   |
                   v
        +----------------------+
        |    Serial Monitor    |
        |  Display the Value   |
        +----------------------+
9.3 Flowchart
        +----------------+
        |     START      |
        +----------------+
                |
                v
        +----------------+
        | Set A0 as      |
        | Input          |
        +----------------+
                |
                v
        +----------------+
        | Start Serial   |
        | at 9600 baud   |
        +----------------+
                |
                v
        +----------------+
        | Read Analog    |
        | Sensor at A0   |
        +----------------+
                |
                v
        +----------------+
        | Print Reading  |
        | on Monitor     |
        +----------------+
                |
                v
        +----------------+
        | Delay 100 ms   |
        +----------------+
                |
                v
        +----------------+
        | Repeat Loop    |
        +----------------+
10. Applications
1. Smoke and gas sensing demonstrations: Reads the response of a compatible sensor module.
2. Arduino learning projects: Demonstrates analog input and serial output.
3. Environmental monitoring prototypes: Logs sensor readings for later analysis.
4. Threshold experiments: Tests how readings change around a selected value.
5. Automation prototypes: Can provide input to a larger control system after suitable calibration and safety design.
11. Common Errors and Precautions
Problem	Possible solution
Always reads 0	Check sensor power, GND, AO connection, and selected analog pin.
Reading does not change	Check wiring, sensor warm-up, and whether the correct output pin is connected.
Values fluctuate	Allow warm-up, check the power supply, and consider averaging readings.
No Serial Monitor output	Select the correct COM port and set the baud rate to 9600.
Threshold message never appears	Confirm that threshold logic has been added and check the calibrated threshold.
Unexpected sensor behavior	Check the sensor model's datasheet and output characteristics.


Safety Precautions
- Check the sensor module's operating voltage before connecting it.
- Follow the manufacturer's warm-up and calibration instructions.
- Do not test the sensor by creating smoke or exposing yourself to hazardous gases.
- Do not rely on a hobby Arduino sensor as a replacement for a certified smoke alarm or gas detector.
- Use a certified, properly installed life-safety alarm for real fire or gas protection.
12. Viva Questions and Answers
Q1. What is a smoke sensor?

A smoke sensor is an electronic sensor that detects smoke or responds to certain gases in the surrounding air.

Q2. Which Arduino pin is used in this program?

Analog pin A0 is used.

Q3. What is the purpose of analogRead()?

It reads an analog voltage and converts it into a digital value.

Q4. What is the usual analogRead() range on Arduino Uno?

The default 10-bit ADC range is 0 to 1023.

Q5. What is the purpose of Serial.begin(9600)?

It starts serial communication at 9600 baud.

Q6. What is the purpose of Serial.println()?

It prints data and moves the cursor to the next line.

Q7. What is a threshold value?

A threshold is a selected value used to decide whether a sensor reading should trigger an action.

Q8. Is sensorThres used in the supplied program?

No. It is declared with the value 400, but no comparison is performed.

Q9. What does delay(100) do?

It pauses the program for 100 milliseconds.

Q10. What is the purpose of pinMode()?

It configures a digital pin's mode, such as INPUT or OUTPUT. In this program it documents that the sensor pin is used as an input.

Q11. Does this program activate a buzzer?

No. It only reads and displays the analog sensor value.

Q12. Does a higher reading always mean more smoke?

No. The response depends on the sensor model and circuit. It must be checked in the datasheet and calibrated.

Q13. Why is calibration important?

Calibration helps determine how the sensor responds under known conditions and helps choose a suitable threshold.

Q14. What is the function of loop()?

It repeatedly executes the instructions while Arduino is running.

Q15. Can this project replace a certified smoke alarm?

No. A hobby sensor project is not a certified life-safety device.
13. Conclusion
This Arduino program demonstrates how to read an analog smoke/gas sensor module using analog pin A0. The analogRead() function obtains a digital reading from the sensor, and the Serial Monitor displays that reading. The program also declares an example threshold value, but does not yet use it. A threshold comparison can be added after the sensor has been correctly identified, tested, and calibrated.
📌 Document Information
Field	Details
Document title	Smoke Sensor with Arduino
File format	Markdown (.md)
Programming language	Arduino C/C++
Sensor connection	Analog output to A0
Example threshold	400 (declared, not used in original code)
Serial baud rate	9600
Reading delay	100 milliseconds
Topic	Embedded Systems / Arduino / Sensors


End of Document