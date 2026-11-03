# Arduino RGB LED Strip Color Animation Using FastLED

## 1. Introduction

This project uses an Arduino and a WS2811 addressable RGB LED strip to create colorful lighting animations.

The program uses the **FastLED library** to control 14 RGB LEDs and display different color patterns using predefined and custom color palettes.

The project automatically changes its color schemes every five seconds. It demonstrates several effects, including:

- Rainbow colors
- Rainbow stripes
- Purple and green colors
- Random colors
- Black and white stripes
- Cloud colors
- Party colors
- Red, white, and blue colors

The animation continuously moves along the LED strip.

## 2. Objectives

- Understand RGB LED strips.
- Learn how to control WS2811 LEDs using Arduino.
- Understand the FastLED library.
- Learn about color palettes.
- Create colorful LED animations.
- Understand RGB and HSV color models.
- Learn how to control LED brightness and animation speed.

## 3. Components Required

| Component | Purpose |
|---|---|
| Arduino Uno | Main controller |
| WS2811 RGB LED strip | Displays different colors |
| External power supply | Powers the LED strip |
| Jumper wires | Electrical connections |
| 330–470 Ω resistor | Recommended protection for the data line |
| Capacitor (approximately 1000 µF) | Helps reduce power-supply fluctuations |
| Breadboard | Circuit connections, if required |

## 4. Required Library

**FastLED Library**

Steps to install:

1. Open Arduino IDE.
2. Select Sketch.
3. Select Include Library.
4. Open Manage Libraries.
5. Search for `FastLED`.
6. Install the FastLED library.

This library provides functions for controlling addressable RGB LED strips and creating lighting animations.

## 5. Complete Arduino Code

```cpp
#include <FastLED.h>

#define LED_PIN     5
#define NUM_LEDS    14
#define BRIGHTNESS  64
#define LED_TYPE    WS2811
#define COLOR_ORDER GRB

CRGB leds[NUM_LEDS];

#define UPDATES_PER_SECOND 100

CRGBPalette16 currentPalette;
TBlendType currentBlending;

extern const TProgmemPalette16 myRedWhiteBluePalette_p PROGMEM;

void setup() {
    delay(3000);

    FastLED.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>
        (leds, NUM_LEDS).setCorrection(TypicalLEDStrip);

    FastLED.setBrightness(BRIGHTNESS);

    currentPalette = RainbowColors_p;
    currentBlending = LINEARBLEND;
}

void loop() {
    ChangePalettePeriodically();

    static uint8_t startIndex = 0;
    startIndex = startIndex + 1;

    FillLEDsFromPaletteColors(startIndex);

    FastLED.show();

    FastLED.delay(1000 / UPDATES_PER_SECOND);
}

void FillLEDsFromPaletteColors(uint8_t colorIndex) {
    uint8_t brightness = 255;

    for (int i = 0; i < NUM_LEDS; i++) {
        leds[i] = ColorFromPalette(
            currentPalette,
            colorIndex,
            brightness,
            currentBlending
        );

        colorIndex += 3;
    }
}

void ChangePalettePeriodically() {
    uint8_t secondHand = (millis() / 1000) % 60;
    static uint8_t lastSecond = 99;

    if (lastSecond != secondHand) {
        lastSecond = secondHand;

        if (secondHand == 0) {
            currentPalette = RainbowColors_p;
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 10) {
            currentPalette = RainbowStripeColors_p;
            currentBlending = NOBLEND;
        }

        if (secondHand == 15) {
            currentPalette = RainbowStripeColors_p;
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 20) {
            SetupPurpleAndGreenPalette();
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 25) {
            SetupTotallyRandomPalette();
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 30) {
            SetupBlackAndWhiteStripedPalette();
            currentBlending = NOBLEND;
        }

        if (secondHand == 35) {
            SetupBlackAndWhiteStripedPalette();
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 40) {
            currentPalette = CloudColors_p;
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 45) {
            currentPalette = PartyColors_p;
            currentBlending = LINEARBLEND;
        }

        if (secondHand == 50) {
            currentPalette = myRedWhiteBluePalette_p;
            currentBlending = NOBLEND;
        }

        if (secondHand == 55) {
            currentPalette = myRedWhiteBluePalette_p;
            currentBlending = LINEARBLEND;
        }
    }
}

void SetupTotallyRandomPalette() {
    for (int i = 0; i < 16; i++) {
        currentPalette[i] = CHSV(random8(), 255, random8());
    }
}

void SetupBlackAndWhiteStripedPalette() {
    fill_solid(currentPalette, 16, CRGB::Black);

    currentPalette[0] = CRGB::White;
    currentPalette[4] = CRGB::White;
    currentPalette[8] = CRGB::White;
    currentPalette[12] = CRGB::White;
}

void SetupPurpleAndGreenPalette() {
    CRGB purple = CHSV(HUE_PURPLE, 255, 255);
    CRGB green = CHSV(HUE_GREEN, 255, 255);
    CRGB black = CRGB::Black;

    currentPalette = CRGBPalette16(
        green, green, black, black,
        purple, purple, black, black,
        green, green, black, black,
        purple, purple, black, black
    );
}

const TProgmemPalette16 myRedWhiteBluePalette_p PROGMEM = {
    CRGB::Red,
    CRGB::Gray,
    CRGB::Blue,
    CRGB::Black,

    CRGB::Red,
    CRGB::Gray,
    CRGB::Blue,
    CRGB::Black,

    CRGB::Red,
    CRGB::Red,
    CRGB::Gray,
    CRGB::Gray,
    CRGB::Blue,
    CRGB::Blue,
    CRGB::Black,
    CRGB::Black
};
```

