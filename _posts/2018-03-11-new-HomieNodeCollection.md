---
tags: blog github HomieNodeCollection Homie
layout: post
---

# New HomieNodeCollection adds parameters for temperatur adjustment, reading interval and can internally set RGBW color

## Parameters ("HomieSetting")
There are two new parameters: `TempAdj` and `sensorInterval`

### temperature adjustment ("TempAdj")

The `SensorNode` will adjust the temperature by a float value that is stored in Homie's
configuration file as "Temperate offset". This is useful if you have a static temperature offset,
e.g. due to heat accumalation in a small enclosure.

### sensor read interval

You can now set the interval to read from the sensors with the parameter "sensorInterval", the unit is milli-seconds, minimum
value is `1000` (1 sec), maximum `600000` (10 min), default value is `30000` (30 sec).

### Example part of `config.json`:
```
{"settings":{"RGBfadeDelay":5, "sensorInterval": 60000, "TempAdj": -0.5}}
```

BTW, you can push the config to a device with this little shell script

_first argument is the device name, second the configuration name. In my proposal, the `.json` extension
is applied automatically. If this annoys you, just remove the .json in the script_

```
mosquitto_pub -h cubietruck -t homie/$1/\$implementation/config/set -f $2.json    
```

## set RGBWColor

The `RGBWNode` offers a public method `void switchLed(const String &property, uint8_t value);`
The property String is "r", "g", "b", or "w" and sets the corresponding channel to value (0-100%).
This signature was choosen to be as similar to the internal HomieNode API as possible. The method also
publishes the new value on MQTT.

This is useful, if you have an switch in the software to switch some light. E.g. I have some reed contacts
in my wardrobe and the led is switched on internally if someone opens the door.

Full source code example:

```
#include <Homie.hpp>
#include <Wire.h>

#include <RGBWNode.h>
#include <SensorNode.h>
#include <LoggerNode.h>

/* Config management:
 * Use external buildnumber and generate firmware-version strings according to
 * Homie-ESP8266 OTA standard
 */

#include "buildnumber.h"
#define FW_NAME "SZ_2xLEDWhite_Thermo_2xInput"
#define FW_MAJOR "0"

#define FW_VERSION FW_MAJOR "." COMMIT_COUNTER "." BUILD_NUMBER

/* Magic sequence for Autodetectable Binary Upload */
const char *__FLAGGED_FW_NAME = "\xbf\x84\xe4\x13\x54" FW_NAME "\x93\x44\x6b\xa7\x75";
const char *__FLAGGED_FW_VERSION = "\x6a\x3f\x3e\x0e\xe1" FW_VERSION "\xb0\x30\x48\xd4\x1a";
/* End of magic sequence for Autodetectable Binary Upload */

RGBWNode white1("LED_W1", 255, 255, 255, 0);
RGBWNode white2("LED_W2", 255, 255, 255, 2);
SensorNode sensor;


void setup() {
  Homie_setFirmware(FW_NAME, FW_VERSION);
  Serial.begin(74880);
  Serial.println("Start");
  Serial.flush();
  pinMode(12, INPUT_PULLUP);
  pinMode(13, INPUT_PULLUP);
  Wire.begin(SDA, SCL);
  Homie.disableLedFeedback();
  Homie.disableResetTrigger();
  Homie.setLoggingPrinter(&Serial);
  Homie.setup();
}


void loop() {
    Homie.loop();
    static bool tuer1 = false;
    static bool tuer2 = false;

    bool t_in = digitalRead(12);
    if (t_in != tuer1) {
        LN.log("loop()", LoggerNode::INFO, "Input 1 changed");
        tuer1 = t_in;
        white1.switchLed("w", tuer1?100:0);
    }
    t_in = digitalRead(13);
    if (t_in != tuer2) {
        LN.log("loop()", LoggerNode::INFO, "Input 2 changed");
        tuer2 = t_in;
        white2.switchLed("w", tuer2?100:0);
    }    
}
```


## miscellaneous

* The version of HomieNodeCollection was bumped to 0.9.1
* the release also includes a small improvement that fading for a led strip is only calculated, if a fading is active
