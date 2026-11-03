# ESP32 Ubidots MQTT-Based Four-Channel Relay Control

## 1. Introduction

The ESP32 Ubidots MQTT-Based Four-Channel Relay Control project is an IoT-based home automation system. It uses an ESP32 development board, Wi-Fi, MQTT communication, and the Ubidots IoT platform to control four electrical devices remotely.

The ESP32 connects to a Wi-Fi network and communicates with Ubidots through the MQTT protocol. When a user changes a relay variable on the Ubidots dashboard, the platform sends a message to the ESP32. The ESP32 receives the message and switches the corresponding relay ON or OFF.

This project demonstrates IoT communication, MQTT messaging, Wi-Fi connectivity, and remote device control.

## 2. Objectives

- To control four relays remotely using the Ubidots IoT platform.
- To understand ESP32 Wi-Fi connectivity.
- To learn MQTT communication.
- To receive control commands from a cloud platform.
- To understand digital output control.
- To develop a basic IoT home automation system.

## 3. Components Required

1. ESP32 Development Board
2. Four-Channel Relay Module
3. Wi-Fi Router or Hotspot
4. Jumper Wires
5. Breadboard (optional)
6. Computer with Arduino IDE
7. Ubidots IoT Account
8. Electrical devices for relay-controlled loads

## 4. Pin Connections

### ESP32 and Relay Module

| Relay Module Input | ESP32 GPIO Pin |
|---|---|
| Relay 1 IN | GPIO 14 |
| Relay 2 IN | GPIO 25 |
| Relay 3 IN | GPIO 27 |
| Relay 4 IN | GPIO 26 |
| VCC | Suitable relay-module supply |
| GND | ESP32 GND |

### Relay Output Connections

Each relay generally has three terminals:

- **COM:** Common terminal
- **NO:** Normally Open terminal
- **NC:** Normally Closed terminal

When the relay is activated, COM connects to NO. When the relay is inactive, COM connects to NC.

The actual ON/OFF behavior depends on whether the relay module is active-HIGH or active-LOW.

**Safety:** Use a properly rated relay module and suitable enclosure. Mains electricity can cause serious injury or fire. Do not work on live mains wiring.

## 5. Software and Libraries Required

### Arduino IDE

Arduino IDE is used to write, compile, and upload the program to the ESP32.

### Required Libraries

| Library | Purpose |
|---|---|
| WiFi.h | Connects ESP32 to Wi-Fi |
| PubSubClient.h | Provides MQTT communication |
| stdio.h | Provides formatted input/output functions |

The program also uses standard C/C++ functions such as `strcmp()`, `strstr()`, `strlen()`, `malloc()`, and `atof()`. Include `<string.h>` and `<stdlib.h>` explicitly when needed.

### Ubidots IoT Platform

Ubidots is a cloud-based IoT platform used to receive device data, manage variables, and control connected devices.

Website: https://ubidots.com/

## 6. Arduino Code