**Note:** The code above removes the unused `extern CRGBPalette16 myRedWhiteBluePalette;` declaration from your original sketch. The actual red-white-blue palette is defined using `myRedWhiteBluePalette_p`.

## 6. Explanation of the Code

### 6.1 Including the FastLED Library

```cpp
#include <FastLED.h>
```

This includes the FastLED library.

It provides functions to:
- Control RGB LED strips.
- Set LED colors.
- Change brightness.
- Create lighting effects.
- Generate color palettes.

### 6.2 Defining LED Configuration

```cpp
#define LED_PIN 5
#define NUM_LEDS 14
#define BRIGHTNESS 64
#define LED_TYPE WS2811
#define COLOR_ORDER GRB
```

| Definition | Meaning |
|---|---|
| `LED_PIN 5` | LED data wire connects to Arduino pin 5 |
| `NUM_LEDS 14` | Number of LEDs is 14 |
| `BRIGHTNESS 64` | Sets brightness level |
| `LED_TYPE WS2811` | Selects WS2811 LED type |
| `COLOR_ORDER GRB` | Defines the color channel order |

**What is GRB?**

GRB means Green, Red, Blue.

Some RGB LED strips use GRB instead of RGB ordering. The correct order depends on the LED strip.

### 6.3 Creating LED Array

```cpp
CRGB leds[NUM_LEDS];
```

This creates an array named `leds` containing 14 LED elements.

Each element stores the color information for one RGB LED.

For example:

```cpp
leds[0] = CRGB::Red;
leds[1] = CRGB::Green;
leds[2] = CRGB::Blue;
```

### 6.4 Defining Update Speed

```cpp
#define UPDATES_PER_SECOND 100
```

This defines how many animation updates should happen per second.

A value of 100 means the program aims to update the LED strip approximately 100 times per second.

### 6.5 Creating Color Palette Variables

```cpp
CRGBPalette16 currentPalette;
TBlendType currentBlending;
```

**`CRGBPalette16`**

Creates a color palette containing 16 color entries.

**`TBlendType`**

Defines how colors are selected or blended.

Two important blending types are:

| Type | Meaning |
|---|---|
| `LINEARBLEND` | Creates smooth transitions between colors |
| `NOBLEND` | Displays separate colors without smooth interpolation |

### 6.6 Setup Function

```cpp
void setup() {
    delay(3000);

    FastLED.addLeds<LED_TYPE, LED_PIN, COLOR_ORDER>
        (leds, NUM_LEDS).setCorrection(TypicalLEDStrip);

    FastLED.setBrightness(BRIGHTNESS);

    currentPalette = RainbowColors_p;
    currentBlending = LINEARBLEND;
}
```

The `setup()` function runs only once when Arduino starts.

**`delay(3000)`**

Waits three seconds before starting the LED animation. This is a power-up safety delay.

**`FastLED.addLeds()`**

Initializes the LED strip.

It specifies:
- LED type.
- Data pin.
- Color order.
- LED array.
- Number of LEDs.

**`setCorrection(TypicalLEDStrip)`**

Applies a typical LED-strip color correction setting.

**`FastLED.setBrightness(BRIGHTNESS)`**

Sets the global brightness to 64.

**`currentPalette = RainbowColors_p`**

Selects the initial rainbow color palette.

