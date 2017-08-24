# Balkony Irrigation Controller

## Overview

* Software: [BalkonController](https://github.com/euphi/BalkonController) based on
  * [Homie-ESP8266](https://github.com/marvinroger/homie-esp8266/)
  * my own [HomieNodeCollection](https://github.com/euphi/HomieNodeCollection)
  * [Arduino-ESP8266](https://github.com/esp8266/Arduino)

* Hardware (Electronics)
  * ESP-12F (ESP-07 would be better, because it supports external antenna)
  * PCF8575 (16x I/O Controller on I2C)
  * BMP180 (I2C-Sensor for temperature and air pressure)
  * 1N4004 diodes (for free-wheeling the relays/valves)

* Hardware (electro-mechanical)
  * 1x + 4x Magnetic Valves
  * Reed Contact with Floater
 
* Hardware (mechanical) 
  * IP54 Case
  * Case for BMP180 Sensor
  * PVC hoses + clamps
