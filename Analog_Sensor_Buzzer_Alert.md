Analog Sensor-Based Buzzer Alert System
1. Introduction
This Arduino project reads an analog sensor through pin A0 and controls a buzzer connected to digital pin 3. If the sensor reading is greater than 200, the Arduino sends PWM value 50 to the buzzer pin. Otherwise, it sends 0. The process repeats every second.

Note: The original code does not specify the sensor type. Select and connect a suitable analog-output sensor for your application.
2. Objectives
- Read an analog sensor value from A0.
- Compare the value with a threshold of 200.
- Activate the buzzer output when the value is above 200.
- Turn the buzzer output off when the value is 200 or below.
- Repeat the check every second.
3. Required Components
Component	Quantity	Purpose
Arduino Uno or compatible board	1	Main controller
Analog-output sensor	1	Provides sensor readings
Buzzer	1	Gives an audible alert
Jumper wires	As needed	Circuit connections
Breadboard	1	Optional prototyping


4. Pin Connections
Sensor
Sensor pin	Arduino pin
Analog output (AO)	A0
VCC	Supply specified by the sensor
GND	GND


Buzzer
Buzzer pin	Arduino pin
Signal / positive	Digital pin 3
GND / negative	GND


Check the buzzer's voltage and current requirements. If it draws more current than an Arduino pin can safely provide, use a suitable transistor driver.
5. Complete Arduino Code
#define BUZZER_PIN 3

void setup() 
{
  pinMode(BUZZER_PIN, OUTPUT);
  //Serial.begin(9600);
}

void loop() 
{
  int sensorValue = analogRead(A0);
  //Serial.println(sensorValue);
  if (sensorValue > 200)
  {
    analogWrite(BUZZER_PIN, 50);
  }
  else
  {
    analogWrite(BUZZER_PIN, 0);    
  }
  delay(1000);
}
6. Code Explanation
6.1 Define the buzzer pin
#define BUZZER_PIN 3
This gives the name BUZZER_PIN to Arduino digital pin 3.
6.2 Setup function
void setup()
{
  pinMode(BUZZER_PIN, OUTPUT);
}
setup() runs once. pinMode() configures pin 3 as an output. The serial initialization line in the original code is commented out, so Serial Monitor output is disabled.
6.3 Read the sensor
int sensorValue = analogRead(A0);
analogRead(A0) reads the voltage at A0 and converts it to a number. On a typical Arduino Uno using its default 10-bit ADC range, the result is 0–1023.
6.4 Compare the reading with the threshold
if (sensorValue > 200)
{
  analogWrite(BUZZER_PIN, 50);
}
else
{
  analogWrite(BUZZER_PIN, 0);
}
- If the reading is greater than 200, PWM value 50 is sent to pin 3.
- If the reading is 200 or below, value 0 is sent, turning the PWM output off.
- A reading exactly equal to 200 follows the else branch.
6.5 Delay
delay(1000);
The Arduino waits 1000 milliseconds (one second) before repeating the sensor check.
7. Important Buzzer Note
On a typical Arduino Uno, analogWrite() accepts values from 0 to 255. A value of 50 is approximately a 20% PWM duty cycle; it does not specify an audible sound frequency.
- An active buzzer may sound when supplied with an appropriate ON signal.
- A passive buzzer generally needs a tone frequency. For a passive buzzer, use tone(BUZZER_PIN, frequency) and noTone(BUZZER_PIN) rather than relying on analogWrite(..., 50) to select a pitch.
8. Working Principle
1. Arduino starts and configures pin 3 as an output.
2. The sensor supplies an analog voltage to A0.
3. Arduino reads the sensor value.
4. It compares the value with 200.
5. Above 200, it outputs PWM value 50 to the buzzer pin.
6. At 200 or below, it outputs 0.
7. It waits one second and repeats.
9. Example Output
Sensor reading	Condition	Buzzer pin output
100	At or below 200	0
199	At or below 200	0
200	Equal to threshold	0
201	Greater than 200	50
500	Greater than 200	50
900	Greater than 200	50


The actual sound depends on the buzzer type and its driver circuit.
10. Optional Serial Monitoring
To display readings in the Serial Monitor, uncomment the serial lines and initialize Serial:
void setup()
{
  pinMode(BUZZER_PIN, OUTPUT);
  Serial.begin(9600);
}

void loop()
{
  int sensorValue = analogRead(A0);
  Serial.println(sensorValue);

  if (sensorValue > 200)
    analogWrite(BUZZER_PIN, 50);
  else
    analogWrite(BUZZER_PIN, 0);

  delay(1000);
}
Select 9600 baud in the Serial Monitor.
11. Advantages
- Simple and suitable for Arduino beginners.
- Uses a threshold to trigger an alert.
- Can be adapted to different analog-output sensors.
- Requires only a small amount of code.
12. Limitations and Safety Notes
- The sensor type is not identified in the original code; the meaning of a value above 200 depends on the sensor.
- The threshold may need calibration for the actual sensor and environment.
- The program checks the sensor once per second.
- PWM value 50 controls duty cycle, not buzzer pitch.
- Do not exceed Arduino pin voltage or current limits. Use a driver circuit if required.
- This prototype is not a certified safety alarm.
13. Conclusion
The Analog Sensor-Based Buzzer Alert System monitors an analog sensor on A0. When its reading rises above 200, the Arduino sends PWM value 50 to the buzzer pin. Otherwise, it sends 0. This process repeats every second. The sensor and threshold should be selected and calibrated for the intended application.
14. Revision Questions
1. What is the purpose of BUZZER_PIN?
2. Which pin reads the sensor value?
3. What does analogRead(A0) do?
4. What is the typical analog reading range on an Arduino Uno?
5. What happens when the sensor reading is exactly 200?
6. What happens when the reading is 201?
7. What does analogWrite() control?
8. Does analogWrite(..., 50) set the buzzer frequency?
9. What is the difference between an active and passive buzzer?
10. Why is delay(1000) used?