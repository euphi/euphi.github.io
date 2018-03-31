---
layout: post
tags: esp8266 homie pitfall arduino
---

# Why asynchronous networking can crash your ESP8266 program

## The Problem

My [library](https://github.com/euphi/ESP_Homie_WS2812FX) to connect the nice [WS2812FX](https://github.com/kitesurfer1404/WS2812FX)
lib to Homie often crashed when sending commands to it.

I analysed the code of my lib and of WS2812FX and also monitored free heap and everything seemed ok.

## The Background

Yesterday I dug further and suddently I remembered a problem I found a while ago:

The [Arduino core for ESP8266](https://github.com/esp8266/Arduino) uses two different task:
* The "normal" tasks that runs `setup()` and `loop()`
* The "network" task that handles all network related stuff.

When you do "synchronous" network I/O you don't notice this: You just poll in your `loop()` if there is something new to handle.

However, Homie-ESP8266 uses asynchronous I/O ([Async MQTT](https://github.com/marvinroger/async-mqtt-client) that is based on
[ESPAsyncTCP](https://github.com/me-no-dev/ESPAsyncTCP). That means these library
(based on the Espressif [NON-OS SDK](https://github.com/espressif/ESP8266_NONOS_SDK)) call you back if they detect new
activity on network - **and this is done in the "network" task.**

The ESP8266 non-os SDK doesn't do a real parallel processing, it does a [cooperative multitreading](https://en.wikipedia.org/wiki/Cooperative_multitasking),
but the task switch can occur whenever `delay()` or `yield()` is called. 

So, there are many recommendations to call `yield()` when performing long tasks in your loop, to handle "background" tasks, e.g.
on [Stackoverflow](https://stackoverflow.com/questions/34497758/what-is-the-secret-of-the-arduino-yieldfunction) or on
[Sparkfun](https://learn.sparkfun.com/tutorials/esp8266-thing-hookup-guide/using-the-arduino-addon). 

So, whenevery you call `delay()` or `yield()` in your `loop()` (directly or indirectly by another functions that calls them),
there is a context switch and your callbacks of you asynchronous network I/O may be called. In case of Homie this is the
`HomieNode::handleInput()` method.

## The Details

So, why my ESP_Homie_WS2812FX caused my ESP8266 to reset often when sending commands to it? (e.g. change mode, brightness, or speed)?

The WS2812FX does a call to `delay(1)` (which is just another way to say `yield()`) just after it finished its
calculations for the next cycle and before sending it to the LED strip:

```C++

void WS2812FX::service() {
  if(_running || _triggered) {
    unsigned long now = millis(); // Be aware, millis() rolls over every 49 days
    bool doShow = false;
    for(uint8_t i=0; i < _num_segments; i++) {
      _segment_index = i;
      if(now > SEGMENT_RUNTIME.next_time || _triggered) {
        doShow = true;
        uint16_t delay = (this->*_mode[SEGMENT.mode])();
        SEGMENT_RUNTIME.next_time = now + max((int)delay, SPEED_MIN);
        SEGMENT_RUNTIME.counter_mode_call++;
      }
    }
    if(doShow) {
      delay(1); // for ESP32 (see https://forums.adafruit.com/viewtopic.php?f=47&t=117327)
      Adafruit_NeoPixel::show();
    }
    _triggered = false;
  }
}


```

So..if new MQTT data arrives while WS2812FX does it calculations they are handled, when the line `delay(1)` is called.
So this call returns **after** the `HomieNode::handleInput()` returns.

However, my `handleInput()` [implementation for the ESP_Homie_WS2812FX libray](https://github.com/euphi/ESP_Homie_WS2812FX/blob/35b656dd58c573bf2b75a3b433441a4679e4cbb2/src/WS2812Node.cpp#L63)
directly called the WS2812FX library to change mode etc. This resets the internal state and thus writes data invalid.
After `handleInput()` returns, the `WS2812FX::service()` continues after the `delay()` - and calls `show()` of the Adafruit_Pixel
libary while its underlying data has just been invalidated. This leads to a memory corruption and thus a reset.

## The Conclusion 

So, what's the conclusion?

**Just never change data in you asynchronous callback (e.g. `HomieNode::handleInput()` that you rely on in your `loop()` methods.
You must synchronise your data handover with your loop task.**

Of couse, this includes everything what is called from your global `loop()` functions: your `HomieNode::loop()` instances,
service routines of your called libraries (e.g `WS128FX::service()` - or `Machine::run()` of the [Automaton](https://github.com/tinkerspy/Automaton) library.)

## The Solution

In most cases (and so in my) the solution is to "buffer" the data and handle it in `loop()`.
I validate the received data, store it in a "buffer" variable, and set a flag that the variable is "dirty". 
In `loop()` I check what is "dirty" and handle the variable, e.g. new mode accordingly.
Since then my ESP_Homie_WS2812FX library runs rock-solide stale.

See my [commit](https://github.com/euphi/ESP_Homie_WS2812FX/commit/d55c46d2e7e9d478bd3b3081fd9785ee320156b0) for details
(the commit also includes some other minor changes).
