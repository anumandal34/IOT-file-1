# Arduino Automatic Smart Irrigation System Using Four Soil Moisture Sensors and Water Pumps

## 1. Introduction

The Automatic Smart Irrigation System is an Arduino-based project that automatically supplies water to plants according to soil moisture levels.

It uses four soil moisture sensors to measure the moisture content of four different soil areas. Arduino reads the sensor values and calculates the moisture percentage.

If the moisture percentage of a particular area falls below the defined threshold of 50%, the corresponding water pump turns ON for a fixed period.

The system helps reduce manual watering and supports efficient water management.

## 2. Objectives

- To monitor soil moisture automatically.
- To control four water pumps independently.
- To supply water when soil becomes dry.
- To reduce unnecessary water consumption.
- To understand sensor interfacing with Arduino.
- To develop an automatic irrigation system.

## 3. Required Components

| Component | Quantity |
|---|---:|
| Arduino Uno | 1 |
| Soil Moisture Sensors | 4 |
| Water Pumps | 4 |
| Relay Modules or Pump Drivers | 4 |
| Water Pipes | As required |
| Breadboard | 1 |
| Jumper Wires | As required |
| External Power Supply | As required |

## 4. Pin Connections

### A. Soil Moisture Sensor Connections

| Sensor | Arduino Analog Pin |
|---|---|
| Soil Sensor 1 | A0 |
| Soil Sensor 2 | A1 |
| Soil Sensor 3 | A2 |
| Soil Sensor 4 | A3 |

Each soil moisture sensor measures the moisture level of a separate soil area.

### B. Water Pump Connections

| Water Pump | Arduino Control Pin |
|---|---|
| Pump 1 | D2 |
| Pump 2 | D3 |
| Pump 3 | D4 |
| Pump 4 | D5 |

**Note:** Connect the pumps through suitable relay modules or transistor/MOSFET driver circuits. Do not connect pumps directly to Arduino pins.

## 5. Important Constants

The program uses the following configuration values:

| Constant | Value | Purpose |
|---|---:|---|
| `SENSOR_READ_DELAY` | 86400000 ms | Wait one day between readings |
| `FULL_MOISTURE_READING` | 290 | Calibrated wet-soil reading |
| `NO_MOISTURE_READING` | 595 | Calibrated dry-soil reading |
| `CUT_OFF_MOISTURE_PERCENATGE` | 50% | Watering threshold |
| `PUMP1_WATERING_TIME` | 2000 ms | Pump 1 watering time |
| `PUMP2_WATERING_TIME` | 2000 ms | Pump 2 watering time |
| `PUMP3_WATERING_TIME` | 2000 ms | Pump 3 watering time |
| `PUMP4_WATERING_TIME` | 2000 ms | Pump 4 watering time |

## 6. Complete Arduino Code