```cpp
#include <PubSubClient.h>
#include <WiFi.h>
#include <stdio.h>

#define WIFISSID "YOUR WIFI NAME"
#define PASSWORD "WIFI PASSWORD"
#define TOKEN "TOKEN"

#define VARIABLE_LABEL_SUB_1 "relay1"
#define VARIABLE_LABEL_SUB_2 "relay2"
#define VARIABLE_LABEL_SUB_3 "relay3"
#define VARIABLE_LABEL_SUB_4 "relay4"

#define DEVICE_LABEL "esp32"
#define MQTT_CLIENT_NAME "testing"

#define R1 14
#define R2 25
#define R3 27
#define R4 26

char mqttBroker[] = "industrial.api.ubidots.com";

const int ERROR_VALUE = 65535;

const uint8_t NUMBER_OF_VARIABLES = 4;

char *variable_labels[NUMBER_OF_VARIABLES] = {
  "relay1", "relay2", "relay3", "relay4"
};

float CONTROL1;
float CONTROL2;
float CONTROL3;
float CONTROL4;

float value;
uint8_t variable;

WiFiClient ubidots;
PubSubClient client(ubidots);

void callback(char* topic, byte* payload, unsigned int length) {
  char* variable_label = (char *) malloc(sizeof(char) * 30);

  get_variable_label_topic(topic, variable_label);

  value = btof(payload, length);

  set_state(variable_label);

  execute_cases();

  free(variable_label);
}

void get_variable_label_topic(char *topic, char *variable_label) {
  Serial.print("topic:");
  Serial.println(topic);

  sprintf(variable_label, "");

  for (int i = 0; i < NUMBER_OF_VARIABLES; i++) {
    char *result_lv = strstr(topic, variable_labels[i]);

    if (result_lv != NULL) {
      uint8_t len = strlen(result_lv);
      char result[100];
      uint8_t i = 0;

      for (i = 0; i < len - 3; i++) {
        result[i] = result_lv[i];
      }

      result[i] = '\0';

      Serial.print("Label is: ");
      Serial.println(result);

      sprintf(variable_label, "%s", result);
      break;
    }
  }
}

float btof(byte *payload, unsigned int length) {
  char *demo_ = (char *) malloc(sizeof(char) * 10);

  for (int i = 0; i < length; i++) {
    demo_[i] = payload[i];
  }

  return atof(demo_);
}

void set_state(char* variable_label) {
  variable = 0;

  for (uint8_t i = 0; i < NUMBER_OF_VARIABLES; i++) {
    if (strcmp(variable_label, variable_labels[i]) == 0) {
      break;
    }

    variable++;
  }

  if (variable >= NUMBER_OF_VARIABLES) {
    variable = ERROR_VALUE;
  }
}

void execute_cases() {
  switch (variable) {
    case 0:
      CONTROL1 = value;
      digitalWrite(R1, value);
      Serial.print("CONTROL1: ");
      Serial.println(CONTROL1);
      Serial.println();
      break;

    case 1:
      CONTROL2 = value;
      digitalWrite(R2, value);
      Serial.print("CONTROL2: ");
      Serial.println(CONTROL2);
      Serial.println();
      break;

    case 2:
      CONTROL3 = value;
      digitalWrite(R3, value);
      Serial.print("CONTROL3: ");
      Serial.println(CONTROL3);
      Serial.println();
      break;

    case 3:
      CONTROL4 = value;
      digitalWrite(R4, value);
      Serial.print("CONTROL4: ");
      Serial.println(CONTROL4);
      Serial.println();
      break;

    case ERROR_VALUE:
      Serial.println("error");
      Serial.println();
      break;

    default:
      Serial.println("default");
      Serial.println();
  }
}

void reconnect() {
  if (client.connect(MQTT_CLIENT_NAME, TOKEN, "")) {
    Serial.println("connected");
  } else {
    Serial.print("failed, rc=");
    Serial.print(client.state());
    Serial.println(" try again in 2 seconds");
  }
}

void setup() {
  Serial.begin(115200);

  pinMode(R1, OUTPUT);
  pinMode(R2, OUTPUT);
  pinMode(R3, OUTPUT);
  pinMode(R4, OUTPUT);

  WiFi.begin(WIFISSID, PASSWORD);

  Serial.println();
  Serial.println();
  Serial.print("Wait for WiFi... ");

  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(500);
  }

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());

  client.setServer(mqttBroker, 1883);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();

    char topicToSubscribe_variable_1[200];
    sprintf(topicToSubscribe_variable_1, "");
    sprintf(topicToSubscribe_variable_1, "%s%s",
            "/v1.6/devices/", DEVICE_LABEL);
    sprintf(topicToSubscribe_variable_1, "%s/%s/lv",
            topicToSubscribe_variable_1, VARIABLE_LABEL_SUB_1);

    Serial.println("subscribing to topic:");
    Serial.println(topicToSubscribe_variable_1);
    client.subscribe(topicToSubscribe_variable_1);

    char topicToSubscribe_variable_2[200];
    sprintf(topicToSubscribe_variable_2, "");
    sprintf(topicToSubscribe_variable_2, "%s%s",
            "/v1.6/devices/", DEVICE_LABEL);
    sprintf(topicToSubscribe_variable_2, "%s/%s/lv",
            topicToSubscribe_variable_2, VARIABLE_LABEL_SUB_2);

    Serial.println("subscribing to topic:");
    Serial.println(topicToSubscribe_variable_2);
    client.subscribe(topicToSubscribe_variable_2);

    char topicToSubscribe_variable_3[200];
    sprintf(topicToSubscribe_variable_3, "");
    sprintf(topicToSubscribe_variable_3, "%s%s",
            "/v1.6/devices/", DEVICE_LABEL);
    sprintf(topicToSubscribe_variable_3, "%s/%s/lv",
            topicToSubscribe_variable_3, VARIABLE_LABEL_SUB_3);

    Serial.println("subscribing to topic:");
    Serial.println(topicToSubscribe_variable_3);
    client.subscribe(topicToSubscribe_variable_3);

    char topicToSubscribe_variable_4[200];
    sprintf(topicToSubscribe_variable_4, "");
    sprintf(topicToSubscribe_variable_4, "%s%s",
            "/v1.6/devices/", DEVICE_LABEL);
    sprintf(topicToSubscribe_variable_4, "%s/%s/lv",
            topicToSubscribe_variable_4, VARIABLE_LABEL_SUB_4);

    Serial.println("subscribing to topic:");
    Serial.println(topicToSubscribe_variable_4);
    client.subscribe(topicToSubscribe_variable_4);
  }

  delay(1);
}
```

