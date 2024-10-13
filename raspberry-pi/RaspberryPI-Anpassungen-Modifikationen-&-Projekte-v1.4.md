# RaspberryPI - Anpassungen, Modifikationen und Projekte

`Für diese Anleitung wurde ein RaspberryPI 5 genutzt, diese sollte jedoch auch für ältere Modelle übertragbar sein.`

`Anleitung verfasst am 7.5.2024`



----------------------------------------------------------------------------------------------------------------


# 1. [SSH](https://www.raspberrypi.com/documentation/computers/remote-access.html)


- SSH ist bereits standardmäßig auf RaspberryPI-OS vorinstalliert.


### A. Installation / Konfiguration

```
$ sudo apt install openssh-server
```
```
$ sudo systemctl status ssh
```
```
$ sudo systemctl enable ssh
```
```
$ sudo systemctl start ssh
```



### B. ggf. wenn Firewall aktiv ist

```
$ sudo ufw allow ssh
```



### C. Hostname / IP Adresse anzeigen

```
$ hostname -I

$ ifconfig
```



### D. Auf dem Client mit dem Host verbinden

```
$ ssh Benutzername@IP-Adresse
```



----------------------------------------------------------------------------------------------------------------


# 2. [VNC](https://www.raspberrypi.com/documentation/computers/remote-access.html)


- VNC ist bereits standardmäßig auf RaspberryPI-OS vorinstalliert.


### A. Installation / Konfiguration

```
$ sudo apt install realvnc-vnc-server
```

```
$ sudo raspi-config
```


Folgendes wählen
```
3 Interface Options

2 VNC

"JA"
```


### B. ggf. wenn Firewall aktiv ist

```
$ sudo ufw allow vnc
```


### C. Hostname / IP Adresse anzeigen

```
$ hostname -I

$ ifconfig
```



### D. Auf dem Client mit dem Host verbinden

```
$ sudo apt install realvnc-vnc-viewer
```
```
$ realvnc-vnc-viewer
```


- Hinweis
	- Es ist nicht zwingend notwendig, einen Account anzulegen, um den VNC-Viewer zu verwenden.



### E. manuell den Autostart einrichten

- Wenn der VNC-Sever nach der Aktivierung mit `raspi-config` nicht automatisch startet, Folgende Schritte befolgen:

```
$ cd ~
```
```
cd .config
```
```
$ mkdir autostart
```
```
$ cd autostart
```
```
$ nano tightvnc.desktop
```

- Folgendes einfügen:
```
[Desktop Entry]
Type=Application
Name=TightVNC
Exec=vncserver :1
StartupNotify=false
```

- mit `STRG+X` speichern





----------------------------------------------------------------------------------------------------------------


# 3. [Nautilus](https://wiki.ubuntuusers.de/Nautilus/) - GNOME Filemanager auf RaspberryPI OS installieren


### A. Möglichkeit-1 Version "focal fossa"

```
$ sudo nano /etc/apt/sources.list
```

- in der vorhandenen Datei Folgendes am Ende einfügen:
	- `focal` kann natürlich auch durch andere `Ubuntu-Versionen` ersetzt werden.

```
deb http://ppa.launchpad.net/ubuntu-desktop/ppa/ubuntu focal main
deb-src http://ppa.launchpad.net/ubuntu-desktop/ppa/ubuntu focal main
```

- speichern mit `STRG+X`


```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
```
```
$ sudo apt update
```
```
$ sudo apt install nautilus
```
```
$ xdg-mime default nautilus.desktop inode/directory application/x-gnome-saved-search
```
```
$ nautilus
```

Nun sollte der Nautilus Dateimanager installiert sein.




### B. Möglichkeit-2 Version "Jammy Jellyfish"

