# Installation von openHAB2 auf einem Orange PI Plus 2 (inklusive Grafana / influxdb)

## Installation Linux

* Ich habe ein aktuelles Armbian Image installiert, siehe (https://www.armbian.com/orange-pi-plus-2/).
  * Dazu einfach das Image auspacken und auf die SD-Karte kopieren (`dd bs=4M if=Image-File.img of=/dev/mmcblk0`) und in den Slot am Orange PI stecken und diesen einschalten.
* Den Boot-Vorgang habe ich mir dann über die UART-Schnittstelle mit einem UART-zu-USB-Wandler angeschaut (Baudrate 115200).
* Interessanterweise hat das Image (und ich habe auch noch einige andere probiert) immer nur bis zur USB-Initialisierung gebootet und ist dann abstürzt.
  * Mit USB-Keyboard am Port ging es dann.
  * Ich habe dann ein Upgrade gemacht (`apt update && apt upgrade`), danach bootet der Orange PI auch "headless", also ohne keyboard.
* Ich habe das Problem etwas detaillierter auf (https://forum.armbian.com/index.php?/topic/5267-orange-pi-plus-2-headless-boot-not-possible-reboot-if-no-keyboard-is-connected/) beschrieben.


## Installation Komponenten

* Armbian kommt schon mit den grundlegenden Tools vorinstalliert, aber die Serveranwendungen 

### openHAB

* Ich habe mich für den nightly-build entschieden, da zum Installationszeitpunkt nur 2.0 (stable) und 2.1 (beta) verfügbar waren, die keinen oder nur eingeschränkten JSR223 ("Jython") Scripting-Support haben. Mit einer 2.2 als Beta oder gar Release empfehle ich diese Varianten zu verwenden.
* Paket-Quelle hinzufügen: `echo deb https://openhab.jfrog.io/openhab/openhab-linuxpkg unstable main > /etc/apt/sources.list.d/openhab2.list`
* Um Fehler wegen fehlender Signatur der Pakete zu vermeiden, muss noch der Schlüssel dazu installiert werden: `wget -qO - 'https://bintray.com/user/downloadSubjectPublicKey?username=openhab' | sudo apt-key add -`
* `apt update && apt install openhab2`
* Außerdem ist ein Installation von java notwendig. openHAB empfiehlt dafür "zulu", also empfehle ich das hier auch mal:
  * Repository-Key hinzufügen: `apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0x219BD9C9`
  * Repository hinzufügen: `apt-add-repository 'deb http://repos.azulsystems.com/ubuntu stable main'`
  * java installieren: `apt install zulu-embedded-8`
* Nun kann man openhab2 starten: `service openhab2 status`

### nginx
* `apt install nginx`

### letsencrypt
* `apt install letsencrypt`

### _Work in progress_ 
* tbc.


## Konfiguration der Komponenten

### openHAB
* openHAB wird über den intergrierten Webserver konfiguriert, also einfach im Browser die IP des Orange PI aufrufen, Port 8080.
* 'Default Setup' auswählen, dann werden ein paar Einstellungen automatisch konfiguriert.
* Nun kann man in der 'Paper UI' unter "Add-Ons" die Add-Ons auswählen, die man benötigt (für meine Beispiele ist das das 'mqtt'-Binding und die 'Telegram'-Action, für die grafische Aufbereitung von Daten "influxdb").
  * Mit 'Bindings' werden Geräte ("Things") aus dem Heimnetz in openHAB eingebunden. Es gibt auch virtuelle Bindings, wie z.B. Astro mit der man die "lokale Sonne" als Thing einbinden kann. Darüber erhält man dann Informationen über Sonnenstand, Uhrzeit Sonnenaufgang usw.
  * Mit 'Actions' kann man mit diversen Diensten kommunizieren, also z.B. Telegram, aber auch Kodi für Benachrichtugen auf dem lokalen TV.
  * Addons für 'Persistence' speichern Daten. Man sollte mindestens MapDB installiern, die immer den letzten Zustand speichert, um diesen nach einen Neustart wiederherzustellen. influxdb oder rrd4j dienen zum Speichern über einen längeren Zeitraum, also für z.B. für Grafiken für Temperaturverläufe.
  * Unter 'Transformations' sind Add-Ons für die Konvertierung von Daten auf dem openHAB event-bus oder im Benutzer-Inferface, also z.B. "Map Transformation" für die Umwandlung von numerischen Werten oder Strings zu (anderen) Strings, z.B. '0' -> "OFF", "true" -> "ON" usw.
  
  