## 7. Explanation of the Code

### A. Include Required Libraries

```cpp
#include <PubSubClient.h>
#include <WiFi.h>
#include <stdio.h>
```

- `PubSubClient.h`: Enables MQTT communication between ESP32 and Ubidots.
- `WiFi.h`: Connects ESP32 to a wireless network.
- `stdio.h`: Provides standard formatted input/output functions.

### B. Wi-Fi Configuration

```cpp
#define WIFISSID "YOUR WIFI NAME"
#define PASSWORD "WIFI PASSWORD"
#define TOKEN "TOKEN"
```

These definitions store the Wi-Fi network name, Wi-Fi password, and Ubidots authentication token.

You must replace the example values with your actual credentials before uploading the program.

Never share your Wi-Fi password or Ubidots token publicly.

### C. Define Ubidots Variables

```cpp
#define VARIABLE_LABEL_SUB_1 "relay1"
#define VARIABLE_LABEL_SUB_2 "relay2"
#define VARIABLE_LABEL_SUB_3 "relay3"
#define VARIABLE_LABEL_SUB_4 "relay4"
```

These labels identify the four variables created in the Ubidots device.

Each variable controls one relay.

| Ubidots Variable | Controlled Relay |
|---|---|
| relay1 | Relay 1 |
| relay2 | Relay 2 |
| relay3 | Relay 3 |
| relay4 | Relay 4 |

### D. Define Device and MQTT Client

```cpp
#define DEVICE_LABEL "esp32"
#define MQTT_CLIENT_NAME "testing"
```

- `DEVICE_LABEL`: Name of the ESP32 device registered in Ubidots.
- `MQTT_CLIENT_NAME`: Identifies the MQTT client connection.

The device label must match the device name used on the Ubidots platform.

### E. Define Relay Pins

```cpp
#define R1 14
#define R2 25
#define R3 27
#define R4 26
```

These statements assign four ESP32 GPIO pins to the relay module.

- Relay 1 uses GPIO 14.
- Relay 2 uses GPIO 25.
- Relay 3 uses GPIO 27.
- Relay 4 uses GPIO 26.

### F. Define MQTT Broker

```cpp
char mqttBroker[] = "industrial.api.ubidots.com";
```

This specifies the Ubidots MQTT server address.

The ESP32 connects to this broker to send and receive MQTT messages.

### G. Define Variables

```cpp
float CONTROL1;
float CONTROL2;
float CONTROL3;
float CONTROL4;

float value;
uint8_t variable;
```

- `CONTROL1` to `CONTROL4`: Store the received control values for the four relays.
- `value`: Stores the incoming MQTT message value.
- `variable`: Identifies which relay variable is being controlled.

### H. Create Wi-Fi and MQTT Client Objects

```cpp
WiFiClient ubidots;
PubSubClient client(ubidots);
```

- `WiFiClient ubidots`: Creates a Wi-Fi network client.
- `PubSubClient client(ubidots)`: Creates an MQTT client that uses the Wi-Fi connection.

### I. MQTT Callback Function

```cpp
void callback(char* topic, byte* payload, unsigned int length)
```

