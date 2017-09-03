---
layout: thing
thing: HeizungController
projectlink: https://github.com/euphi/ESP_Heizungscontroller
prjstate: ready
features:
  - Controlls 12 Relays connected to magnetic valves for underfloor heating
  - Reads in 1 door contact to monitor front door
  - Buzzer to give alert feedback
electronics:
  - PCF8575 (16x I/O Controller on I2C)
  - ESP8216-ADC
  - Status LED
ems:
  - 8x Relais-Board (with optocoupler)
  - 4x Relais-Board (with optocoupler)
  - Buzzer
  - Reed Contact
  - WAGO clamps for connection supply and wire to valves (230V AC!)
  - Magnetic valves (were already installed as part of floor heating system)
hws:
  - DIN Rail
  - clamps for DIN rail
sws:
  - hnc
  - homie-esp
comments: true
---

This device is installed inside the service cabinet of apartment's floor heating system.
The magnetic valves were already installed and use 230V AC supply that was switch by simple bimetal thermostat in each room.
I started to remove the thermostat and replace them by my [ESP_Thermostat](thing_thermostat.html). 
So, I installed a DIN rail in the service cabinet and installed a 12V PSU to provide 12VDC instead of 230VAC to the rooms already equipped with new thermostats.

The HeizungController devices is also installed on DIN rail on a simple lab PCB.

As the device is quite close to the front door, I also connected a reed contact that is mounted on the frame of the front door, so it can be used as simple alarm system.
