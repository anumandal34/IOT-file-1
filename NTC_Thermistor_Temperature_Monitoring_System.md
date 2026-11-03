# NTC Thermistor-Based Temperature Monitoring and Alert System

## 1. Introduction

The NTC Thermistor-Based Temperature Monitoring System is an Arduino project that measures temperature using an NTC thermistor. It displays the temperature in Celsius and Fahrenheit on a 16×2 LCD.

When the temperature exceeds the programmed limit of 100°F, the red LED and buzzer turn ON to provide an alert. During normal temperature conditions, the green LED remains ON.

This project is useful for understanding temperature sensors, analog input, LCD interfacing, and automatic alert systems.

## 2. Objectives

- To measure temperature using an NTC thermistor.
- To calculate temperature in Celsius and Fahrenheit.
- To display temperature readings on an LCD.
- To generate an alert when temperature exceeds 100°F.
- To understand Arduino analog sensor interfacing.
- To learn how LEDs and buzzers work in an alert system.

## 3. Components Required

1. Arduino UNO
2. NTC Thermistor (10K)
3. 10K Resistor
4. 16×2 LCD Display
5. Green LED
6. Red LED
7. Buzzer
8. Breadboard
9. Jumper Wires
10. USB Cable

## 4. Pin Connections

### A. LCD Display Connections

The LCD uses six Arduino digital pins in 4-bit mode.

| LCD Pin | Arduino Pin |
|---|---|
| RS | 2 |
| E | 3 |
| D4 | 4 |
| D5 | 5 |
| D6 | 6 |
| D7 | 7 |
| VSS | GND |
| VDD | 5V |
| RW | GND |

Connect the LCD contrast pin (VO) to the middle terminal of a potentiometer, with the other terminals connected to 5V and GND. Connect the LCD backlight according to its pin requirements.

### B. Thermistor Connections

The thermistor and 10K resistor form a voltage divider.

| Component | Connection |
|---|---|
| 10K thermistor | 5V to A0 junction |
| 10K resistor | A0 junction to GND |
| A0 | Middle junction of both components |

### C. LED and Buzzer Connections

| Component | Arduino Pin |
|---|---|
| Green LED | Digital Pin 8 |
| Red LED | Digital Pin 9 |
| Buzzer | Digital Pin 13 |

**Note:** Connect each LED through an appropriate current-limiting resistor, such as 220Ω or 330Ω. Check the buzzer's voltage and current requirements before connecting it directly to an Arduino pin.

## 5. Arduino Code

```cpp
#include <LiquidCrystal.h> // Libraries

LiquidCrystal lcd(2, 3, 4, 5, 6, 7);

#define ThermistorPin A0

long ADC_Value;
float R1 = 10000;
float logR2, R2, T;

// Steinhart-Hart coefficients
float c1 = 0.001129148;
float c2 = 0.000234125;
float c3 = 0.0000000876741;

float temp_c, temp_f;

#define G_led 8
#define R_led 9
#define buzzer 13

void setup() {
  pinMode(ThermistorPin, INPUT);

  pinMode(R_led, OUTPUT);
  pinMode(G_led, OUTPUT);
  pinMode(buzzer, OUTPUT);

  lcd.begin(16, 2);
  lcd.clear();

  lcd.setCursor(0, 0);
  lcd.print("   Welcome To   ");

  lcd.setCursor(0, 1);
  lcd.print("Temperature NTC");

  delay(2000);
  lcd.clear();
}

void loop() {
  ADC_Value = 0;

  for (int i = 0; i < 50; i++) {
    ADC_Value = ADC_Value + analogRead(ThermistorPin);
    delay(1);
  }

  ADC_Value = ADC_Value / 50;

  R2 = R1 * (1023.0 / (float)ADC_Value - 1.0);

  logR2 = log(R2);

  temp_c = (1.0 / (
    c1 + c2 * logR2 + c3 * logR2 * logR2 * logR2
  ));

  temp_c = temp_c - 273.15;

  temp_f = (temp_c * 9.0) / 5.0 + 32.0;

  lcd.setCursor(0, 0);
  lcd.print("  Temperature   ");

  lcd.setCursor(0, 1);
  lcd.print(temp_c, 1);
  lcd.write(0xdf);
  lcd.print("C  ");

  lcd.setCursor(9, 1);
  lcd.print(temp_f, 1);
  lcd.write(0xdf);
  lcd.print("F  ");

  if (temp_f > 100) {
    digitalWrite(buzzer, HIGH);
    digitalWrite(G_led, LOW);
    digitalWrite(R_led, HIGH);
    delay(300);
  } else {
    digitalWrite(G_led, HIGH);
    digitalWrite(R_led, LOW);
  }

  digitalWrite(buzzer, LOW);
  delay(500);
}
```