The callback function executes when an MQTT message arrives.

It receives three parameters:

- `topic`: The MQTT topic containing the variable name.
- `payload`: The received message data.
- `length`: The number of bytes in the message.

The function identifies the relay variable, converts the received message into a number, and executes the appropriate relay control operation.

### J. Get Variable Label from Topic

```cpp
void get_variable_label_topic(char *topic, char *variable_label)
```

This function searches the incoming MQTT topic for one of the four variable labels.

For example, if the incoming topic contains `relay1`, the function identifies it as the first relay variable.

The identified label is then used to determine which relay should be controlled.

### K. Convert MQTT Message into Float

```cpp
float btof(byte *payload, unsigned int length)
```

This function converts the received MQTT payload from a byte array into a floating-point number using `atof()`.

For example:

- Payload `0` becomes 0.0.
- Payload `1` becomes 1.0.

The result is stored in the `value` variable.

### L. Identify Relay State

```cpp
void set_state(char* variable_label)
```

This function compares the received variable label with the four relay labels.

It assigns a number to identify the relay:

| Variable Number | Relay |
|---|---|
| 0 | Relay 1 |
| 1 | Relay 2 |
| 2 | Relay 3 |
| 3 | Relay 4 |

If the label is invalid, the program attempts to assign an error value.

### M. Execute Relay Control

```cpp
void execute_cases()
```

This function uses a `switch` statement to select the relay that must be controlled.

Example:

```cpp
case 0:
  CONTROL1 = value;
  digitalWrite(R1, value);
  break;
```

If the received variable is `relay1`, the program updates `CONTROL1` and writes the received value to GPIO 14.

The other cases control GPIO 25, GPIO 27, and GPIO 26.

**Digital output behavior:**

- `digitalWrite(pin, LOW)` sets the output LOW.
- `digitalWrite(pin, HIGH)` sets the output HIGH.

Whether the relay turns ON or OFF depends on the relay module's input polarity. Many relay modules are active-LOW, meaning LOW activates the relay.

### N. MQTT Reconnection Function

```cpp
void reconnect()
```

This function attempts to reconnect the ESP32 to the Ubidots MQTT broker if the MQTT connection is lost.

```cpp
client.connect(MQTT_CLIENT_NAME, TOKEN, "")
```

The function uses the MQTT client name and Ubidots token to authenticate the connection.

If the connection succeeds, the Serial Monitor displays `connected`.

Otherwise, it displays the MQTT connection error code.

### O. Setup Function

```cpp
void setup()
```

The setup function runs once when the ESP32 starts.

It performs the following tasks:

1. Starts serial communication at 115200 baud.
2. Configures all four relay pins as outputs.
3. Connects ESP32 to the Wi-Fi network.
4. Displays the assigned IP address.
5. Sets the Ubidots MQTT broker address.
6. Registers the MQTT callback function.

### P. Loop Function

```cpp
void loop()
```

The loop function repeatedly checks whether the MQTT client is connected.

If the connection is lost, the program calls `reconnect()` and prepares the four MQTT subscription topics.

### Q. MQTT Subscription Topics

The program constructs four topics:

```text
/v1.6/devices/esp32/relay1/lv
/v1.6/devices/esp32/relay2/lv
/v1.6/devices/esp32/relay3/lv
/v1.6/devices/esp32/relay4/lv
```

These topics allow the ESP32 to receive the latest values of the four Ubidots variables.

The `/lv` part refers to the last value of a variable.

## 8. Working Principle

1. ESP32 starts and initializes the four relay output pins.
2. ESP32 connects to the Wi-Fi network.
3. It connects to the Ubidots MQTT broker using the authentication token.
4. It subscribes to the four relay variable topics.
5. The user changes a relay variable from the Ubidots dashboard.
6. Ubidots publishes the updated value through MQTT.
7. ESP32 receives the message through the MQTT callback.
8. The program identifies the corresponding relay.
9. The received value is converted into a numeric value.
10. ESP32 changes the corresponding GPIO output.
11. The relay module switches the connected device according to its input polarity.

## 9. Expected Output