**`currentBlending = LINEARBLEND`**

Enables smooth color transitions.

### 6.7 Loop Function

```cpp
void loop() {
    ChangePalettePeriodically();

    static uint8_t startIndex = 0;
    startIndex = startIndex + 1;

    FillLEDsFromPaletteColors(startIndex);

    FastLED.show();

    FastLED.delay(1000 / UPDATES_PER_SECOND);
}
```

The `loop()` function runs continuously.

**`ChangePalettePeriodically()`**

Changes the color palette according to elapsed time.

**`static uint8_t startIndex = 0`**

Creates a variable that:
- Stores a small unsigned number.
- Starts at zero.
- Retains its value between function calls.

**`startIndex = startIndex + 1`**

Increases the starting color position each time the loop runs.

This creates the movement effect.

**`FillLEDsFromPaletteColors(startIndex)`**

Fills the LED array with colors from the selected palette.

**`FastLED.show()`**

Sends the color data to the physical LED strip.

**`FastLED.delay(1000 / UPDATES_PER_SECOND)`**

Controls the animation update interval.

With 100 updates per second, the target interval is approximately 10 milliseconds.

### 6.8 FillLEDsFromPaletteColors() Function

```cpp
void FillLEDsFromPaletteColors(uint8_t colorIndex) {
    uint8_t brightness = 255;

    for (int i = 0; i < NUM_LEDS; i++) {
        leds[i] = ColorFromPalette(
            currentPalette,
            colorIndex,
            brightness,
            currentBlending
        );

        colorIndex += 3;
    }
}
```

This function assigns colors to all 14 LEDs.

**`uint8_t colorIndex`**

Stores the starting position in the color palette.

**`uint8_t brightness = 255`**

Sets the palette color brightness to its maximum value. The global FastLED brightness setting still limits overall output.

**`for` loop**

Repeats the color assignment for every LED.

**`ColorFromPalette()`**

Gets a color from the current palette.

Its arguments are:

1. Current palette.
2. Palette color index.
3. Brightness.
4. Blending method.

**`colorIndex += 3`**

Moves the palette index forward by three for each LED, creating a color difference between neighboring LEDs.

### 6.9 ChangePalettePeriodically() Function

```cpp
void ChangePalettePeriodically() {
    uint8_t secondHand = (millis() / 1000) % 60;
    static uint8_t lastSecond = 99;
}
```

This function changes the color palette at specific times.

**`millis()`**

Returns the number of milliseconds since the Arduino program started.

**`millis() / 1000`**

Converts milliseconds into seconds.

**`% 60`**

Gets the current second position within a 60-second cycle.

For example:
- 10 seconds → 10
- 25 seconds → 25
- 40 seconds → 40

**`lastSecond`**

Stores the previously checked second. It prevents the palette-selection code from running repeatedly during the same second.

### 6.10 Palette Changing Conditions

The program selects a different palette at different times.

| Time | Color Palette | Blending |
|---|---|---|
| 0 seconds | Rainbow | LINEARBLEND |
| 10 seconds | Rainbow stripes | NOBLEND |
| 15 seconds | Rainbow stripes | LINEARBLEND |
| 20 seconds | Purple and green | LINEARBLEND |
| 25 seconds | Random colors | LINEARBLEND |
| 30 seconds | Black and white stripes | NOBLEND |
| 35 seconds | Black and white stripes | LINEARBLEND |
| 40 seconds | Cloud colors | LINEARBLEND |
| 45 seconds | Party colors | LINEARBLEND |
| 50 seconds | Red, white and blue | NOBLEND |
| 55 seconds | Red, white and blue | LINEARBLEND |

The palette sequence repeats every 60 seconds.

### 6.11 SetupTotallyRandomPalette() Function

```cpp
void SetupTotallyRandomPalette() {
    for (int i = 0; i < 16; i++) {
        currentPalette[i] = CHSV(random8(), 255, random8());
    }
}
```

This function creates a random color palette.

**`random8()`**

Generates a random 8-bit value.

**`CHSV()`**

Creates a color using HSV values:

- H = Hue
- S = Saturation
- V = Value (brightness)

The function fills all 16 palette entries with randomly generated colors.

### 6.12 SetupBlackAndWhiteStripedPalette() Function

```cpp
void SetupBlackAndWhiteStripedPalette() {
    fill_solid(currentPalette, 16, CRGB::Black);

    currentPalette[0] = CRGB::White;
    currentPalette[4] = CRGB::White;
    currentPalette[8] = CRGB::White;
    currentPalette[12] = CRGB::White;
}
```