## 6. Explanation of the Code

### A. Include LCD Library

```cpp
#include <LiquidCrystal.h>
```

This library allows Arduino to communicate with and control the LCD display.

### B. Create LCD Object

```cpp
LiquidCrystal lcd(2, 3, 4, 5, 6, 7);
```

Creates an LCD object and assigns the Arduino pins used for communication.

### C. Define Thermistor Pin

```cpp
#define ThermistorPin A0
```

The thermistor voltage divider is connected to analog pin A0. Arduino reads its voltage using `analogRead()`.

### D. Declare Variables

```cpp
long ADC_Value;
float R1 = 10000;
float logR2, R2, T;
float temp_c, temp_f;
```

- `ADC_Value`: Stores the analog reading from the thermistor circuit.
- `R1`: Resistance of the fixed resistor (10KΩ).
- `R2`: Calculated thermistor resistance.
- `logR2`: Natural logarithm of thermistor resistance.
- `temp_c`: Temperature in Celsius.
- `temp_f`: Temperature in Fahrenheit.
- `T`: Declared but not used in this program.

### E. Define Temperature Coefficients

```cpp
float c1 = 0.001129148;
float c2 = 0.000234125;
float c3 = 0.0000000876741;
```

These are Steinhart-Hart coefficients used to calculate temperature from the thermistor's resistance.

They must match the particular thermistor being used for accurate readings.

### F. Define LED and Buzzer Pins

```cpp
#define G_led 8
#define R_led 9
#define buzzer 13
```

- Green LED is connected to pin 8.
- Red LED is connected to pin 9.
- Buzzer is connected to pin 13.

### G. Setup Function

```cpp
void setup() {
  pinMode(ThermistorPin, INPUT);

  pinMode(R_led, OUTPUT);
  pinMode(G_led, OUTPUT);
  pinMode(buzzer, OUTPUT);

  lcd.begin(16, 2);
}
```

The `setup()` function runs once when Arduino starts.

- Configures the thermistor input.
- Configures the LEDs and buzzer as outputs.
- Initializes the LCD with 16 columns and 2 rows.

The welcome message is displayed for two seconds before the LCD is cleared.

### H. Read Analog Sensor Values

```cpp
ADC_Value = 0;

for (int i = 0; i < 50; i++) {
  ADC_Value = ADC_Value + analogRead(ThermistorPin);
  delay(1);
}

ADC_Value = ADC_Value / 50;
```

Arduino reads the thermistor voltage 50 times and calculates the average.

Averaging helps reduce small variations in the analog readings.

### I. Calculate Thermistor Resistance

```cpp
R2 = R1 * (1023.0 / (float)ADC_Value - 1.0);
```

This formula calculates the thermistor resistance using the voltage-divider circuit.

- `R1` is the fixed 10KΩ resistor.
- `R2` is the thermistor resistance.
- `ADC_Value` is the analog reading.

### J. Calculate Temperature in Celsius

```cpp
logR2 = log(R2);

temp_c = (1.0 / (
  c1 + c2 * logR2 + c3 * logR2 * logR2 * logR2
));

temp_c = temp_c - 273.15;
```

The Steinhart-Hart equation calculates temperature in Kelvin from thermistor resistance.

The program subtracts 273.15 to convert Kelvin into Celsius.

### K. Convert Celsius into Fahrenheit

```cpp
temp_f = (temp_c * 9.0) / 5.0 + 32.0;
```

This formula converts Celsius temperature into Fahrenheit.

Formula:

**Fahrenheit = (Celsius × 9/5) + 32**

### L. Display Temperature on LCD

