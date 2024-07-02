# Software & Pakete


# 1. Programme auf Linux über das Terminal deinstallieren


### A. Liste installierter Pakete:
```
$ dpkg --list
```

- nach einem bestimmten Paket suchen:
```
$ dpkg --list | grep [PROGRAMMNAME]
```

- Beispiel
```
$ dpkg --list | grep "firefox"
```



### B. Paketmanager [apt](https://wiki.ubuntuusers.de/APT/)

- Deinstallation aber Konfig-Datein werden behalten:
```
$ sudo apt-get remove PAKETNAME
```

- Deinstallation & Entfernung Konfigurationsdateien:
```
$ sudo apt-get --purge remove [PAKETNAME]
```


### C. [Flatpak](https://wiki.ubuntuusers.de/Flatpak/)

- Deinstallation von Programmen:
```
$ sudo flatpak uninstall [PAKET]
```


----------------------------------------------------------------------------------------------------------------


# 2. Paket Konfigurationen für Kali Linux - [Tasksel](https://pkg.kali.org/pkg/tasksel)

Mit `Software-selection` können im Nachhinein weitere Tools, Desktopumgebungen etc. nachinstalliert oder entfernt werden.


> [Tasksel - wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/tasksel/)


```
$ sudo apt install tasksel
```
```
$ sudo tasksel
```


- Mit der Leertaste kann die Auswahl bestätigt werden.




- WICHTIG:
	- Wenn ein bereits vorhandenes `*` bei einer Option entfernt wird, werden die Programme etc. hinter dieser Option entfernt !
	- Daher bei dem Setzen der `*` sorgfältig arbeiten !



----------------------------------------------------------------------------------------------------------------


# 3. [Flatpak](https://flatpak.org/) - Paketmanager

### [Flatpak auf Kali installieren](https://www.kali.org/docs/tools/flatpak/)


`Hier verwendetes Betriebssystem: Kali mit der GNOME Benutzeroberfläche`


- Flathub App-Store installieren:

```
$ sudo apt install flatpak
$ sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo	
$ sudo apt install gnome-software-plugin-flatpak
```



- optional die Apps, die über Flatpak installiert wurden, an die Kali-Themes anpassen:



- Themes anpassen (Kali-Themes in den Apps):

```
$ mkdir -p ~/.themes
$ cp -a /usr/share/themes/* ~/.themes/
$ sudo flatpak override --filesystem=~/.themes/
```



- Pakete die über Flathub installiert wurden, updaten:

```
$ flatpak update
```

- Liste installierter Programme:

```
$ flatpak list
```

- Deinstallation von Programmen:

```
$ sudo flatpak uninstall [PAKET] 
```

- kaputte Installation Fix:
```
$ flatpak repair
```

- Verlauf anzeigen:
```
$ flatpak history
```




