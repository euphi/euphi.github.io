---
layout: post
tags: Homie HomieNodeCollection C++ 
---

# New InputPinArrayNode for HomieNodeCollection - or: "using C++ containers is easy!"

I mounted some white LED strips in our large wardrobe that are dimmed and switched by an ESP8266
using two reed contacts at the door. The SW application for the ESP8266 was very quick to write using my
HomieNodeCollection: It also integrates an HTU21D temperature sensor, so it informs my openHab server about
temperature and LED state on MQTT following the Homie Convention.

To read the input PINs (reed contacts) I used the `loop()` function to frequently read the state of the pin and in case
of change set the property of the corresponding LED strip.

This was quick to write, but I thought it would be better to also inform directly about the state, so I wanted to write
a HomieNode for it.

## Generic approach

Ok, but this should be done in a generic way, so it can be used in my HomieNodeCollection.
So, I have an "variable length"-array of GPIOs to be used as input. The C++ standard library has a container for 
this: a _vector_. And it is also available on Arduino. The Node should inform the Homie broker automatically about
state changes and it should support an internal action if a state change has been detected (by supporting a callback
method).

So , requirements were clear, so I started coding - and finished within 45 min (thats less time than
committing to github and writing this blog post....).

You can find the release on [github](https://github.com/euphi/HomieNodeCollection/releases/tag/v0.9.2) - and soon on
[platformio](https://platformio.org/lib/show/1407/HomieNodeCollection)


Header:
```C++

#ifndef SRC_INPUTPINARRAYNODE_H_
#define SRC_INPUTPINARRAYNODE_H_

#include <HomieNode.hpp>

class InputPinArrayNode: public HomieNode {

public:
	typedef std::function<bool(uint8_t idx, bool state)> InputPinChangeEventHandler;
	InputPinArrayNode(std::vector<uint8_t> pins, InputPinChangeEventHandler& cb);

protected:
	  virtual void setup() override;
	  virtual void loop() override;
	  virtual void onReadyToOperate() override;

private:
	std::vector<uint8_t> inputPins;
	std::vector<bool> storedPins;
	InputPinChangeEventHandler & callback;
};

#endif /* SRC_INPUTPINARRAYNODE_H_ */
```

Implementation:
```C++

#include <InputPinArrayNode.h>

InputPinArrayNode::InputPinArrayNode(std::vector<uint8_t> pins,	InputPinChangeEventHandler& cb):
			HomieNode("Input", "Contact"),
			inputPins(pins),
			storedPins(pins.size()),
			callback(cb) {
	advertiseRange("pin", 0, inputPins.size()-1);
}

void InputPinArrayNode::setup() {
	for (uint8_t pin : inputPins) {
		pinMode(pin, INPUT_PULLUP);
	}
}

void InputPinArrayNode::loop() {
	uint8_t idx = 0;
	for (uint8_t pin : inputPins) {
		bool curState = digitalRead(pin);
		bool oldState = storedPins[idx];
		if (curState != oldState) {
			setProperty("pin").setRange(idx).setRetained(true).send(curState ? "OPEN":"CLOSED");
			storedPins[idx] = curState;
			callback(idx, curState);
		}
		++idx;
	}
}

void InputPinArrayNode::onReadyToOperate() {
	uint8_t idx = 0;
	for (uint8_t pin : inputPins) {		
		storedPins[idx] = digitalRead(pin);
		setProperty("pin").setRange(idx).setRetained(true).send(storedPins[idx] ? "OPEN":"CLOSED");
		idx++;
	}
}

```

## Deployment

Adding this to my ESP8266 SW was straigt forward:

```C++

#include <Homie.hpp>
#include <Wire.h>

#include <RGBWNode.h>
#include <SensorNode.h>
#include <InputPinArrayNode.h>
#include <LoggerNode.h>

#define FW_NAME "SZ_2xLEDWhite_Thermo_2xInput"
#define FW_VERSION "1.0.0"

/* Magic sequence for Autodetectable Binary Upload */
const char *__FLAGGED_FW_NAME = "\xbf\x84\xe4\x13\x54" FW_NAME "\x93\x44\x6b\xa7\x75";
const char *__FLAGGED_FW_VERSION = "\x6a\x3f\x3e\x0e\xe1" FW_VERSION "\xb0\x30\x48\xd4\x1a";
/* End of magic sequence for Autodetectable Binary Upload */

RGBWNode white1("LED_W1", 255, 255, 255, 0);
RGBWNode white2("LED_W2", 255, 255, 255, 2);

std::vector<uint8_t> vecInputs = {12,13};
InputPinArrayNode::InputPinChangeEventHandler handler = [](uint8_t idx, bool state)->bool {
	LN.logf(__PRETTY_FUNCTION__, LoggerNode::INFO, "Input %x changed to %s", idx, state?"true":"false");
	((idx==0)?white1:white2).switchLed("w", state?100:0);
};

SensorNode sensor;
InputPinArrayNode inputs(vecInputs, handler);


void setup() {
  Homie_setFirmware(FW_NAME, FW_VERSION);
  Serial.begin(74880);
  Wire.begin(SDA, SCL);
  Homie.disableLedFeedback();
  Homie.setup();
}

void loop() {
    Homie.loop();
}
```
Voilà! That's it.

Ok, I may win the "senseless use of a lambda-function"-award for that, but I remember the synax better than for 
function pointers... :-)

I was so confident it will work that I sent the firmware via OTA (["Homie-stlye"](https://github.com/marvinroger/homie-esp8266/tree/develop/scripts/ota_updater))
to my device that is not easy to reach - without testing it before on a "lab device".

For completeness, my `platformio.ini` file:

```
[env:esp07]
platform = espressif8266
board = esp07
framework = arduino
build_flags = -DDEBUG_UPDATER=Serial -Wl,-Tesp8266.flash.1m64.ld

lib_deps=HomieNodeCollection
upload_speed = 460800                                                                                                                                                             

monitor_baud = 74880
monitor_rts = 0
monitor_dtr = 0
```
