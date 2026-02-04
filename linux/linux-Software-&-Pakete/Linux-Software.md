# Linux Software

`Anleitung zuletzt bearbeitet am 15.12.2024`


## Inhaltsverzeichnis
1. Paket Konfigurationen für Kali Linux - [Tasksel](https://pkg.kali.org/pkg/tasksel)
2. Kali undercover mode in [XFCE](https://www.xfce.org/)
3. [Apache2](https://httpd.apache.org/) Webserver installieren
4. [SSH](https://de.wikipedia.org/wiki/Secure_Shell) Server auf Ubuntu installieren
5. Dateien über SSH mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) kopieren
6. remote Desktop


----------------------------------------------------------------------------------------------------------------


# 1. Paket Konfigurationen für Kali Linux - [Tasksel](https://pkg.kali.org/pkg/tasksel)

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


# 2. Kali undercover mode in [XFCE](https://www.xfce.org/)

Der undercover-Modus verwandelt die Xfce Oberfläche, in eine täuschend echte Windows10 Oberfläche.

Befehl:
```
$ kali-undercover
```

- Um den Modus zu deaktivieren, einfach den Befehl erneut in das Terminal eingeben.
- Die undercover-Funktion funktioniert ausschließlich in [Xfce](https://www.xfce.org/) !


----------------------------------------------------------------------------------------------------------------


# 3. [Apache2](https://httpd.apache.org/) Webserver installieren:

```
$ sudo apt update
$ sudo apt install apache2
```

- Firewall-Einstellungen - [ufw](https://wiki.ubuntuusers.de/ufw/):
```
$ sudo ufw app list
$ sudo ufw allow 'Apache'
$ sudo ufw status
```

- Apache2 Status & Autostart:
```
$ sudo systemctl status apache2
$ sudo systemctl enable apache2
$ sudo systemctl start apache2
$ sudo systemctl status apache2
```


- Für ein besseres Dateimanagement kann optional der [Midnight Commander](https://midnight-commander.org/) (auch bekannt als `Total Commander`) verwendet werden:
```
$ sudo apt install mc
$ sudo mc
```


> [wiki.ubuntuusers.de/Apache_2.4](https://wiki.ubuntuusers.de/Apache_2.4/)


----------------------------------------------------------------------------------------------------------------


# 4. [SSH](https://de.wikipedia.org/wiki/Secure_Shell) Server auf Ubuntu installieren

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

- speichern mit `STRG+X`
```
$ sudo service ssh restart
```

- Wenn Fehlermeldungen nach dem bearbeiteten der Konfiguartionsdatei auftreten, kann die Syntax in der Datei überprüft werden:
```
$ sudo sshd -t
```

- SSH Verbindung aufbauen
	- Terminal auf dem Client öffnen und Befehl eingeben
```
$ ssh user@IP-Adresse
```


----------------------------------------------------------------------------------------------------------------


# 5. Dateien über SSH mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) kopieren
Secure Copy (SCP) ist ein älteres Protokoll zur verschlüsselten Übertragung von Daten über SSH.


- Eine Datei vom lokalen zum entfernten System kopieren:
```
$ scp PFAD/LOKALE/DATEI USERNAME@IP-ADRESSE:/DEIN/PFAD/ENTFERNTES/SYSTEM
```
Beispiel: 
```
scp /home/user/Documets/Datei.txt user@192.168.2.5:/var/www/html/Datei.txt
```


- Eine Datei vom entfernten zum lokalen System kopieren
```
$ scp USERNAME@IP-ADRESSE:/PFAD/ENTFERNTES/SYSTEM/ZUR/DATEI /PFAD/LOKALES/SYSTEM/DATEI
```
Beispiel: 
```
scp user@192.168.2.5:/var/www/html/Datei.txt /home/user/Documets/
```


> [kali.org/SSH](https://www.kali.org/tools/openssh/)

> [wiki.ubuntuusers.de/SSH/](https://wiki.ubuntuusers.de/SSH/)



----------------------------------------------------------------------------------------------------------------


# 6. remote Desktop


# A. [VNC](https://www.realvnc.com/de/connect/download/viewer/)
- VNC eignet sich nur im Heimnetz, da die Übertragung unverschlüsselt ist.
- Zudem ist VNC erfahrungsgemäß nicht besonders zuverlässig und funktioniert nur wenn ein Bildschirm direkt angeschlossen ist, auch wenn dieser nicht verwendet wird. 
- Daher eignet sich VNC eher um sich das umstecken von Maus/Tastatur zu sparen.
- Wenn man zuverlässig über remote-Desktop arbeiten möchte sollte dies lieber über `RDP` oder `noMachine` tun.


### Installation des VNC-Servers
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
- Es empfiehlt sich das automatische Sperren des Bildschirms unter `Datenschutz` und dann `Bildschirm` `"Automatic Screen Lock"` zu deaktivieren, um Fehler zu vermeiden.


- [Firewall](https://de.wikipedia.org/wiki/Firewall)
    - Wenn die Firewall genutzt wird, muss noch eine Portfreigabe,  z.B. mit dem Firewallmanager [ufw](https://wiki.ubuntuusers.de/ufw/) vorgenommen werden.
    - Welcher Port verwendet wird, kann mit dem Befehl eingesehen werden:

```
$ systemctl status x11vnc.service
```
```
$ sudo apt install ufw
$ sudo ufw allow PORT
$ sudo ufw allow 5900
$ sudo ufw status
$ sudo ufw enable
```

- Nun kann z.B. [Remmina](https://remmina.org/) oder ein VNC-Viewer genutzt werden, um die remote-Desktop Verbindung herzustellen.


----------------------------------------------------------------------------------------------------------------


# B. [noMachine](https://www.nomachine.com/de)
- NoMachine nutzt das NX-Protokoll welches ein closed Source Protokoll ist.
- Der Vorteil von noMachine ist die mit TLS verschlüsselte Verbindung.
- Es wird anders wie bei RDP keine virtuelle Sitzung, sondern die eigentliche lokale Sitzung verwendet.
- Im grunde genommen kann man sagen, dass noMachine eher wie eine Art fernsteuerung funktioniert.
- Durch die lokale Sitzung können auch die lokalen I/O-Anschlüsse, wie z.B. USB-, Audio- und Video-Anschlüsse verwendet werden.


### Installation des noMachine-Servers
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

- [Firewall](https://de.wikipedia.org/wiki/Firewall)
```
$ sudo apt install ufw
$ sudo ufw allow PORT
$ sudo ufw allow 4000
$ sudo ufw status
$ sudo ufw enable
```
```
$ reboot
```

- Nun ist noMachine installiert und kann nach Belieben angepasst werden.
- Um eine Remote Desktop-Verbindung herstellen zu können, muss auf dem Viewer (Client) ebenfalls noMachine installiert werden. Für Linux Clients (dem Viewer) ist der Vorgang identisch, für Windows einfach die Installtionsdatei herunterladen und entweder als Server einrichten oder als Viewer verwenden.
- Die Verwendung von Remmina ist mit einem noMachine Server nicht möglich.


----------------------------------------------------------------------------------------------------------------


# C. [RDP Server](https://de.wikipedia.org/wiki/Remote_Desktop_Protocol)
- RDP ist ein Protokoll für remote Desktop, welches üblicherweise bei Windows verwendet wird.
- Dabei wird eine virtuelle Sitzung erstellt.
- Für remote Verbindugen zu einem entfernten Windows-System ist das die einfachste Methode, bietet jedoch auch im vergleich zu noMachine wenige Anpassungsmöglichkeiten.


### Installation des RDP-Servers
```
$ sudo apt install xrdp
$ systemctl status xrdp
$ systemctl enable xrdp
```

- [Firewall](https://de.wikipedia.org/wiki/Firewall)
```
$ sudo apt install ufw
$ sudo ufw allow PORT
$ sudo ufw allow 3389
$ sudo ufw status
$ sudo ufw enable
```
```
$ reboot
```

- Nun kann z.B. [Remmina](https://remmina.org/) oder ein anderer RDP Viewer genutzt werden, um die remote-Desktop Verbindung herzustellen.


----------------------------------------------------------------------------------------------------------------
