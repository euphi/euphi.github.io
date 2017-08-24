# Homeautomation

## Overview
My private long-term project is a fully homemade home-automation system.
Core of the the system is a [openHAB](https://www.openhab.org) server that integrates a bunch of  [Homie](https://github.com/marvinroger/homie)-devices running on [ESP8266](https://github.com/esp8266/Arduino).

## Integrations

All my current projects are based on Homie-ESP8266:

### Finished
  * Thermostat + LED-Controller (autonomous)  
    * Sensors & Actors: HTU21D (Temperature + Humidity)
    * Location: Kitchen

### Running, but not yet feature-complete
  * Thermostat + LED-Controller - missing feature: user interaction, e.g. touch, professionally-manufactored PCB for deployment in all rooms)
    * Location: 2 Office rooms, planned: other rooms incl. bathrooms
  * [Irrigation Control](thing_balkoncontroller.html) (includes LED-Controller) 
    * Location: Balkony
    * Sensors & Actors: BMP180 (Temperature + Air pressure), PCF8575 (Multi-IO) + Relay-Board/MOSFETs, External: Magnetic Valves; PWM output for LED
    * Missing feature: autonomous timers, water level of tank, automatic filling of tank.
  * Heating Contoller (missing: better integration, integration of door alert)
    * Location: Entrance (Distribution Box)
    * Actors: PCF8575 Multi-IO to controll one 8xRelais-Board + one 4xRelais-Board
    * Missing feature: Read in contacts from doors to support door alarm system
  
### Work in progress (runs on lab-desk):
  * Touch Controller (using MPR121)

### Ideas / Concept
  * Information display based on the [OpenWordClock](https://github.com/fablabnbg/OpenWordClock)-Design

## Integration of other devices
  * Fibaro Smoke Detectors (Z-Wave)
  * Yamaha AVR Receiver

## Integration of Services
  * WeatherUnderground
  * Telegram