| Ubidots Variable | Received Value | ESP32 Pin | Output |
|---|---|---|---|
| relay1 | 0 or 1 | GPIO 14 | LOW or HIGH |
| relay2 | 0 or 1 | GPIO 25 | LOW or HIGH |
| relay3 | 0 or 1 | GPIO 27 | LOW or HIGH |
| relay4 | 0 or 1 | GPIO 26 | LOW or HIGH |

The actual electrical device ON/OFF state depends on whether the relay module is active-HIGH or active-LOW.

The Serial Monitor displays Wi-Fi connection details, MQTT status, incoming topics, and relay control values.

## 10. Applications

- Smart home automation
- Remote light control
- Remote fan control
- IoT-based electrical appliance control
- Industrial device monitoring and control
- Wireless relay switching systems
- Cloud-based automation projects

## 11. Advantages

- Controls four devices remotely.
- Uses wireless communication.
- Allows control through a cloud platform.
- Supports MQTT messaging.
- Can be expanded with additional IoT features.
- Reduces the need for manual switching.

## 12. Limitations

- Requires an active Wi-Fi connection.
- Requires internet access to communicate with the cloud platform.
- Depends on the availability of the Ubidots service.
- MQTT connection failures can interrupt remote control.
- Incorrect relay polarity can cause unexpected switching.
- The original program has memory-handling and MQTT-processing issues that should be corrected.

## 13. Important Code Corrections

The supplied code demonstrates the intended project, but several issues should be fixed before using it as a reliable working system.

### A. Add `client.loop()`

The original loop does not call:

```cpp
client.loop();
```

This is important because PubSubClient uses this function to process incoming MQTT messages and maintain the connection.

Add it to the main loop:

```cpp
void loop() {
  if (!client.connected()) {
    // Reconnect and subscribe to topics
  }

  client.loop();
}
```

Without `client.loop()`, the ESP32 may not process incoming relay commands.

### B. Correct the Variable Data Type

The original code declares:

```cpp
uint8_t variable;
```

But later assigns:

```cpp
variable = ERROR_VALUE;
```

The error value is 65535, which cannot fit inside an 8-bit unsigned integer.

A better declaration is:

```cpp
int variable;
```

This allows the program to store the error value correctly.

### C. Fix MQTT Payload Handling

The original `btof()` function allocates only 10 bytes and does not add a null terminator. It also does not release the allocated memory.

A safer version is:

```cpp
float btof(byte *payload, unsigned int length) {
  char buffer[32];

  if (length >= sizeof(buffer)) {
    return 0;
  }

  memcpy(buffer, payload, length);
  buffer[length] = '\0';

  return atof(buffer);
}
```

This version uses a fixed-size buffer, adds the required string terminator, and avoids a memory leak.

### D. Subscribe Only After Successful Connection

The original code attempts subscriptions immediately after calling `reconnect()`, even if the MQTT connection attempt fails.

Only subscribe when the connection succeeds:

```cpp
if (!client.connected()) {
  reconnect();

  if (client.connected()) {
    client.subscribe("/v1.6/devices/esp32/relay1/lv");
    client.subscribe("/v1.6/devices/esp32/relay2/lv");
    client.subscribe("/v1.6/devices/esp32/relay3/lv");
    client.subscribe("/v1.6/devices/esp32/relay4/lv");
  }
}

client.loop();
```

In a complete implementation, subscriptions should be performed after every successful reconnection.

### E. Secure Your Credentials

Do not publish your real Wi-Fi password or Ubidots token in public code repositories.

Use placeholders in shared documentation and protect the real credentials.

The supplied code uses MQTT port 1883, which does not provide transport encryption. For deployment over untrusted networks, use a supported TLS-secured MQTT connection and appropriate certificate validation.

### F. Verify Relay Polarity

Some relay boards activate when their input is LOW, while others activate when their input is HIGH.

Test the relay module safely before connecting electrical appliances.

## 14. Conclusion

The ESP32 Ubidots MQTT-Based Four-Channel Relay Control project demonstrates how IoT technology can be used to control electrical devices remotely.

The ESP32 connects to Wi-Fi and communicates with the Ubidots cloud platform using MQTT. When the user changes a relay variable, the ESP32 receives the command and controls the corresponding GPIO pin.

This project helps beginners understand ESP32 programming, Wi-Fi connectivity, MQTT communication, cloud-based IoT systems, and relay control.