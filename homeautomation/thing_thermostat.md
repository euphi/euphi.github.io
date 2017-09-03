---
layout: thing
thing: ThermostatCtrl
projectlink: https://github.com/euphi/ESP-LEDCtrl
prjstate: beta
features:
  - Measurement of Temperature and Humidity
  - 2xRBG-Dimmer
  - LED-Dimmer (white)
electronics:
  - ESP12F
  - HTU21D
  - ULN2003
  - MOSFET
ems: None
hws: Adapter for in-wall junction box (3d-printed)
sws:
  - hnc
  - Homie-ESP8266
comments: true
---

This device is a variant of my LedController + Thermostat based on HomieNodeCollection.
It is planned to integrate some user interface, preferable by touch with MPR121 controller. However, the touch control is still in some experimenting phase.

To mount the device in place of the former simple bimetall thermostats, i created an adapter ring to be 3d-printed, that can be mounted on top of in-wall junction box.
The PCB fits inside the box, so the ring adds some distance between PCB or wall to the HTU21D Temperature sensors. The ring also has some vents to improve air flow. The current desing leads to approx 1K higher Temperature than ambient Temperature in the room. This is quite acceptable, especially because the Thermostat can be adjusted to it (relative Temperature change is more important).
