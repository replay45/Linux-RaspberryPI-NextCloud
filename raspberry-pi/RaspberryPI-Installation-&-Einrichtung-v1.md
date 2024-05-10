# RaspberryPI - Installation, Einrichtung und Updates



`Empfohlen wird eine SD-Karte mit mind. 8 GB.`

`Für diese Anleitung wurde ein RaspberryPI 5 genutzt, sollte jedoch auch für andere Versionen genutzt werden können.`

`RaspberryPI OS ist eine Linux Distribution, die auf Debian basiert und von der Raspberry Pi Foundation entwickelt wurde.`

`Anleitung verfasst am 7.5.2024`


-----------------------------------------------------------------------------------------------------------------------------------------------


# 1. Installation von [RaspberryPI-OS](https://www.raspberrypi.com/software/)

### A. Installation von [RaspberryPI-OS](https://www.raspberrypi.com/software/) über den Imager.

- installieren des `rpi-imager` auf `Linux`, um das RaspberryPI-OS auf eine `SD-Karte zu flashen`:
    - Den rpi-imager von der offiziellen Webseite herunterladen und als Debian-Paket installieren.
    - Oder den rpi-imager über den [Flatpak-AppStore](https://flathub.org/apps/org.raspberrypi.rpi-imager) installieren.

- Der rpi-imager kann alternativ auch auf Windows, MacOS oder auch einem anderem RaspberryPI installiert werden.



### B. Flashen des OS auf die SD Karte

- Die SD-Karte nun einsetzen.
- in dem rpi-imager
    - das Modell,
    - das passende OS zum Modell und
    - die SD-Speicherkarte auswählen.

- auf `Weiter` klicken
- Danach könnte eine auf Deutsch etwas `verwirrende Meldung erscheinen`, diese mit `NEIN` beantworten und
- die Warnung, dass alle Daten auf der SD-Karte gelöscht werden, mit `JA` bestätigen.

- Der Vorgang kann nun einige Zeit in Anspruch nehmen.

- Sobald der `Vorgang abgeschlossen` ist, kann der `Imager geschlossen` werden und die `SD-Karte` entfernt werden.



### C. RaspberryPI Ersteinrichtung

- Jetzt die SD-Karte in den RaspberryPI einstecken.
- booten (Strom anschließen)

- Sprach- und Tastatureinstellungen vornehmen,
- einen Nutzer erstellen,
- alternativ den RaspberryPI mit einem WLAN verbinden oder ein LAN-Kabel einstecken

- Standardbrowser wählen, empfohlen Firefox
- Updates installieren und neustarten


-----------------------------------------------------------------------------------------------------------------------------------------------


# 2. IP-Adresse herausfinden

```
$ ifconfig
```
oder
```
$ ip a
```


-----------------------------------------------------------------------------------------------------------------------------------------------


# 3. System Updaten

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


-----------------------------------------------------------------------------------------------------------------------------------------------


# 4. [Tux Racer](https://tuxracer.sourceforge.net/) - Rennspiel & [Supertux](https://www.supertux.org/) - Jump-'n'-Run


In den beiden Spielen muss man das Linux-Maskottchen `Tux` steuern.



  - Tux Racer
```
$ sudo apt install extremetuxracer
```

  - Supertux
```
$ sudo apt install supertux
```


-----------------------------------------------------------------------------------------------------------------------------------------------


# 5. [Brave-Browser installieren](https://brave.com/linux/)

```
$ sudo apt install curl
$ sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
$ echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list
$ sudo apt update
$ sudo apt install brave-browser
```


-----------------------------------------------------------------------------------------------------------------------------------------------