```
$ echo "deb http://ppa.launchpad.net/ubuntu-desktop/ppa/ubuntu focal main" | sudo tee -a /etc/apt/sources.list.d/ubuntu-desktop-ppa.list
```
```
$ sudo apt update
```
```
$ sudo apt install nautilus
```
```
$ xdg-mime default nautilus.desktop inode/directory application/x-gnome-saved-search
```
```
$ nautilus
```

Nun sollte der Nautilus Dateimanager installiert sein.



----------------------------------------------------------------------------------------------------------------


# 4. Desktop-Umgebung auf RaspberryPI-OS nachinstallieren und ändern

`Es gibt eine Möglichkeit die Änderung mit einer Grafischen- sowie zwei verschiedenen Befehlszeilenmethoden vorzunehmen. Diese Anleitung stützt sich besonders auf die Installation die Benutzeroberfläche GNOME.`


A. grafische Installationsmethode:
- Im Menü unter `Preferences` auf `Add/Remove Software` gehen.

- Hier kann nun nach `gnome`, `xfce4`, `kde-plasma-desktop`, `mate-desktop-environment`, `lxde`, `lxqt` oder `cinnamon-desktop-environment` gesucht werden.

- Auf `Apply` klicken, um mit der Installation fortzufahren.



B. Befehlszeilen Methode:
- 1. erste Alternative - Es kann auch der folgende Befehl verwendet werden:
	```
	$ sudo apt install <package>
	```
oder
- 2. zweite Methode - mit [Tasksel](https://wiki.ubuntuusers.de/tasksel/)
	```
	$ sudo tasksel
	```



- Sollte nach einem Neustart die Änderung bei GNOME nicht übernommen worden sein, kann noch etwas nachgeholfen werden:
```
$ sudo apt install gdm3
```
```
$ sudo dpkg-reconfigure gdm3
```
```
$ reboot
```


- Um zwischen den Desktopumgebungen wechseln zu können, kann der folgende Befehl verwendet werden:
```
$ sudo update-alternatives --config x-session-manager
```


- Wenn das nicht genügt, um zwischen den Desktopumgebungen zu wechseln, können diese Befehle genutzt werden, um die Display-Manager anzusprechen:
```
$ sudo systemctl restart lightdm
```
```
$ sudo systemctl restart gdm
```


----------------------------------------------------------------------------------------------------------------


# 5. Stromverbrauch & Energiezustände



### Auch wenn der RaspberryPI ausgeschaltet wird, aber am Strom bleibt, verbraucht er trotzdem noch vergleichsweise viel Strom. [datasheets.raspberrypi.com](https://datasheets.raspberrypi.com/hat/hat-plus-specification.pdf)


Woran liegt das ?

- Grund dafür ist, dass der PI nur softwareseitig heruntergefahren wird, das heißt, der PI bleibt in dem sogenannten `warm-Standby-Modus` und wird nicht ganz abgeschaltet.

- Aufgrund vieler Probleme mit den Erweiterungskarten (HATs) für den RaspberryPI, muss die 3- und 5-Volt-Schiene weiterhin mit Strom versorgt werden.


### Wenn KEINE Erweiterungskarte genutzt wird und dies auch nicht in Planung ist, kann der Energieverbrauch beeinflusst werden.
### Sollte eine Erweiterungskarte jedoch genutzt werden, kann dies zu Problemen führen !

----------------------------------------------------------------------------------------------------------------


- Stromverbrauch verringern:

```
$ sudo rpi-eeprom-config -e
```

Folgendes Ändern - [POWER_OFF_ON_HALT](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#POWER_OFF_ON_HALT)

von `POWER_OFF_ON_HALT=0`

zu `POWER_OFF_ON_HALT=1`


- speichern mit `STRG+X`


```
$ reboot
```


- Somit sollte die 3-Volt-Schiene beim Herunterfahren abgeschaltet werden und der Stromverbrauch im heruntergefahrenen Zustand deutlich geringer als vorher sein.

----------------------------------------------------------------------------------------------------------------
