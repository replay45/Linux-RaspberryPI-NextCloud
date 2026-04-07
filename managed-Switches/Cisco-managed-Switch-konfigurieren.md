# [Cisco](https://www.cisco.com/) managed Switch konfigurieren

`Anleitung verfasst am 28.3.2026`

`Anleitung getestet mit einem Cisco Catalyst Switch und Cisco IOS-Software`


### Hinweis zum seriellen Konsolen-Anschluss
- Managed Switches verwenden in der Regel serielle Anschlüsse, die als "Console" betitelt werden.
- Diese Anschlüsse sind entweder als [RJ45](https://de.wikipedia.org/wiki/RJ-Steckverbindung) oder manchmal auch als USB-Port verfügbar.
- Da beim klassischen Konsolen Kabel die Pins vertauscht werden, ist es wichtig, dass niemals der LAN-Anschluss am PC/Laptop für den Konsolen-Anschluss verwendet wird, ansonsten können die Geräte beschädigt werden.
- Der RJ45-Anschluss muss in den Consolen-Port des Switches und der USB-Anschluss in den Client.


-----------------------------------------------------------------------------------------------------


# 1. Konsole aufrufen (Linux)
- Für Windows kann das Programm [PuTTY](https://putty.org/index.html) verwendet werden.

### Minicom installieren & USB-Verbindung prüfen
- Linux-Client mit Konsolenport des Switches über ein geeignetes Konsolenkabel anschließen.
- Termianl ([CLI](https://de.wikipedia.org/wiki/Kommandozeile)) auf dem Linux-Client öffnen.
- `minicom` installieren (Debian/Ubuntu):
```
$ sudo apt update
$ sudo apt install minicom
$ minicom --version
```

- Prüfen, ob Switch korrekt über USB-to-Serial-Adapter an Linux-Client angeschlossen ist:
    - Um Fehlerquellen zu vermeiden, alle nicht benötigten USB-Verbindungen trennen/entfernen.
    - Es sollte eine Ausgabe erscheinen, die wie folgt aussehen könnte: `/dev/ttyUSB0`
```
$ ls /dev/ttyUSB*
```

### Minicom starten
- Minicom starten
    - Falls eine Fehlermeldung erscheint, z.B. `... is locked`, dann sollten alle anderen Terminal-Sessions/ Terminalprogramme beendet werden.
    - Wenn die Verbindung korrekt hergestellt wurde, dann sollte die Willkommensmeldung von minicom erscheinen.
    - In der Willkommensseite einfach eine beliebige Taste drücken. Falls das nicht geht, minicom verlassen und die Baudrate überprüfen.
```
$ sudo minicom -D /dev/ttyUSB0
```

- Es sollte nun die Konsole des managed Switch geöffnet sein.
- Falls nicht, muss vielleicht die `Baudrate` angegeben werden.
    - Die Baudrate steht in der Regel im Handbuch des Switches (häufig 9600, 38400 oder 115200).
    - Die Baudrate kann man wie folgt angeben: `$ minicom -D /dev/ttyUSB0 -b BAUDRATE`, z.B: `$ minicom -D /dev/ttyUSB0 -b 9600`

- Minicom verlassen
    - Um Minicom zu verlassen, `STRG+A`, dann `X` und `Enter` drücken.
    - Eine weitere wichtige Tastenkombination ist `STRG+W` um den Zeilenumbruch ein-/auszuschalten.


-----------------------------------------------------------------------------------------------------


# 2. Ersteinrichtung
- Wenn der Switch auf Werkseinstellungen gesetzt ist, fragt er zunächst, ob der Konfigurationsdialog geladen werden soll, also ob man durch die Einrichtung geführt werden soll. Es wird immer empfohlen `no` auszuwählen und die Konfiguration manuell vorzunehmen.


- Hostnamen setzen
```
> en
# config terminal
# hostname NEUER-HOSTNAME
# copy running-config startup-config
# show startup-config
```

- Passwort für den privilegierten Modus setzen
```
# config terminal
# enable secret NEUES-PASSWORT
# copy running-config startup-config
# show startup-config
```

- Passwort entfernen (nicht empfohlen!)
```
> en
# config terminal
# no enable secret
# copy running-config startup-config
# show startup-config
```


#### User-/Priviligierter-Modus
- Standardmäßig befindet man sich im User-Modus, gekennzeichnet durch `>`.
- Der priviligierte-Modus ist durch in `#` gekennzeichnet.

- Um in den privilegierten-Modus zu kommen:
```
> enable
oder
> en
```

- Um aus dem privilegierten-Modus in den User-Modus zurückzugelangen:
```
> exit
```

-----------------------------------------------------------------------------------------------------


# 3. Befehle für die [Cisco](https://www.cisco.com/) [CLI](https://de.wikipedia.org/wiki/Kommandozeile)
- Hilfe-Befehl:
```
> ?
```

- Switch ausschalten/herunterfahren
    - mit "yes" bestätigen
```
> reload
```

- Zeigt IOS-Version, Modell, Uptime und mehr:
```
> show version
```

- Um aus der Shell auszuloggen:
    - Danach über minicom `STRG+A`, `X`, `Enter`
```
> logout
oder
> exit
```

- Zeigt Hardware-Informationen (Modell, Seriennummer):
```
> show inventory
```

- Zeigt den Status aller Schnittstellen (Up/Down, Geschwindigkeit, Duplex):
```
> show interfaces status
```

- Zeigt direkt verbundene Cisco-Geräte (falls CDP aktiviert ist):
```
> show cdp neighbors
```

- In den Konfigurationsmodus:
```
# configure terminal
oder
# conf t
```


#### [Mac-Adressen](https://de.wikipedia.org/wiki/MAC-Adresse)
- Zeigt die MAC-Adresstabelle (welche MACs an welchen Ports hängen):
```
> show mac address-table
```

- Zeigt nur dynamisch gelernte MAC-Adressen:
```
> show mac address-table dynamic
```

- Zeigt Switchport-Informationen (VLAN, Mode, MAC-Adressen):
```
> show interface switchport
```


#### [IP-Adressen](https://de.wikipedia.org/wiki/IP-Adresse)
- Zeigt IP-Adressen und Status aller Schnittstellen (VLANs, Ports):
```
> show ip interface brief
```

- Zeigt die Routing-Tabelle an:
```
> show ip route
```

- Zeigt Details zu VLAN 1 (Management-VLAN):
```
> show interface vlan 1
```

#### Web-Oberfläche (prüfen, ob aktiv/inaktiv ist)
- IP-Adresse der Management-Schnittstelle prüfen:
```
> show ip interface brief
```


-----------------------------------------------------------------------------------------------------


# 4. statische [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) im Switch setzen
- In den privilegierten Modus wechseln
```
> en
```

- In den configure terminal Modus wechseln
```
# configure terminal
```

- statische IP für das Interface vlan 1 setzen:
    - Wenn gewünscht ggf. das vlan X anpassen.
```
# interface vlan 1

# ip address IP-ADRESS SUBENTMASK
z.B.
# ip address 192.168.1.10 255.255.255.0
```

- Interface/Schnittstelle einschalten:
```
# no shutdown
```

- Terminal-Modus verlassen
```
# exit
```

- Konfig speichern:
```
# copy running-config startup-config
```

- Start-Konfig prüfen:
```
# show startup-config
```

- IP-Adresse überprüfen
```
# show interface vlan 1
# show dhcp lease
# show running-config interface vlan 1
```


# 5. WebUI aktivieren
- In den privilegierten Modus wechseln
```
> en
```

- In den configure terminal Modus wechseln
```
# configure terminal
```

- HTTP-Server aktivieren:
```
# ip http server
```

- HTTPS-Server aktivieren:
```
# ip http secure-server
```

- Authentifizierungsmethode für die WebUI festlegen:
```
# ip http authentication local
```

- Terminal-Modus verlassen
```
# exit
```

- Konfig speichern:
```
# copy running-config startup-config
```

- Start-Konfig prüfen:
```
# show startup-config
```

- IP-Adresse überprüfen
```
# show interface vlan 1
# show dhcp lease
# show running-config interface vlan 1
```

### Benutzer für die WebUI erstellen

- In den privilegierten Modus wechseln
```
> en
```

- In den configure terminal Modus wechseln
```
# configure terminal
```

- Benutzer erstellen
    - Privilege 15 gibt dem Benutzer volle Admin-Rechte.
    - Secret speichert das Passwort verschlüsselt.
```
# username HIER-DEIN-USERNAME privilege 15 secret HIER-DEIN-PASSWORT
```

- Authentifizierungsmethode für die WebUI festlegen:
```
# ip http authentication local
```

- Terminal-Modus verlassen
```
# exit
```

- Konfig speichern:
```
# copy running-config startup-config
```

- Start-Konfig prüfen:
```
# show startup-config
```

- Nun PC/Laptop über ein LAN-Kabel an einen Port des Switches anschließen (KEIN Management(MGMT)-Port, ein normaler Port)
- Im Browser die IP-Adresse des Switches eingeben: `https://IP-ADRESSE`
- Falls der Switch noch nicht am Netzwerk angeschlossen ist, ist es ggf. notwendig, temporär im PC/Laptop in den Etherneteinstellungen eine feste IP und Subnetmask einzustellen. Dabei sollte natürlich die gleiche Subnetmask und der gleiche IP-Adressbereich, wie sie im Switch eingestellt sind, verwendet werden.
- Danach nochmal versuchen, die WebUI aufzurufen.


-----------------------------------------------------------------------------------------------------


# 6. Konfiguration sichern - Konfigurations-Backup

### Konfiguration sichern - WebUI
- aktuelle Konfiguration über die WebUI sichern
    - Dafür muss die WebUI auf dem entsprechenden Switch aktiv sein, dieser benötigt zudem auch eine IP-Adresse dafür.
    - PC/Laptop per LAN mit dem Switch verbinden und WebUI im Browser öffnen.
    - Nun zu `Administration > Management > Backup & Restore` navigieren.
- Um Backup zu erstellen/ zu sichern: 
    - Copy: `from Device`
    - File Type: `Configuration`
    - Transfer Mode: `HTTP`
    - `Download File`
- Um Backup wiederherzustellen:
    - Copy: `to Device`
    - File Type: `Configuration`
    - Transfer Mode: `HTTP`
    - Backup existing startup config to flash? `Yes`
    - `Select File`

### Konfiguration sichern - Console (mit USB-Stick)
- Aktuelle Konfiguration auf USB-Stick speichern
    - Dafür einen handelsüblichen USB-Stick nehmen und an einen USB-Port am Switch anschließen.
    - Der USB-Stick muss FAT32 formatiert sein (Cisco-Switches unterstützen kein NTFS oder exFAT)
- Aktuelle Konfiguration über Console sichern
    - PC/Laptop per seriellem Anschluss (Console) anschließen (alternativ in der WebUI den Consolen-Tab öffnen) und Befehl verwenden, um Konfig zu sichern.
    - Konsole öffnen.
    - Prüfen, ob der Switch den USB-Stick erkennt:
```
> en
# dir usbflash0:
```

- Konfiguration sichern:
```
# copy running-config usbflash0:backup_runningconf_cisco_switchXY.cfg
und/oder
# copy startup-config usbflash0:backup_startconf_cisco_switchXY.cfg
```

- Optional überprüfen:
    - Hier sollte nun die Konfiguration aufgelistet sein.
```
# dir usbflash0:
```

- Sobald alle Dateiübertragungen abgeschlossen sind kann der USB-Stick einfach entfernt werden.


-----------------------------------------------------------------------------------------------------


# 7. Software-Update durchführen - [Cisco IOS](https://www.cisco.com/site/us/en/products/networking/software/ios-nx-os/index.html)
- Zunächst ein Backup der Konfiguration erstellen und sichern.
    - Die Schritte dazu werden in Punkt 6. erläutert.

- Hinweis zu Switches im Stack:
    - Einstellungen nur am Master vornehmen, dieser wird dann die neue Firmware an die anderen Mitglieder im Stack verteilen.

### Speicherplatz auf dem Switch prüfen
- Die Switches haben nur begrenzten Speicherplatz.
    - Falls nicht ausreichend Speicherplatz vorhanden ist, kann es zu Instabilität oder zu Datenverlust kommen.

- Speicherplatz prüfen
    - Außerdem sollte der noch freie Speicherplatz angezeigt werden. Dieser kann dann mit der neuen Firmware-Datei abgeglichen werden.
    - Es sollte auch auf ausreichend Puffer geachtet werden, sodass immer ein wenig Speicher frei bleibt.
```
> en
# show flash:
```

### IOS-Software herunterladen
- Die gewünschte Firmaware-Version herunterladen, am besten die `"stable-version"` auswählen.
- Dabei auf das genaue Modell des Switches achten.
- Die genaue Modellbezeichnung findet man auf dem Switch selbst als auch in der WebUI. Alternativ kann man sich das Modell auch in der Console mit `> show version` anzeigen lassen.
- Sofern die WebUI verwendet wird, sollte man auch die Version `...with webui...` herunterladen.

### Software-Update durchführen mit WebUI
- Nun in der WebUI `Allgemeine Einstellungen > Software Update` öffnen.
    - Dateityp: `IOS- und Web-UI`
    - Datei auswählen
    - `Update starten`
    - mit `OK` bestätigen
    - Nun abwarten, der Vorgang kann mehrere Minuten in Anspruch nehmen, die WebUI `NICHT` schließen !
    - Falls der Browser eine Meldung bringt mit "Page Unresponsive" diese einfach dort lassen/ignorieren, weder auf wait noch auf exit klicken.
    - Nach einiger Zeit kann man dann auf wait klicken, damit die Seite neu laden kann und man sich anmelden kann.

- Falls an dieser Stelle die WebUI nicht richtig initialisiert wird:
    - PC/Laptop per Console anschließen und neu starten:
```
> en
# reload
```

- Nach dem Update die Version prüfen.
```
> show version
```


-----------------------------------------------------------------------------------------------------


# 8. Stacking
- Mehrere Switches als Stack konfigurieren.
- Mit Cisco IOS-Software.


### Hostnamen & Softwareversion
- Zunächst sollte überprüft werden, ob alle Switches einen eindeutigen Hostnamen haben.
- Außerdem sollten alle Switches die gleiche IOS-Softwareversion haben.
- Wenn das nicht der Fall sein sollte, dann sollte das entsprechend angepasst werden.


### IP-Adressen, Benutzer, WebUI - Stackmitglieder, NICHT-Master-Switch
- Nur der zukünftige Master-Switch sollte eine feste IP-Adresse haben. Die anderen Mitglieder des Stacks erhalten dann eine IP vom Master-Switch.
- IP-Adresse vom Nicht-Master-Switch entfernen:
```
> en
# configure terminal
# interface vlan 1
# no ip address
# exit
# exit
# copy running-config startup-config
```

- Außerdem sollten angelegte Benutzer auf den Stackmitglieder entfernt werden.
    - Der Platzhalter USERNAME muss entsprechend angepasst werden.
```
# configure terminal
# no username USERNAME
# exit
# copy running-config startup-config
```

- Auch die WebUI sollte deaktiviert werden.
```
# configure terminal
# no ip http server
# no ip http secure-server
# exit
# copy running-config startup-config
```


### Stack-Mitglieder IDs
- Jeder Switch im Stack benötigt eine genaue Member-ID.
- Die IDs sollten `vor dem Stacken` zugewiesen werden, sodass Konflikte vermieden werden. Das Ganze muss dann auf jedem Switch einzeln passieren.
    - `Switch 1`: Erster Switch im Stack (Master)
    - `priority XX`: Switch mit der höchsten Priorität (Master), darunter Mitglieder
    - Die Modellnummer des Switches findet sich auf dem Switch selber, aber auch einsehbar mit `> show version | include Model`
- Switch 1 (Master):
```
> en
# configure terminal
# switch 1 renumber 1
# exit
# copy running-config startup-config
# reload
> en
# configure terminal
# switch 1 priority 15
# exit
# copy running-config startup-config
# reload
```

- Switch 2 (Member):
```
> en
# configure terminal
# switch 1 renumber 2
# exit
# copy running-config startup-config
# reload
> en
# configure terminal
# switch 2 priority 14
# exit
# copy running-config startup-config
# reload
```

- Optional, falls vorhanden - Switch 3 (Member):
```
> en
# configure terminal
# switch 1 renumber 3
# exit
# copy running-config startup-config
# reload
> en
# configure terminal
# switch 3 priority 13
# exit
# copy running-config startup-config
# reload
```

- Überprüfen:
    - Solange die Switches noch nicht physisch gestackt sind, sind sie auch mit niedriger Priorität und Nummer "Master".
    - Daher wird die Master-LED auch noch aktiv sein, bis der Switch im nächsten Punkt nach einem Neustart mit dem Stack verbunden wird.
    - Außerdem ist es absolut normal, dass bei folgenden Befehlen bei den zukünftigen "Member-Switches" ein Switch 1 als Member und provisioned angezeigt wird, das wird dann nach dem physischen Stacken verschwinden.
```
> show switch
> show switch detail
```


### physisch Stacken
- `WICHTIG: Switches ausschalten`.
- Die Stack-Kabel an die Stack-Module anschließen.
- Dabei am besten die Ring-Topologie nutzen.
- `Zuerst den Master-Switch einschalten`.
- Prüfen mit:
```
> show switch stack-ports
```


-----------------------------------------------------------------------------------------------------


# 9. Switch auf Werkseinstellungen zurücksetzen - [CLI](https://de.wikipedia.org/wiki/Kommandozeile) (IOS-Software)
- Vor dem Zurücksetzen sollte ein Backup der Konfiguration erstellt werden !

[Mehr Informationen zum Zurücksetzen auf cisco.com](https://www.cisco.com/c/de_de/support/docs/lan-switching/vlan/217969-reset-catalyst-switches-to-factory-defau.html)

### startup-config löschen
```
> en
# write erase
# reload
```

### VLANs löschen
```
> en
# delete flash:vlan.dat
# reload
```

# kompletter reset:
```
> en
# write erase
# delete flash:vlan.dat
# reload
```

- Nun sollte der Switch auf Werkseinstellungen zurückgesetzt sein.

-----------------------------------------------------------------------------------------------------
