# OpenHAB 2 - Tipps & Tricks

## Regeln mit jython (JSR223)

## Eclipse Smarthome API

### Übersicht
Scripte mit JSR223 haben vollen Zugriff auf die interne API von OpenHAB bzw. Eclipse Smarthome. Die Doku dazu findet sich über [Eclipse Smarthome javadoc](https://eclipse.org/smarthome/documentation/javadoc/overview-summary.html).

### Besonderheiten
  * `events.postUpdate(item, state)` postet ein Update für das angegebene Item. Der neue State *muss* dabei vom Typ `String` (!) sein, nicht etwa vom eigentlich state (z.B. `OnOffType`). State auslesen und an ein anderes Item vom gleichen Typ kopieren, funktioniert also so nicht direkt, sondern nur über den Umweg einer Umwandlung in `String`
