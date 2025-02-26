# Raspberry Pi - Installation, Einrichtung und Updates


`Anleitung verfasst am 7.5.2024, zuletzt bearbeitet 19.2.2025`


----------------------------------------------------------------------------------------------------------------


`Empfohlen wird eine SD-Karte mit mind. 8 GB.`

`Für diese Anleitung wurde ein Raspberry Pi 5 genutzt, diese sollte jedoch auch für ältere Modelle übertragbar sein.`

`Raspberry Pi OS ist eine Linux Distribution, die auf Debian basiert und von der Raspberry Pi Foundation entwickelt wurde.`


----------------------------------------------------------------------------------------------------------------


## Inhaltsverzeichnis
1. Installation von [Raspberry Pi OS](https://www.raspberrypi.com/software/)
2. IP-Adresse herausfinden
3. System aktualisieren
4. [Firewall](https://de.wikipedia.org/wiki/Firewall) aktivieren - [ufw](https://wiki.ubuntuusers.de/ufw/)
5. Konfigurationen - raspi-config (optional)
6. [Tux Racer](https://tuxracer.sourceforge.net/) - Rennspiel & [Supertux](https://www.supertux.org/) - Jump-'n'-Run
7. [Brave-Browser installieren](https://brave.com/linux/) (optional)
8. Pi-OS: vom automatischen Boot in das Terminal, temporär in UI wechseln


----------------------------------------------------------------------------------------------------------------


# 1. Installation von [Raspberry Pi OS](https://www.raspberrypi.com/software/)

### A. Installation von [Raspberry Pi OS](https://www.raspberrypi.com/software/) über den Imager.
- Installieren des `rpi-imager` auf `Linux`, um das Raspberry Pi OS auf eine `SD-Karte zu flashen`:
    - Den `rpi-imager` von der offiziellen Webseite herunterladen und als Debian-Paket installieren. (Mit [GDebi](https://packages.debian.org/de/stable/gdebi) können Debian-Pakete komfortabel installiert werden.)
    - Oder den rpi-imager über den [Flatpak-AppStore](https://flathub.org/apps/org.raspberrypi.rpi-imager) installieren.

- Der rpi-imager kann `alternativ` auch auf `Windows`, `MacOS` oder auch einem `anderen Raspberry Pi` installiert werden.
- Fortgeschrittene Nutzer können auch direkt über den entsprechenden Raspberry Pi das OS installieren.


### B. Flashen des OS auf die SD Karte
- Die SD-Karte nun in einen PC einsetzen, wo der rpi-imager installiert wurde.
- rpi-imager öffnen
    - das Modell,
    - das passende OS zum Modell und
    - die SD-Speicherkarte auswählen.
- auf `Weiter` klicken
- Danach könnte eine auf Deutsch etwas `verwirrende Meldung erscheinen`, diese mit `NEIN` beantworten und
- die Warnung, dass alle Daten auf der SD-Karte gelöscht werden, mit `JA` bestätigen.
- Der Vorgang kann nun einige Zeit in Anspruch nehmen.
- Sobald der `Vorgang abgeschlossen` ist, kann der `Imager geschlossen` werden und die `SD-Karte` entfernt werden.


### C. Raspberry Pi Ersteinrichtung
- Jetzt die SD-Karte in den Raspberry Pi einstecken.
- booten (Strom anschließen)
- Sprach- und Tastatureinstellungen vornehmen,
- einen Nutzer erstellen,
- LAN-Kabel einstecken oder alternativ den Raspberry Pi mit einem WLAN verbinden
- Standardbrowser wählen (empfohlen Firefox)
- Updates installieren und neustarten


----------------------------------------------------------------------------------------------------------------


# 2. [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) herausfinden
```
$ ifconfig
```
oder
```
$ ip a
```


----------------------------------------------------------------------------------------------------------------


# 3. System aktualisieren

### Paketmanager: [apt](https://wiki.ubuntuusers.de/APT/)
```
$ sudo apt update
```
```
$ sudo apt upgrade
```
```
$ sudo apt dist-upgrade
```
```
$ sudo apt autoremove
```
```
$ sudo apt autoclean
```

- Kombo Befehl:
```
$ sudo apt-get update -y && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y && sudo apt-get autoremove -y && sudo apt-get autoclean -y
```


----------------------------------------------------------------------------------------------------------------


# 4. [Firewall](https://de.wikipedia.org/wiki/Firewall) aktivieren - [ufw](https://wiki.ubuntuusers.de/ufw/)

- Was ist eine [Firewall](https://de.wikipedia.org/wiki/Firewall) ?
    - Eine Firewall kontrolliert den Datenfluss zwischen einem internen und einem externen Netzwerk und blockiert unerwünschten Datenverkehr.
    - Die Funktion einer Firewall simpel erklärt: Wenn eine Anfrage aus dem internen Bereich gesendet und von extern beantwortet wurde, (z.B. einen Aufruf einer Webseite) dann können die entsprechenden Datenpakete die Firewall passieren. Wenn jedoch Datenpakete von extern gesendet werden, ohne, dass danach gefragt wurde, sollte die Firewall diese blockieren.
    - Zusammenfassend schützt eine Firewall vor unerwünschtem Datenverkehr in einem Netzwerk, um die Systeme zu schützen.


[Firewall Manager - ufw](https://wiki.ubuntuusers.de/ufw/):

- Installation & Status
```
$ sudo apt install ufw
$ sudo ufw status
```
- Firewall aktivieren/ deaktivieren/ reseten
```
$ sudo ufw enable
$ sudo ufw disable
$ sudo ufw reset
```

- Mehr zur Firewall und zu Sicherheit auf Linux unter [Linux-RaspberryPI-NextCloud/ Linux/ Sicherheit auf Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)


----------------------------------------------------------------------------------------------------------------


# 5. Konfigurationen - raspi-config (optional)

### Über `raspi-config` können einige Konfigurationen, vorgenommen werden:
```
$ raspi-config
```
```
1 System Options
2 Display Options
3 Interface Options
4 Performance Options
5 Localisation Options
6 Advanced Options
8 Update
9 About raspi-config
```
- Durch das entsprechende Menü navigieren und Einstellungen vornehmen.
	
----------------------------------------------------------------------------------------------------------------


# 6. [Tux Racer](https://tuxracer.sourceforge.net/) - Rennspiel & [Supertux](https://www.supertux.org/) - Jump-'n'-Run

In den beiden Spielen muss man das Linux-Maskottchen `Tux` steuern.

- Tux Racer
```
$ sudo apt install extremetuxracer
```

- Supertux
```
$ sudo apt install supertux
```

- Mehr zu den beiden Spielen und weiteren Spaß-Projekten unter [Projekt-Sammlung-und-Fun-Projekte](https://github.com/replay45/Projekt-Sammlung-und-Fun-Projekte/tree/main/Spa%C3%9F-Projekte)

----------------------------------------------------------------------------------------------------------------


# 7. [Brave-Browser installieren](https://brave.com/linux/) (optional)
```
$ sudo apt install curl
$ sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
$ echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list
$ sudo apt update
$ sudo apt install brave-browser
```


----------------------------------------------------------------------------------------------------------------


# 8. Pi-OS: vom automatischen Boot in das Terminal, temporär in UI wechseln
- Wenn der Raspberry Pi automatisch in das Terminal bootet kann der Befehl verwendet werden, um temporär in die UI zu wechseln.

### Bedingung: Die UI muss bereits installiert sein.
```
$ startx
```

----------------------------------------------------------------------------------------------------------------