This function creates a black and white striped palette.

**`fill_solid()`**

Fills all 16 palette entries with black.

Then the program changes four positions to white.

The result is a repeating black and white pattern.

### 6.13 SetupPurpleAndGreenPalette() Function

```cpp
void SetupPurpleAndGreenPalette() {
    CRGB purple = CHSV(HUE_PURPLE, 255, 255);
    CRGB green = CHSV(HUE_GREEN, 255, 255);
    CRGB black = CRGB::Black;

    currentPalette = CRGBPalette16(
        green, green, black, black,
        purple, purple, black, black,
        green, green, black, black,
        purple, purple, black, black
    );
}
```

This function creates a custom purple and green color palette.

It uses:
- Purple color.
- Green color.
- Black color.

These colors are arranged to create a striped lighting effect.

### 6.14 Custom Red, White and Blue Palette

```cpp
const TProgmemPalette16 myRedWhiteBluePalette_p PROGMEM = {
    CRGB::Red,
    CRGB::Gray,
    CRGB::Blue,
    CRGB::Black,
    ...
};
```

This creates a predefined color palette stored in program memory.

**`PROGMEM`**

Stores constant data in flash memory rather than using ordinary RAM.

**`TProgmemPalette16`**

Defines a 16-entry palette stored in program memory.

The palette contains:
- Red.
- Gray, representing a softer white.
- Blue.
- Black.

## 7. How the Project Works

1. Arduino starts.
2. The program waits for three seconds.
3. FastLED initializes the WS2811 LED strip.
4. Brightness is set to 64.
5. The rainbow palette is selected.
6. The program fills all 14 LEDs with palette colors.
7. The LED strip displays the colors.
8. The color index increases to create movement.
9. After the specified times, the program changes palettes.
10. The process continues in a repeating 60-second cycle.

## 8. Block Diagram

```text
+----------------------+
|     Arduino Board    |
|                      |
| FastLED Library      |
| Color Palette Logic  |
+----------+-----------+
           |
           | Data Signal
           |
           v
+----------------------+
|   WS2811 LED Driver  |
|                      |
| Controls RGB LEDs    |
+----------+-----------+
           |
           v
+----------------------+
|    RGB LED Strip     |
|                      |
| 14 Addressable LEDs  |
|                      |
| Rainbow Colors       |
| Purple and Green     |
| Random Colors        |
| Black and White      |
| Party Colors         |
+----------------------+
```

## 9. Flowchart

```text
        +---------+
        |  Start  |
        +----+----+
             |
             v
 +-----------------------+
 | Initialize FastLED    |
 | and LED strip         |
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Set brightness        |
 | Select rainbow palette|
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Check elapsed time    |
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Change palette if     |
 | required              |
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Fill LEDs with        |
 | palette colors       |
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Display LED colors    |
 | using FastLED.show()  |
 +-----------+-----------+
             |
             v
 +-----------------------+
 | Wait for next update  |
 +-----------+-----------+
             |
             +-------> Repeat
```

## 10. Wiring Connections

Connect the WS2811 LED strip to Arduino and a suitable external power supply.

| LED Strip Connection | Arduino / Power Supply |
|---|---|
| DIN / Data In | Arduino Digital Pin 5 through a recommended series resistor |
| VCC / + | External power supply matching the strip voltage |
| GND / - | External power supply GND and Arduino GND |
| Arduino GND | Connected to the same common ground |

**Important electrical precautions:**

- Many WS2811 strips use 5 V or 12 V, depending on the model. Check the strip specifications.
- Do not power a large LED strip directly from the Arduino 5 V pin.
- Use an external supply with enough current capacity.
- Connect Arduino GND and LED power-supply GND together.
- Connect the data wire to the strip's DIN end, not DOUT.
- A 330–470 Ω resistor in series with the data line is recommended.
- A capacitor across the strip's power input can help protect against voltage fluctuations.

## 11. Expected Output

The LED strip displays different colorful patterns.

| Time in cycle | Expected effect |
|---|---|
| 0–10 seconds | Moving rainbow colors |
| 10–15 seconds | Rainbow stripes without blending |
| 15–20 seconds | Smooth rainbow stripes |
| 20–25 seconds | Purple and green |
| 25–30 seconds | Random colors |
| 30–35 seconds | Black and white stripes |
| 35–40 seconds | Blended black and white |
| 40–45 seconds | Cloud colors |
| 45–50 seconds | Party colors |
| 50–55 seconds | Red, white and blue stripes |
| 55–60 seconds | Blended red, white and blue |