```cpp
#define SENSOR_READ_DELAY 86400000

#define FULL_MOISTURE_READING 290
#define NO_MOISTURE_READING 595

#define CUT_OFF_MOISTURE_PERCENATGE 50

#define PUMP1_WATERING_TIME 2000
#define PUMP2_WATERING_TIME 2000
#define PUMP3_WATERING_TIME 2000
#define PUMP4_WATERING_TIME 2000

int pumpPin1 = 2;
int pumpPin2 = 3;
int pumpPin3 = 4;
int pumpPin4 = 5;

int inputSensorPin1 = A0;
int inputSensorPin2 = A1;
int inputSensorPin3 = A2;
int inputSensorPin4 = A3;

void setup()
{
  pinMode(pumpPin1, OUTPUT);
  pinMode(pumpPin2, OUTPUT);
  pinMode(pumpPin3, OUTPUT);
  pinMode(pumpPin4, OUTPUT);

  digitalWrite(pumpPin1, HIGH);
  digitalWrite(pumpPin2, HIGH);
  digitalWrite(pumpPin3, HIGH);
  digitalWrite(pumpPin4, HIGH);
}

void getMoisturePercentageAndWaterIt(
  int sensorValue,
  int outPinNo,
  int wateringTime)
{
  sensorValue = constrain(
    sensorValue,
    FULL_MOISTURE_READING,
    NO_MOISTURE_READING
  );

  int moisturePercentage = map(
    sensorValue,
    FULL_MOISTURE_READING,
    NO_MOISTURE_READING,
    100,
    0
  );

  if (moisturePercentage < CUT_OFF_MOISTURE_PERCENATGE)
  {
    digitalWrite(outPinNo, LOW);
    delay(wateringTime);
    digitalWrite(outPinNo, HIGH);
  }
}

void loop()
{
  int inputSensorPin1Value = analogRead(inputSensorPin1);
  getMoisturePercentageAndWaterIt(
    inputSensorPin1Value,
    pumpPin1,
    PUMP1_WATERING_TIME
  );

  int inputSensorPin2Value = analogRead(inputSensorPin2);
  getMoisturePercentageAndWaterIt(
    inputSensorPin2Value,
    pumpPin2,
    PUMP2_WATERING_TIME
  );

  int inputSensorPin3Value = analogRead(inputSensorPin3);
  getMoisturePercentageAndWaterIt(
    inputSensorPin3Value,
    pumpPin3,
    PUMP3_WATERING_TIME
  );

  int inputSensorPin4Value = analogRead(inputSensorPin4);
  getMoisturePercentageAndWaterIt(
    inputSensorPin4Value,
    pumpPin4,
    PUMP4_WATERING_TIME
  );

  delay(SENSOR_READ_DELAY);
}
```

## 7. Explanation of the Code

### A. Define Sensor Reading Delay

```cpp
#define SENSOR_READ_DELAY 86400000
```

This defines the waiting time between two complete soil moisture checks.

The unit is milliseconds.

- 1 second = 1,000 milliseconds.
- 1 minute = 60,000 milliseconds.
- 1 hour = 3,600,000 milliseconds.
- 1 day = 86,400,000 milliseconds.

Therefore, the program checks the soil moisture once per day.

### B. Define Moisture Calibration Values

```cpp
#define FULL_MOISTURE_READING 290
#define NO_MOISTURE_READING 595
```

These values represent the calibrated sensor readings used by the program.

- 290 represents the wet-soil reference.
- 595 represents the dry-soil reference.

These are example calibration values. Actual values depend on the sensor, soil, and installation conditions.

### C. Define Moisture Threshold

```cpp
#define CUT_OFF_MOISTURE_PERCENATGE 50
```

This sets the minimum moisture percentage required by the system.

If the calculated moisture percentage is below 50%, the pump starts watering.

### D. Define Pump Watering Time

```cpp
#define PUMP1_WATERING_TIME 2000
```

This sets the watering duration for pump 1.

The value 2000 means 2000 milliseconds, or 2 seconds.

The same watering time is configured for the other three pumps.

### E. Define Pump Pins

```cpp
int pumpPin1 = 2;
int pumpPin2 = 3;
int pumpPin3 = 4;
int pumpPin4 = 5;
```

These variables define the Arduino output pins used to control the four pumps.

### F. Define Sensor Pins

```cpp
int inputSensorPin1 = A0;
int inputSensorPin2 = A1;
int inputSensorPin3 = A2;
int inputSensorPin4 = A3;
```

These variables define the analog input pins connected to the soil moisture sensors.

Arduino reads analog values from 0 to 1023 on a standard Uno 10-bit ADC.

### G. Setup Function

```cpp
void setup()
{
  pinMode(pumpPin1, OUTPUT);
  pinMode(pumpPin2, OUTPUT);
  pinMode(pumpPin3, OUTPUT);
  pinMode(pumpPin4, OUTPUT);
}
```

The `setup()` function runs only once when Arduino starts.

It configures all four pump-control pins as output pins.

### H. Turn Pumps OFF Initially

