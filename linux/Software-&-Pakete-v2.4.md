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


-------------------------------------------------------------------------------------------------------------------------


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




WICHTIG:
	- Wenn ein bereits vorhandenes `*` bei einer Option entfernt wird, werden die Programme etc. hinter dieser Option entfernt !
	- Daher bei dem Setzen der `*` sorgfältig arbeiten !



-------------------------------------------------------------------------------------------------------------------------



# 3. Flatpak auf Kali installieren



### [Flatpak](https://www.kali.org/docs/tools/flatpak/) ist ein Paketmanager für Linux.


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



-------------------------------------------------------------------------------------------------------------------------



# 4. Kali undercover mode in [Xfce](https://www.xfce.org/)


Der undercover-Modus verwandelt die Xfce Oberfläche, in eine täuschend echte Windows10 Oberfläche.

Befehl:
```
$ kali-undercover
```


Um den Modus zu deaktivieren, einfach den Befehl erneut in das Terminal eingeben.


Die undercover-Funktion funktioniert ausschließlich in [Xfce](https://www.xfce.org/) !




-------------------------------------------------------------------------------------------------------------------------


# 5. SSH Server auf Ubuntu installieren, um über SSH Zugriff auf das Terminal zu erhalten.


### SSH auf dem Host:

```
sudo apt install openssh-server
sudo systemctl status ssh
sudo systemctl enable ssh
sudo systemctl start ssh
sudo ufw allow ssh
```


- OPTIONAL: ggf. können zusätzlich noch einige Einstellungen angepasst werden

```
sudo nano /etc/ssh/sshd_config
```

Speichern mit `STRG+X`
```
$ sudo service ssh restart
```



### Dateien über SSH mit SCP kopieren:

Secure Copy (SCP) ist ein älteres Protokoll zur verschlüsselten Übertragung von Daten über SSH.


- Eine Datei vom lokalen zum entfernten System kopieren:
```
$ scp DATEI.TXT DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD
```


- Eine Datei vom entfernten zum lokalen System kopieren
```
$ scp DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD/ZUR/DATEI.TXT
```


> [kali.org/SSH](https://www.kali.org/tools/openssh/)

> [wiki.ubuntuusers.de/SSH/](https://wiki.ubuntuusers.de/SSH/)



-------------------------------------------------------------------------------------------------------------------------


# 7. [Apache2](https://httpd.apache.org/) Webserver installieren:

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


-------------------------------------------------------------------------------------------------------------------------