The sequence starts again after 60 seconds.

## 12. Important Functions

| Function | Purpose |
|---|---|
| `FastLED.addLeds()` | Initializes the LED strip |
| `FastLED.setBrightness()` | Sets global LED brightness |
| `FastLED.show()` | Sends color data to the LED strip |
| `FastLED.delay()` | Controls animation timing |
| `ColorFromPalette()` | Gets a color from the current palette |
| `CHSV()` | Creates a color using HSV values |
| `CRGB::Red` | Represents red color |
| `fill_solid()` | Fills a palette with one color |
| `random8()` | Generates a random 8-bit number |
| `millis()` | Returns elapsed milliseconds |
| `SetupTotallyRandomPalette()` | Creates a random palette |
| `ChangePalettePeriodically()` | Changes the palette according to time |

## 13. Applications

- Decorative RGB lighting.
- Smart home lighting systems.
- LED animation projects.
- Room decoration.
- Gaming and entertainment lighting.
- Festival and event lighting.
- Arduino learning projects.
- IoT-based lighting demonstrations.

## 14. Troubleshooting

| Problem | Possible Solution |
|---|---|
| `FastLED.h: No such file or directory` | Install the FastLED library |
| LEDs do not turn on | Check power supply, ground and data connections |
| Wrong colors appear | Check `COLOR_ORDER`; try RGB or GRB according to the strip |
| Only some LEDs work | Check the strip, data direction and power supply |
| LED animation is unstable | Check common ground and power-supply capacity |
| LEDs are too bright | Reduce `BRIGHTNESS` |
| LEDs flicker | Check wiring, power stability and data-line connection |
| Colors do not change | Check the palette-changing function and elapsed-time conditions |

## 15. Precautions

1. Check the operating voltage of the WS2811 LED strip.
2. Use an appropriate external power supply.
3. Connect all grounds together.
4. Connect Arduino data to the DIN input.
5. Avoid connecting the LED strip's power input directly to an unsuitable Arduino power pin.
6. Start with low brightness.
7. Avoid short circuits.
8. Check the LED strip's color order before testing.

## 16. Viva Questions and Answers

**Q1. What is FastLED?**  
FastLED is an Arduino library used to control addressable RGB LED strips.

**Q2. What is WS2811?**  
WS2811 is an LED driver IC used to control addressable RGB LEDs.

**Q3. What is RGB?**  
RGB stands for Red, Green and Blue. These three color components can be combined to produce many colors.

**Q4. What is the purpose of `LED_PIN 5`?**  
It defines Arduino digital pin 5 as the LED data pin.

**Q5. What is the purpose of `NUM_LEDS 14`?**  
It defines the number of LEDs in the strip as 14.

**Q6. What does `BRIGHTNESS 64` mean?**  
It sets the global brightness level to 64.

**Q7. What is a color palette?**  
A color palette is a collection of colors used to create lighting effects.

**Q8. What is `CRGBPalette16`?**  
It is a FastLED data type that represents a palette containing 16 color entries.

**Q9. What is `LINEARBLEND`?**  
It creates smooth transitions between colors in a palette.

**Q10. What is `NOBLEND`?**  
It selects palette colors without smooth interpolation between them.

**Q11. What does `FastLED.show()` do?**  
It sends the LED color data to the physical LED strip.

**Q12. What is the purpose of `millis()`?**  
It returns the number of milliseconds elapsed since the Arduino program started.

**Q13. What does `CHSV()` mean?**  
It creates a color using Hue, Saturation and Value.

**Q14. What is the purpose of `PROGMEM`?**  
It stores constant data in flash memory instead of ordinary RAM.

**Q15. Why is an external power supply used?**  
RGB LED strips can require more current than an Arduino board can safely provide.

## 17. Conclusion

This project demonstrates how to create colorful animations using Arduino, WS2811 RGB LEDs and the FastLED library. It uses predefined and custom color palettes to generate different lighting effects.

The project helps beginners understand RGB LEDs, color palettes, binary and color data, brightness control, and animation timing. It is also a useful foundation for smart lighting and IoT projects.

---

**Document:** Arduino RGB LED Strip Color Animation Using FastLED  
**Level:** Beginner  
**Programming Language:** Arduino C/C++  
**LED Type:** WS2811 RGB LED Strip  
**Library:** FastLED