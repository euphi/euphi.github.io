---
layout: page

---

# Halterung Forumslader

To charge my mobile or other batteries during bicycle trips, I ordered the great ["Forumlader"](www.forumslader.de).
The forumslader is connected to the hub (front wheel) generator. A microcontroler adapts the power electronics (a bridge rectifier plus some capacitors) to the speed (frequency), so it gets more power than specified from the generator. Enough to charge the phone (5V, 1A DCDC converter) and a buffer battery.
It has an bluetooth modul that transmits information about the speed (based on frequency), battery currents, gradient (based on speed and change of air pressure) etc. to an app running on the mobile.

I got the variant with 4 batteries, so I was not able to build the "compact" variant that fits inside the steering.

Therefore I ordered some cheap IP68 4-pin connectors from China and built a laser-cuttable box ([boxes.py](http://www.festi.info/boxes.py/)). To mount this box to my bicycle, I created an adapter with FreeCAD.