```cpp
lcd.setCursor(0, 0);
lcd.print("  Temperature   ");

lcd.setCursor(0, 1);
lcd.print(temp_c, 1);
lcd.write(0xdf);
lcd.print("C  ");
```

Displays the temperature in Celsius on the LCD.

```cpp
lcd.setCursor(9, 1);
lcd.print(temp_f, 1);
lcd.write(0xdf);
lcd.print("F  ");
```

Displays the temperature in Fahrenheit.

The value `0xdf` is used to display the degree symbol on a compatible character LCD.

### M. Temperature Alert Condition

```cpp
if (temp_f > 100) {
  digitalWrite(buzzer, HIGH);
  digitalWrite(G_led, LOW);
  digitalWrite(R_led, HIGH);
  delay(300);
}
```

If the temperature exceeds 100°F:

- Buzzer turns ON.
- Green LED turns OFF.
- Red LED turns ON.
- Arduino waits for 300 milliseconds.

### N. Normal Temperature Condition

```cpp
else {
  digitalWrite(G_led, HIGH);
  digitalWrite(R_led, LOW);
}
```

If the temperature is 100°F or below:

- Green LED turns ON.
- Red LED turns OFF.

### O. Turn Off Buzzer

```cpp
digitalWrite(buzzer, LOW);
delay(500);
```

The buzzer is turned OFF after the alert section. Arduino waits 500 milliseconds before repeating the measurement.

**Important:** In this program, the buzzer sounds for approximately 300 milliseconds during each high-temperature loop cycle, followed by the 500-millisecond delay.

## 7. Working Principle

1. Arduino starts and initializes the LCD, thermistor input, LEDs, and buzzer.
2. The thermistor senses the surrounding temperature.
3. Its resistance changes according to temperature.
4. The voltage-divider circuit produces a voltage that Arduino reads through A0.
5. Arduino averages 50 analog readings.
6. The program calculates thermistor resistance.
7. The Steinhart-Hart equation calculates temperature in Celsius.
8. The program converts Celsius into Fahrenheit.
9. Both temperature values are displayed on the LCD.
10. If the Fahrenheit temperature exceeds 100°F, the red LED and buzzer provide an alert.
11. Otherwise, the green LED remains ON.

## 8. Expected Output

| Temperature Condition | Green LED | Red LED | Buzzer |
|---|---|---|---|
| 100°F or below | ON | OFF | OFF |
| Above 100°F | OFF | ON | ON briefly during each cycle |

The LCD continuously displays temperature in Celsius and Fahrenheit.

## 9. Applications

- Room temperature monitoring
- Electronic equipment temperature monitoring
- Home automation systems
- Small temperature alert systems
- Educational electronics projects
- Basic environmental monitoring

## 10. Advantages

- Simple and low-cost project.
- Displays temperature in two units.
- Provides automatic high-temperature alerts.
- Uses commonly available Arduino components.
- Helps beginners understand analog sensors and LCD programming.

## 11. Limitations

- Temperature accuracy depends on the thermistor and its calibration.
- Incorrect resistance values produce inaccurate readings.
- Electrical noise can affect analog measurements.
- The system has a fixed alert threshold of 100°F.
- The circuit is not suitable as a certified industrial or medical temperature monitoring device.

## 12. Important Improvements and Safety

1. Check that the thermistor is actually 10KΩ at its rated reference temperature.
2. Confirm that the Steinhart-Hart coefficients match the thermistor.
3. Avoid dividing by zero if the analog reading is zero.
4. Verify the voltage-divider orientation. The resistance formula in this code assumes the thermistor is connected between 5V and A0, and the fixed resistor is connected between A0 and GND.
5. Use a current-limiting resistor with each LED.
6. Use an appropriate driver if the buzzer requires more current than an Arduino output pin can safely provide.
7. Calibrate the readings against a reliable thermometer.

## 13. Conclusion

The NTC Thermistor-Based Temperature Monitoring and Alert System is an Arduino project that measures and displays temperature in Celsius and Fahrenheit.

It uses a thermistor to detect temperature changes and an LCD to display the readings. When the temperature exceeds 100°F, the red LED and buzzer provide an alert.

This project helps beginners understand thermistors, voltage dividers, analog input, mathematical temperature conversion, LCD interfacing, and automatic alert systems.