```cpp
digitalWrite(pumpPin1, HIGH);
digitalWrite(pumpPin2, HIGH);
digitalWrite(pumpPin3, HIGH);
digitalWrite(pumpPin4, HIGH);
```

These statements set all four pump-control pins HIGH.

The code assumes active-LOW relay modules, where:

- LOW = Pump ON.
- HIGH = Pump OFF.

If your relay modules are active-HIGH, the logic must be reversed.

### I. Create Moisture Calculation Function

```cpp
void getMoisturePercentageAndWaterIt(
  int sensorValue,
  int outPinNo,
  int wateringTime)
```

This is a user-defined function that calculates soil moisture percentage and controls the corresponding pump.

It receives three parameters:

| Parameter | Purpose |
|---|---|
| `sensorValue` | Analog reading from the soil sensor |
| `outPinNo` | Arduino pump-control pin |
| `wateringTime` | Pump running duration in milliseconds |

### J. Constrain Sensor Value

```cpp
sensorValue = constrain(
  sensorValue,
  FULL_MOISTURE_READING,
  NO_MOISTURE_READING
);
```

The `constrain()` function limits the sensor reading between 290 and 595.

For example:

- If the reading is 250, it becomes 290.
- If the reading is 400, it remains 400.
- If the reading is 650, it becomes 595.

### K. Calculate Moisture Percentage

```cpp
int moisturePercentage = map(
  sensorValue,
  FULL_MOISTURE_READING,
  NO_MOISTURE_READING,
  100,
  0
);
```

The `map()` function converts the analog sensor reading into a percentage.

The conversion is:

| Sensor Reading | Moisture Percentage |
|---:|---:|
| 290 | 100% |
| 350 | Approximately 80% |
| 400 | Approximately 64% |
| 442 | Approximately 50% |
| 500 | Approximately 31% |
| 595 | 0% |

A lower analog reading represents wetter soil in this particular calibration.

### L. Check Moisture Condition

```cpp
if (moisturePercentage < CUT_OFF_MOISTURE_PERCENATGE)
```

This checks whether the soil moisture percentage is below 50%.

If the condition is true, the system starts the corresponding water pump.

### M. Turn Pump ON

```cpp
digitalWrite(outPinNo, LOW);
```

This sends a LOW signal to the selected pump-control pin.

For an active-LOW relay, the pump turns ON.

### N. Keep Pump Running

```cpp
delay(wateringTime);
```

This keeps the pump running for the configured duration.

For example, if the watering time is 2000, the pump runs for 2 seconds.

### O. Turn Pump OFF

```cpp
digitalWrite(outPinNo, HIGH);
```

This sends a HIGH signal to the selected pump-control pin.

For an active-LOW relay, the pump turns OFF.

### P. Loop Function

```cpp
void loop()
```

The loop function repeatedly performs the following operations:

1. Reads sensor 1 and controls pump 1.
2. Reads sensor 2 and controls pump 2.
3. Reads sensor 3 and controls pump 3.
4. Reads sensor 4 and controls pump 4.
5. Waits for the configured sensor reading delay.
6. Repeats the process.

## 8. Working Principle

The system uses four soil moisture sensors installed in four different plant areas.

Each sensor measures the moisture level of its soil area and sends an analog signal to Arduino.

Arduino converts the analog reading into a moisture percentage.

If the moisture level is below 50%, the corresponding pump turns ON for two seconds. After watering, the pump turns OFF.

The system performs this process for all four sensors and then waits for one day before taking the next set of readings.

### Example of System Operation

| Plant | Moisture | Condition | Pump |
|---|---:|---|---|
| Plant 1 | 80% | Wet | OFF |
| Plant 2 | 30% | Dry | ON for 2 seconds |
| Plant 3 | 65% | Wet | OFF |
| Plant 4 | 20% | Dry | ON for 2 seconds |

## 9. Block Diagram

