# Installation, einrichten und erste Schritte mit Jython für openHAB2

## Installation

* Ubuntu kommt mit einem jython package, aber das scheint auf openjdk aufzusetzen, aber leider nicht auf der aus performance-Gründen empfohlen "zulu"-Version.
* Deswegen sollte man jython von [jython.org](http://www.jython.org/downloads.html) herunterladen und installieren:
  > wget http://search.maven.org/remotecontent?filepath=org/python/jython-installer/2.7.0/jython-installer-2.7.0.jar
  > sudo java -jar jython-installer-2.7.0.jar   
* _to be continued_

## Einrichtung

* _to be continued_

## Erste Schritte

### Installation Hilfsfunktionen

* Auf github findet sich das Projekt [openhab2-jython](https://github.com/steve-bate/openhab2-jython), welches einige Module und Skripte zur Verfügung stellt, die den Umgang mit jython wesentlich vereinfachen.
* Aktuell gibt es keine Installation- oder Update-Routine. Deswegen ist es am einfachsten, das Repository lokal in einem Arbeits-/Download-Verzeichnis zu klonen:
  > git clone https://github.com/steve-bate/openhab2-jython.git
* .. und anschließend die benötigten Skripte in sein openhab2-Konfigurationsverzeichnis zu kopieren.
* _to be continued_

### Das erste Skript

* Skripte werden beim Laden der Skript-Datei automatisch einmal ausgeführt. Geladen 

### API-Doku

* Ganz wichtig: Jython erlaubt den vollen Zugriff auf die Eclipse Smarthome API. Die Doku dazu findet sich [hier](http://www.eclipse.org/smarthome/documentation/javadoc/index.html).
* Das ist einerseits großartig, weil man damit praktisch direkt "in openHAB" programmiert und so alle Möglichkeiten hat, andererseits ist die API so natürlich auch sehr umfangreich und komplex.