> [Flatpak - Setup](https://flatpak.org/setup/Ubuntu)

> [Flatpak - wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/Flatpak/)



----------------------------------------------------------------------------------------------------------------


# 4. [Snap](https://snapcraft.io/) - Paketmanager


### [Snap auf Kali-Linux installieren](https://snapcraft.io/docs/installing-snap-on-kali)


`Hier verwendetes Betriebssystem: Kali mit der GNOME Benutzeroberfläche`


```
$ sudo apt update
```
```
$ sudo apt install snapd
```
```
$ su root
```
```
$ apt update
```
```
$ apt install snapd
```
```
$ systemctl enable --now snapd apparmor
```
```
$ snap install hello-world
```
```
$ hello-world
```


- Sollte es zu Problemen kommen, kann der folgende Befehl genutzt werden:
    - falls der `snapd.apparmor service` deaktiviert ist, sollte der auszuführende Befehl im Terminal nach der Ausführung des Befehls erscheinen 

```
$ snap warnings
```



### B. [Snap App Store installieren](https://snapcraft.io/docs/installing-snap-store-app):

```
$ sudo snap install snap-store
```

- Wenn der Store sich nicht über das Icon öffnen lässt, über das Terminal mit dem Befehl versuchen:

```
snap-store
```



----------------------------------------------------------------------------------------------------------------


# 5. Kali undercover mode in [Xfce](https://www.xfce.org/)

Der undercover-Modus verwandelt die Xfce Oberfläche, in eine täuschend echte Windows10 Oberfläche.

Befehl:
```
$ kali-undercover
```


Um den Modus zu deaktivieren, einfach den Befehl erneut in das Terminal eingeben.


Die undercover-Funktion funktioniert ausschließlich in [Xfce](https://www.xfce.org/) !




----------------------------------------------------------------------------------------------------------------


# 6. [Apache2](https://httpd.apache.org/) Webserver installieren:

```
$ sudo apt update
$ sudo apt install apache2
```

Firewall-Einstellungen - [ufw](https://wiki.ubuntuusers.de/ufw/):
```
$ sudo ufw app list
$ sudo ufw allow 'Apache'
$ sudo ufw status
```

Apache2 Status & Autostart:
```
$ sudo systemctl status apache2
$ sudo systemctl enable apache2
$ sudo systemctl start apache2
$ sudo systemctl status apache2
```


Für ein besseres Dateimanagement kann optional der [Midnight Commander](https://midnight-commander.org/) (auch bekannt als `Total Commander`) verwendet werden:
```
$ sudo apt install mc
$ sudo mc
```


> [wiki.ubuntuusers.de/Apache_2.4](https://wiki.ubuntuusers.de/Apache_2.4/)


----------------------------------------------------------------------------------------------------------------


# 7. SSH Server auf Ubuntu installieren, um über SSH Zugriff auf das Terminal zu erhalten.

### SSH auf dem Host:

```
$ sudo apt install openssh-server
$ sudo systemctl status ssh
$ sudo systemctl enable ssh
$ sudo systemctl start ssh
$ sudo ufw allow ssh
```


- OPTIONAL: ggf. können zusätzlich noch einige Einstellungen angepasst werden

```
$ sudo nano /etc/ssh/sshd_config
```

speichern mit `STRG+X`
```
$ sudo service ssh restart
```

- Wenn Fehlermeldungen nach dem bearbeiteten der Konfiguartionsdatei auftreten, kann die Syntax in der Datei überprüft werden:

```
$ sudo sshd -t
```



### Dateien über SSH mit SCP kopieren:

Secure Copy (SCP) ist ein älteres Protokoll zur verschlüsselten Übertragung von Daten über SSH.


- Eine Datei vom lokalen zum entfernten System kopieren:
```
$ scp DATEI.TXT DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD
```


- Eine Datei vom Entfernten zum Lokalen System kopieren
```
$ scp DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD/ZUR/DATEI.TXT
```


> [kali.org/SSH](https://www.kali.org/tools/openssh/)

> [wiki.ubuntuusers.de/SSH/](https://wiki.ubuntuusers.de/SSH/)



----------------------------------------------------------------------------------------------------------------


# 8. remote Desktop

# A. [VNC](https://www.realvnc.com/de/connect/download/viewer/)


VNC eignet sich nur im Heimnetz, da die Übertragung unverschlüsselt ist.

Zudem ist VNC erfahrungsgemäß nicht besonders zuverlässig und funktioniert nur wenn ein Bildschirm direkt angeschlossen ist, auch wenn dieser nicht verwendet wird. 

Daher eignet sich VNC eher um sich das umstecken von Maus/Tastatur zu sparen.

Wenn man zuverlässig über remote-Desktop arbeiten möchte sollte dies lieber über `RDP` oder `noMachine` tun.



- Installation & Displaymanager wechseln
```
$ sudo apt update
```
```
$ sudo apt install lightdm
```

- x11vnc installieren
```
$ sudo reboot
```
```
$ sudo apt install x11vnc
```
```
$ sudo nano /lib/systemd/system/x11vnc.service
```

- Folgendes einfügen:
```
[Unit]
Description=x11vnc service
After=display-manager.service network.target syslog.target

[Service]
Type=simple
ExecStart=/usr/bin/x11vnc -forever -display :0 -auth guess -passwd DEIN-PASSWORT
ExecStop=/usr/bin/killall x11vnc
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

- VNC zum Autostart hinzufügen und starten
```
$ systemctl daemon-reload
$ systemctl enable x11vnc.service
$ systemctl start x11vnc.service
$ systemctl status x11vnc.service
```

- automatisches Bildschirmsperren deaktivieren

Es empfiehlt sich das automatische Sperren des Bildschirms unter `Datenschutz` und dann `Bildschirm` `"Automatic Screen Lock"` zu deaktivieren, um Fehler zu vermeiden.


- Firewall

Wenn die Firewall genutzt wird, muss noch eine Portfreigabe,  z.B. mit dem Firewallmanager [ufw](https://wiki.ubuntuusers.de/ufw/) vorgenommen werden.

Welcher Port verwendet wird, kann mit dem Befehl eingesehen werden:

```
$ systemctl status x11vnc.service
```
```
$ sudo apt install ufw
$ sudo ufw allow {PORT}
$ sudo ufw allow 5900
$ sudo ufw status
$ sudo ufw enable
```


----------------------------------------------------------------------------------------------------------------


# B. [noMachine](https://www.nomachine.com/de)

- deb-Datei herunterladen
- Terminal öffnen
```
$ cd Downloads
$ ls
```
```
$ sudo dpkg -i DATEINAME.deb
$ sudo dpkg -i nomachine_8.11.3_4_amd64.deb
```

- Firewall
```
$ sudo apt install ufw
$ sudo ufw allow {PORT}
$ sudo ufw allow 4000
$ sudo ufw status
$ sudo ufw enable
```
```
$ reboot
```

- Nun ist noMachine installiert und kann nach Beliben angepasst werden.


----------------------------------------------------------------------------------------------------------------


# C. [RDP Server](https://de.wikipedia.org/wiki/Remote_Desktop_Protocol)

- Installation des RDP-Servers
```
$ sudo apt install xrdp
$ systemctl status xrdp
$ systemctl enable xrdp
```

- Firewall
```
$ sudo apt install ufw
$ sudo ufw allow {PORT}
$ sudo ufw allow 3389
$ sudo ufw status
$ sudo ufw enable
```
```
$ reboot
```

Nun kann z.B. [Remmina](https://remmina.org/) oder ein anderer RDP Viewer genutzt werden, um die remote-Desktop Verbindung herzustellen.

----------------------------------------------------------------------------------------------------------------
