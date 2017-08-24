---
layout: thing
thing: BalkonController
projectlink: https://github.com/euphi/BalkonController
prjstate: proto
features:
  - Controller to supply water to 4 different plant beds
  - Measurement of Temperature and Air Pressure
  - 2xRBG-Dimmer
  - LED-Dimmer (white)
electronics:
  - ESP12F
  - BMP180
  - ULN2003
  - PCF8575 (16x I/O Controller on I2C)
  - 1N4004 (free-wheel diodes for relais/valves)
ems:
  - 2x Relais-Board (with optocoupler)
  - MOSFET
  - Magnetic valves
  - Reed-Contact with floater
hws:
  - water pump
  - IP54-Case
  - Case for Temp/Pressure Sensor
  - PVC hoses + connectors
  - water tanks
sws:
  - "<a href='https://github.com/euphi/HomieNodeCollection'>HomieNodeCollection</a>"
  - Homie-ESP8266
comments: true
---

This device is installed at the balkony and can control a water pump and a 4xvalve to distribute water to our plants.
A BMP180 sensors provides information about temperature and air pressure on I2C and is located in a seperate case in a shady place above the water tanks.
Future improvement is to support an additional valve and reed contacts to automatically fill the water tanks when necessary.