```text
      Soil Moisture Sensors
       |    |    |    |
       A0   A1   A2   A3
       |    |    |    |
       +----+----+----+
                |
                v
           Arduino Uno
                |
       +--------+--------+
       |        |        |
       v        v        v
    Pump 1   Pump 2   Pump 3
       |
       v
     Pump 4

       Each pump waters
       its corresponding plant
```

## 10. Flowchart

```text
             START
                |
                v
       Initialize Pump Pins
                |
                v
        Turn All Pumps OFF
                |
                v
       Read Moisture Sensor 1
                |
                v
       Calculate Moisture %
                |
                v
        Is Moisture < 50%?
           /          \
         Yes           No
          |             |
          v             |
      Pump 1 ON         |
          |             |
      Wait 2 Seconds    |
          |             |
      Pump 1 OFF        |
          |             |
          +-------------+
                |
                v
       Repeat for Sensors
          2, 3 and 4
                |
                v
        Wait for 1 Day
                |
                v
          Repeat Cycle
```

## 11. Expected Output

The original program does not use an LCD or Serial Monitor, so it does not display moisture percentages.

The output is observed through the pump operation.

Example:

- Plant 1 has 80% moisture: Pump 1 remains OFF.
- Plant 2 has 30% moisture: Pump 2 turns ON for 2 seconds.
- Plant 3 has 65% moisture: Pump 3 remains OFF.
- Plant 4 has 20% moisture: Pump 4 turns ON for 2 seconds.

## 12. Applications

1. Automatic garden irrigation.
2. Smart agriculture.
3. Greenhouse watering systems.
4. Home gardening.
5. Plant nursery management.
6. Agricultural research.
7. Water-saving irrigation systems.

## 13. Advantages

- Automatic plant watering.
- Controls four different plant areas.
- Reduces manual work.
- Helps avoid unnecessary watering.
- Uses simple and affordable components.
- Allows different watering times for each pump.

## 14. Limitations

- The system uses fixed watering durations.
- Moisture readings depend on sensor calibration.
- The original program waits one full day between readings.
- It does not check moisture immediately after watering.
- It does not display moisture values.
- It does not detect an empty water tank.
- A pump may run without water if the water supply is exhausted.

## 15. Important Improvements

1. Add an LCD or Serial Monitor to display moisture percentages.
2. Use a shorter sensor-reading interval during testing.
3. Read the moisture level again after watering.
4. Add a water-level sensor to protect the pumps from dry running.
5. Use waterproof or capacitive soil moisture sensors for better durability.
6. Add independent watering thresholds and times for different plants.
7. Use non-blocking timing instead of long `delay()` calls for a more responsive system.

## 16. Viva Questions and Answers

**Q1. What is the main purpose of this project?**

To automatically water plants based on soil moisture levels.

**Q2. Which sensor is used in this project?**

A soil moisture sensor.

**Q3. How many soil moisture sensors are used?**

Four sensors.

**Q4. How many water pumps are used?**

Four water pumps.

**Q5. What is the function of `analogRead()`?**

It reads an analog voltage and returns a digital value.

**Q6. What is the range of Arduino Uno analog readings?**

0 to 1023.

**Q7. What is the function of `map()`?**

It converts a number from one range into another range.

**Q8. What is the function of `constrain()`?**

It limits a value between a minimum and maximum value.

**Q9. What is the moisture threshold in this project?**

50%.

**Q10. How long does each pump run?**

2000 milliseconds or 2 seconds.

**Q11. What is the purpose of `delay()`?**

It pauses program execution for a specified time.

**Q12. Why are relay modules used?**

They allow Arduino to control higher-power electrical devices such as water pumps.

**Q13. What is smart irrigation?**

Smart irrigation automatically supplies water according to the moisture requirements of plants.

## 17. Conclusion

The Arduino Automatic Smart Irrigation System uses four soil moisture sensors and four water pumps to water plants automatically. It measures soil moisture, calculates the moisture percentage, and activates the corresponding pump when the moisture level falls below 50%.

This project demonstrates sensor interfacing, analog input, conditional statements, user-defined functions, and automatic control.