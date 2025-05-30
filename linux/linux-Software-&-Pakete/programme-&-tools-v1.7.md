# Programme und Tools

`Anleitung zuletzt bearbeitet am 7.2.2025`


## Inhaltsverzeichnis
1. Browser & Mail
2. Systemüberwachung & Systeminformationen
3. Medienwiedergabe
4. Poduktivität
5. [Metadata Cleaner](https://flathub.org/apps/fr.romainvigier.MetadataCleaner)
6. [Warpinator](https://warpinator.com/)
7. VPN - Wireguard


----------------------------------------------------------------------------------------------------------------


# 1. Browser & Mail

### [Firefox](https://www.mozilla.org/de/firefox/new/)
```
$ sudo apt install firefox
```

### [Chromium](https://www.chromium.org/chromium-projects/)
```
$ sudo apt install chromium
```

### [Brave](https://brave.com/de/)
```
$ sudo apt install curl
$ sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
$ echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list
$ sudo apt update
$ sudo apt install brave-browser
```

### [Thunderbird](https://www.thunderbird.net/de/)
```
$ sudo apt install thunderbird
```

- Eine empfohlene Alternative für `Andorid` ist [K-9-Mail](https://k9mail.app/) - mehr zu Andorid in dem Repository [Windows-Apple-und-Android](https://github.com/replay45/Windows-Apple-und-Android/tree/main/Android).


----------------------------------------------------------------------------------------------------------------


# 2. Systemüberwachung & Systeminformationen


### [hwinfo](https://hwinfo.su/de/linux/)
```
$ sudo apt install hwinfo
```

### CPU-X
```
$ sudo apt install cpu-x
```

### [Neofetch](https://github.com/dylanaraps/neofetch)
```
$ sudo apt install neofetch
$ neofetch
```


----------------------------------------------------------------------------------------------------------------


# 3. Medienwiedergabe

### [vlc Media Player](https://www.videolan.org/vlc/index.de.html)
```
$ sudo apt install vlc
```


----------------------------------------------------------------------------------------------------------------


# 4. Poduktivität

### [Visual Studio Code](https://code.visualstudio.com/)
- Debian Paket herunterladen
- entweder manuell installieren
- oder mit [GDebi](https://packages.debian.org/de/stable/gdebi) (Debian-Paket-Installationsmanager) komfortabel installieren


### [GDebi](https://packages.debian.org/de/stable/gdebi)
- Debian-Paket-Installationsmanager
```
$ sudo apt-get install gdebi
```


### [remmina](https://remmina.org/) - Remote Desktop Client
```
$ sudo apt install remmina
```


### [balena etcher](https://etcher.balena.io/)
- download balena etcher App-image File
```
$ sudo apt update
$ sudo apt install zenity
$ sudo mv balena etcher App-image /optautp
$ sudo su
$ chmod 777 /opt/balena-etcher.AppImage
$ /opt/balena-etcher.AppImage
```


### Passwortmanager [KeePassXC](https://keepassxc.org/)
```
$ sudo apt install keepassxc
```


### [Libre Office](https://de.libreoffice.org/)

- Möglichkeit 1
    - Download Deb-Datei
    - entpacken und Terminal in Ordner öffnen 
```
$ sudo dpkg -i *.deb
```

- Möglichkeit 2
    - Debian Paket herunterladen
    - mit [GDebi](https://packages.debian.org/de/stable/gdebi) (Debian-Paket-Installationsmanager) komfortabel installieren


----------------------------------------------------------------------------------------------------------------


# Linux Tools


# 5. [Metadata Cleaner](https://flathub.org/apps/fr.romainvigier.MetadataCleaner)

`10.4.2025: Der Metadata Cleaner wird nicht mehr vom Entwickler supportet und erhält keine Updates mehr.`
[gitlab.com - Metadata Cleaner](https://gitlab.com/rmnvgr/metadata-cleaner/)

Metadaten in einer Datei enthalten viele Informationen.
Kameras zeichnen Daten darüber auf, wann und wo ein Bild aufgenommen und welche Kamera verwendet wurde. 
Office-Anwendungen fügen automatisch Autoren- oder Firmeninformationen zu Dokumenten hinzu.
Nicht immer möchte man, dass diese Informationen für Dritte zugänglich sind.

- Installation & Verwendung auf Linux
    - Mit dem Tool [Metadata Cleaner](https://metadatacleaner.romainvigier.fr/) kann man Metadaten in Dateien anzeigen und weitesgehend entfernen.
    - Es gibt jedoch niemals eine Garantie dafür, dass alle Metadaten vollständig entfernt werden.
    - Der Metadata Cleaner kann über [Flatpak](https://flathub.org/apps/fr.romainvigier.MetadataCleaner) installiert werden, z.B. über den Flatpak-App-Store.
    - Um nun die Metadaten einzusehen und zu entfernen, muss der Metadata Cleaner geöffnet, die Datei eingefügt und dann nur noch unten rechts auf den Button `Clean` gedrückt werden.
    - Es empfiehlt sich immer, eine Kopie der Datei zu sichern, bevor die Metadaten entfernt werden.


----------------------------------------------------------------------------------------------------------------


# 6. [Warpinator](https://warpinator.com/)

Warpinator ist ein Tool, womit Dateien und Ordner einfach zwischen Linux Computern oder alternativ auch an Android oder Windows Geräte im selben lokalen Netzwerk übertragen werden können.

- Warpinator für Linux, kann als [Flatpak](https://flathub.org/apps/org.x.Warpinator) installiert werden.
- Für Android gibt es mehrere Möglichkeiten. Entweder man lädt sich offizielle Version als [APK](https://warpinator.com/) herunter, oder man greift auf die inoffizielle Version aus dem [Play-Store](https://play.google.com/store/apps/details?id=slowscript.warpinator) zurück.
Aber auch im [F-Droid-Store](https://f-droid.org/de/packages/slowscript.warpinator/) ist der Warpinator zu finden.


----------------------------------------------------------------------------------------------------------------


# 7. VPN


## Was ist [Wireguard](https://www.wireguard.com/) ?

Wireguard ist ein modernes und effizientes VPN-Protokoll, welches schnelle und sichere VPN-Verbindungen ermöglicht.
Zudem ist das Protokoll sowohl für einfache Konfiguration als auch für hohe Sicherheit bekannt.

Das Protokoll wird nicht nur von VPN-Anbietern benutzt, es findet auch Verwendung bei modernen [FritzBoxen](https://avm.de/).
Somit kann man eine sichere Verbindung von seinem Handy oder Laptop von überall aus, auf das Heimnetz herstellen, was sich besonders bei der Nutzung von öffentlichen oder fremden WLANs anbietet, denn bei der VPN-Verbindung wird der eigene Netzwerkverkehr bis in das Heimnetz verschlüsselt. Außerdem kann man über die VPN-Vebindung in das Heimnetz auch auf Geräte aus dem Heimnetzwerk zugreifen oder einen eigenen DNS-Server, wie [Pi hole](https://pi-hole.net/) nutzen.


- Mehr zu Pi hole und DNS-Servern unter [RaspberryPi/Pi-hole](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi) 


## [Wireguard](https://www.wireguard.com/) auf Windows/Android/iOS/MacOS
- Auf den gängigen Betriebsystemen gibt es eine offizielle App mit grafischer Oberfläche, in der man die Konfigurationsdatei benutzerfreundlich importieren kann.
- Für Linux gibt es leider noch keine App mit grafischem Interface.



## [Wireguard einrichten - Debian basierte Distributionen](https://www.wireguard.com/)

`Die Anleitung wurde auf Kali Linux mit der GNOME Benutzeroberfläche getestet, sollte aber auf anderen Debian basierenden Distributionen, wie Ubuntu, problemlos funktionieren.`

`Für diese Anleitungen wird eine Wireguard-Konfigurationsdatei vom VPN-Server vorausgesetzt.`

- Dabei ist egal, ob diese von einem VPN-Anbieter oder von der FritzBox aus dem Heimnetzwerk kommt.

- Hinweis zu Kali:
    - In Kali Linux ist es möglich, die VPN-Verbindung im Quick-Panel oder in den Einstellungen unter "Netzwerk" zu aktivieren.
    - Alternativ kann aber auch die Verbindung über `nmtui` (Punkt 7.1) im Terminal gestartet werden.

- Hinweis zu Ubuntu:
    - Zum Zeitpunkt der Erstellung dieser Anleitung ist es erst in Ubuntu 23.10 möglich, die Wireguard VPN-Verbindung grafisch zu starten.
    - Wer eine ältere Version nutzt, muss die Verbindung im Terminal über `nmtui` (Punkt 7.1) starten.


### Wichtig - Name der Konfigurationsdatei
- Um Probleme bei der Einrichtung zu vermeiden, empiehlt es sich die Konfigurationsdatei `wg_config.conf` zu benennen.
- Wer mehrere Verbindungen nutzt, kann die Konfigurationen mit Zahlen ergänzen, wie z.B. `wg_config01.conf`.


-----------------------------------------------------------------------------------------------------------------


# 7.1 Befehlszeilen-Methode
- Installation:
```
$ sudo apt install wireguard resolvconf
```

- in das Verzeichnis wechseln, wo die config-Datei gespeichert ist:
```
$ sudo su
$ cd /etc/Wireguard
$ sudo chown root:root /etc/wireguard/wg-client.conf
$ sudo chmod 644 /etc/wireguard/wg-client.conf
$ sudo chmod 755 /etc/wireguard
```

- Verbindung über Befehl starten/beenden:
```
$ sudo wg-quick up /etc/wireguard/wg-client.conf
$ sudo wg show
```
```
$ wg-quick down /etc/wireguard/wg-client.conf
$ ifconfig
$ ip a
```

- Verbindung über nmtui starten/beenden:
    - Durch das Setzen des `*` kann die Verbindung aufgebaut/beendet werden.
```        
$ nmtui-connect
```
```
$ nmcli connection import type wireguard file /etc/wireguard/wg-client.conf
$ nm-connection-editor
```


# 7.2 grafische Methode mit dem nm-editor
```
$ sudo apt install wireguard resolvconf
$ nm-connection-editor
```

- `+`
- unter `virtual` `Wireguard` auswählen
- nach dem ausfüllen der Kästchen, auf `Save` gehen
- Nun in den Einstellungen noch die Eigenschaften der Verbindung öffnen und bei `Peers` die Fehlenden Einträge ergänzen.
- Nach dem Speichern, kann im Quick-Panel (Schnelleinstellungen) oder alternativ in den Einstellungen unter "Netzwerk", die VPN-Verbindung gestartet und beendet werden.


# 7.3 Wireguard-config via nmcli importieren
```
$ nmcli
$ nmcli connection import type wireguard file wg_config.conf
```
```
$ sudo wg show
$ ifconfig
$ ip a
```

- um den Autostart von Wireguard (über nmcli) zu deaktivieren:

Wireguard Verbindungsname ausfindig machen
```
$ nmcli connection show
```

Autostart ausschalten:
```
$ nmcli connection modify "dein-verbindungsname" connection.autoconnect no
```

Überprüfen:
```
$ nmcli connection show "dein-verbindungsname" | grep autoconnect
```




# 7.4 Wireguard Konfiguaration manuell in den Einstellungen hinzufügen (grafische Methode)
Diese Methode ist unter Ubuntu erst ab der Version 24 möglich !
Diese Anleitung wurde unter Kali Linux getestet.


- Unter Kali Linux gibt es eine weitere Möglichkeit, um Wireguard komfortabel nutzen zu können.
- Natürlich funktioniert auch die Befehlszeilen-Methode unter Kali Linux.


- Die config-Datei bereithalten und öffnen.
- Einstellungen öffnen,
- zu dem Punkt `Netzerk` eine VPN Verbindung hinzufügen.
- Unter `Add VPN` `Wireguard` auswählen.
- Dann einen `Connection` und `Interface` Namen wählen, und den privaten Schlüssel einfügen.
- Danach bei `Peers` die Adresse des Routers einfügen.
- Nun auf speichern klicken und in dem Quick-Panel (Schnelleinstellungen) oder in den Einstellungen unter VPN die Verbindung herstellen.

- Zum Überprüfen der Verbindung:
```
$ ifconfig
```
oder
```
$ ip a
```

----------------------------------------------------------------------------------------------------------------
