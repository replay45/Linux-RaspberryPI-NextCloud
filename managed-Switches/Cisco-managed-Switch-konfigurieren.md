# [Cisco](https://www.cisco.com/) managed Switch konfigurieren

`Anleitung verfasst am 28.3.2026, zuletzt bearbeitet am 28.9.2026`

`Anleitung getestet mit einem Cisco Catalyst Switch aus der 2960X/XR-Reihe und Cisco IOS-Software`


### Hinweis zum seriellen Konsolen-Anschluss
- Managed Switches/Router verwenden in der Regel serielle Anschlüsse, die als "Console" betitelt werden.
- Diese Anschlüsse sind entweder als [RJ45](https://de.wikipedia.org/wiki/RJ-Steckverbindung), manchmal auch als [mini-USB-Port](https://de.wikipedia.org/wiki/Universal_Serial_Bus), oder bei älteren Modellen als [RS-232/DB9](https://de.wikipedia.org/wiki/RS-232) verfügbar.
- Da beim klassischen Konsolen Kabel die Pins vertauscht werden, ist es wichtig, dass niemals der LAN-Anschluss am PC/Laptop für den Konsolen-Anschluss verwendet wird, ansonsten können die Geräte beschädigt werden.
- Der RJ45-Anschluss muss in den Consolen-Port des Switches und der `USB-Anschluss in den Client`.


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
    - Die Baudrate kann man wie folgt angeben: `$ minicom -D /dev/ttyUSB0 -b BAUDRATE`, z.B: `$ sudo minicom -D /dev/ttyUSB0 -b 9600`

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

- Switch neustarten
```
> reload
```

- alle Switches im Stack neustarten
```
> reload /all
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

- In den Konfigurationsmodus wechseln:
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
- Die gewünschte Firmaware-Version herunterladen, am besten die `"stable-version"` auswählen [cisco.com](https://www.cisco.com/c/en/us/support/switches/category.html).
- Dabei auf das genaue Modell des Switches achten, evtl. muss ein Benutzerkonto angelegt werden.
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


----------------------------------------------------------------------------------------------------


# 10. Portmodes & Portfast


### Welche Portmodes gibt es ?
- `dynamic - auto`
    - Zunächst stehen wahrscheinlich nach standardkonfiguration alle Ports am Switch auf "dynamic - auto".
    - Das ist jedoch ein potenzielles Sicherheitsrisiko, denn der Switch verhandelt per DTP (Dynamic Trunking Protocol) den Modus. Angreifer könnten dabei versuchen den Port zum Trunk-Port zu machen, wodurch dieser sich unerlaubten Zugriff auf alle VLANs machen könnte.
    - Daher sollte `KEIN` Port im default-Zustand auf "dynamic" bleiben ! Ports sollten standardmäßig als "Access-Ports" konfiguriert werden.

- `Access Ports`
    - Access Ports senden/empfangen nur untagged-Traffic (+optional das Voice-VLAN) - mehr zu Voice-VLANs bei Punkt "12. Voice-VLAN".
    - Ports sollten standardmäßig als Access Ports konfiguriert sein.

- `Trunk`
    - Der Trunk-Modus wird benötigt, um mehrere VLANs über einen einzelnen Port zu transportieren.
    - Trunkports werden in der Regel für WLAN-AccessPoints oder als Uplink zu anderen Switches oder einer Firewall/Gateway verwendet.
    - Dabei muss natürlich das Gegenüber auch entsprechend einen Trunk-Port bieten und den VLAN-Traffic taggen können.
    - Das Native VLAN sollte aus Sicherheitsgründen auf ein nicht verwendetes VLAN gesetzt werden und auf dem Trunk NCIHT unter den erlaubten VLANs geführt werden !
    - Da das Native VLAN keine aktiven Teilnehmer haben sollte, kann darüber kein unbefugter Traffic ins Netzwerk gelangen.
    

### Was ist Portfast ?
- Portfast ist eine Funktion, um STP (Spanning-Tree-Protocol)-Funktionen auf Ports für Edge-Devices (also Endgeräte), wie PCs, Laptops, Server, Drucker etc. zu deaktivieren.
- Denn wenn ein Gerät an den Switch per LAN angeschlossen wird, startet STP. Erst wenn STP durchgelaufen ist, wird der Port freigeschaltet.
- Edge-Devices (also Endgeräte) unterstützen allerdings kein STP und daher dauert es ca. 30s bis der Port frei ist und der Client eine Verbindung aufbauen kann.


### Auf welchen Ports sollte man Portfast aktivieren ?
- Grundsätzlich sollte Portfast auf allen `Access Ports`, für `Clients, die KEIN STP unterstüzen, aktiviert` werden. Das wären z.B. PCs, Laptops, unmanaged Switches etc.
- Lediglich auf den Uplinks zu anderen smart/managed-Switches oder Routern/Firewalls/Gateways sollte Portfast NICHT aktiviert werden.


### Welche STP (Spanning-Tree-Protocol)-Funktionen sind empfohlen (Übersicht) ?
- STP-Modus: `rapid-pvst`
    - Überprüfen mit `# show spanning-tree summary | include mode`
    - rapid-pvst ist der Standard für fast alle Netzwerke.

- Für Edge-Ports (access Ports für Clients):
    - Portfast: `enabled` - Schnelle Verfügbarkeit für Endgeräte, blockiert BPDUs (STP ist deaktiviert)

- Für Uplinks/Trunk-Ports z.B. zu anderen smart/managed-Switches:
    - Portfast: `disabled` - STP ist aktiv


### Portfast Status-überprüfen (IOS-Software)
- Prüfen, ob & für welche Ports, Portfast aktiv ist:
    - Wenn keine Ausgabe erscheint ist Portfast nicht aktiv.
```
> en
# show spanning-tree | include Portfast
# show running-config
```

- Detaillierte Informationen zu Portfast pro Port:
    - Den Platzhalter `PORT` mit dem entsprechenden Port ersetzen.
```
# show spanning-tree interface PORT detail
z.B.
# show spanning-tree interface GigabitEthernet1/0/1 detail
```

- Übersicht aller STP-Port-Eigenschaften
```
# show spanning-tree summary
# show interfaces status
```

### Portfast auf allen "access Ports" aktivieren (IOS-Software) - CLI
```
> en
# conf t
```

- Der folgende Befehl aktiviert Portfast auf allen "access Ports"
    - Auf Trunk-Ports wird Portfast nicht aktiviert !
    - Portfast muss außerdem manuell auf allen Uplinks zu anderen smart/managed-Switches deaktiviert werden.
```
# spanning-tree portfast default
```

- Um Portfast auf bestimmten Ports wieder zu deaktivieren
```
# interface GigabitEthernet1/0/1
# spanning-tree portfast disable
```

- Speichern
```
# copy running-config startup-config
```

### Portfast in der WebUI
Alternativ kann man Portfast auch in der WebUI aktivieren/deaktivieren.

- In der WebUI unter `Konfiguration > Ports` kann man die einzelnen Ports anwählen.
- Unter `Erweiterte Einstellungen` kann man nun STP für den ausgewählten Port konfigurieren.

- Für Endgeräte (Edge-Devices):
    - `STP-Porttyp`: `Edge`
    - "Anwenden"

- Für Uplinks zu anderen smart/managed-Switches:
    - `STP-Porttyp`: `Deaktivieren`
    - "Anwenden"

- Änderungen speichern (Speichersymbol oben rechts in der WebUI um in startup-config zu speichern)


-----------------------------------------------------------------------------------------------------


# 11. Trunk-Ports für mehrere VLANs
- Wenn mehrere VLANs über einen pyhsichen Port laufen sollen, da z.B. ein WLAN-AccessPoint angeschlossen ist, der mehrere WLANs/SSIDs in unterschiedlichen VLANs bereitstellt, müssen diese über den gleichen Port laufen.
- Dabei muss beachtet werden, dass entweder ein dedizierter Uplink-Port, z.B. zu einer Firewall/Gateway benötigt wird, der dann auf das entsprechende VLAN konfiguriert wird oder ein bereits vorhandener Uplink Port zur Firewall/Gateway ebenfalls als Trunk-Port konfiguriert wird.


### Trunk-Port in der WebUI einstellen
- WebUI öffnen
- Den entsprechenden Port identifizieren und unter `Konfiguration > Ports` den entsprechenden Port auswählen.
- Zunächst unter `Porteinstellungen` `Portfast deaktivieren` oder unter `Erweiterte Einstellungen` `STP-Porttyp` auf `Deaktivieren` setzten.
- Danach unter `Porteinstellungen` den `Switch-Modus` auf `trunk` setzten.
- Für die VLANs bei `zulässiges VLAN` `VLAN-IDs` anwählen, um unter `VLAN-IDs` die VLANs einzugeben (z.B. `10,20,30`)
- Das `Native VLAN` ist das VLAN in das jeglicher ungetaggte Traffic landet, wenn also gewünscht ist, dass aus Sicherheitsgründen ungetaggter Traffic in einem nicht verwendeteten VLAN landet (damit er verworfen wird), kann man den Wert auf z.B. `999` setzen.
- "Anwenden"
- Änderungen speichern (Speichersymbol oben rechts in der WebUI um in startup-config zu speichern)


### Hinweis zu Geräte die kein VLAN-Tagging unterstützen
- Es gibt einige Geräte die kein VLAN-Tagging unterstützen, für die man typischerweise jedoch in ein VLAN einrichten möchte, wie z.B. Drucker.
- Für diese Geräte muss man am Switch den Portmodus (switchport mode) auf `access` setzen, damit der Switch den Traffic an dem Port tagged, sodass dann z.B. der Uplink zum Gateway über einen Trunk-Port laufen kann.


-----------------------------------------------------------------------------------------------------


# 12. [Link Aggregation](https://de.wikipedia.org/wiki/Link_Aggregation) - LACP
- Was ist Link Aggregation/LACP
    - LACP (Link Aggregation Control Protocol) ist eine logische Zusammenfassung mehrer physischer Ports zu einem einzigen logischen Port.
    - Das Ziel dabei ist, die Bandbreite zu erhöhen und Redundanz zu schaffen.
    - Die Bandbreitenerhöhung funktioniert nur bei mehreren Datenströmen, durch viele Geräte/Clients, da so die Last auf die logischen Ports aufgeteilt werden kann, bei einem einzelnen Client würde LACP keine Bandbreitenerhöhung erfolgen, da die Leitung z.B. pro Mac-Adresse (Layer2) verwendet wird.
    - Beispiel: 2x 1Gbit/s-Ports wird mit LACP zu einem logischem Port mit bis zu 2Gbit/s.
    - Aber LACP bietet auch den Vorteil der Redundanz, wodurch der Uplink aufrechterhalten werden kann, wenn eine Leitung z.B. durch einen Defekt ausfällt.
    - Außerdem werden Wartungsarbeiten an den Leitungen etc. durch LACP vereinfacht, da immer ein Uplink verfügbar bleiben kann.
    - Bei Cisco wird für LACP auch oftmals das Synonym `"Etherchannel"` verwendet.


### LACP-Konfigurastionen in der WebUI überprüfen
- WebUI öffnen
- Unter `Monitoring > Ports` > `Port-Channel-Übersicht` sollten alle LACP-Portgruppierungen angezeigt werden.


### LACP über die WebUI einstellen
- WebUI öffnen
- Bei Cisco Catalyst Modellen: `Konfiguration > Ports`
- `STRG`-Taste gedrückt halten und die zwei Ports auswählen
- Unter der Anzeige des/der ausgewählten Port(s) prüfen, ob die korrekten Ports angewählt wurden.
- Nun unter `Porteinstellungen > Portgruppennummern` eine Nummer festlegen, z.B. `1` (diese muss auf beiden Switches identisch sein)
- "Anwenden"
- Der Portgruppentyp sollte `LACP` sein.
- Nun sollten die Ports in WebUI immer zusammen angezeigt werden.
- Unter `Monitoring > Ports` > `Port-Channel-Übersicht` sollten alle LACP-Portgruppierungen angezeigt werden.
- Wenn über die LACP-Ports mehrere VLANs laufen sollen, muss der Port als `Trunk-Port` konfiguriert werden - mehr dazu unter Punkt "11. Trunk-Ports für mehrere VLANs".
- Änderungen speichern (Speichersymbol oben rechts in der WebUI um in startup-config zu speichern)
- Die Einstellungen müssen auf der entsprechenden Gegenseite, z.B. ein auf einem anderern Switch, ebenfalls vorgenommen werden, dabei den gleichen Wert für die LACP-Portgruppennummer verwenden.


### physisch verbinden & Status prüfen
- Nun die beiden konfigurierten Geräte mit den entsprechenden Ports physisch verbinden.
- In der WebUI dafür unter `Services > CLI` folgende Befehle nutzen, um den Status einzusehen:
- Portchannel überprüfen
    - `D` = down
    - `P` = bundeld
```
# show etherchannel X summary
z.B. # show etherchannel 1 summary

# show interfaces Port-channelX
z.B. # show interfaces Port-channel1
```

- Nun können noch Stabilitätstest vorgenommen werden, z.B. ein Client an einen Access-Port anschließen und testen, ob es zu Ausfällen kommt, wenn eine Leitung der LACP-Portgruppierung getrennt wird. Dabei hilft unter anderem auch der "Ping"-Befehl auf einem beliebigen Betriebssytem.
- Dabei können die Meldungen unter "Warnungen" in der WebUI auf ungewöhnliche Warnungen geprüft werden.


### Wichtig:
- Auf der Gegenseite, z.B. ein Server mit mehreren Netzwerkkarten oder ein smart/managed-Switch muss ebenfalls LACP konfiguriert sein/werden !


-----------------------------------------------------------------------------------------------------
