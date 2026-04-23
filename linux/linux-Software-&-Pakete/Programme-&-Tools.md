# Programme und Tools

## Inhaltsverzeichnis
1. Browser & Mail
2. Systemüberwachung & Systeminformationen
3. Medienwiedergabe
4. Poduktivität
5. Metadata Cleaner / mat2
6. [Warpinator](https://warpinator.com/)
7. VPN - Wireguard


----------------------------------------------------------------------------------------------------------------

`Anleitung zuletzt bearbeitet am 4.2.2026`

# 1. Browser & Mail
- [sicher-&-datenschutzorientiert-surfen](https://github.com/replay45/ethical-hacking-und-cybersecurity/blob/main/cyber-security/sicher-%26-datenschutzorientiert-surfen.md)

### [Firefox](https://www.mozilla.org/de/firefox/new/)
```
$ sudo apt install firefox-esr
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

### [Mullvad](https://mullvad.net/de/download/browser/linux)
```
$ sudo apt install curl
$ sudo curl -fsSLo /usr/share/keyrings/mullvad-keyring.asc https://repository.mullvad.net/deb/mullvad-keyring.asc
$ echo "deb [signed-by=/usr/share/keyrings/mullvad-keyring.asc arch=$( dpkg --print-architecture )] https://repository.mullvad.net/deb/stable stable main" | sudo tee /etc/apt/sources.list.d/mullvad.list
$ sudo apt update
$ sudo apt install mullvad-browser
```

### [Thunderbird](https://www.thunderbird.net/de/)
```
$ sudo apt install thunderbird
```

- Eine empfohlene Alternative für `Andorid` ist [K-9-Mail](https://k9mail.app/) - mehr zu Andorid in dem Repository [Windows-Apple-und-Android](https://github.com/replay45/Windows-Apple-und-Android/tree/main/Android).


----------------------------------------------------------------------------------------------------------------


# 2. Systemüberwachung & Systeminformationen

`Anleitung zuletzt bearbeitet am 4.2.2026`

### htop - Systemmonitor
- `htop` ist ein Systemmonitor, ähnlich wie ein Taskmanager, aber im Terminal und zeigt die CPU und RAM Auslastung, die Prozessliste, den System-Load und die Prozess ID.
- Alternativ gibt es noch `$ top`, das ist jedoch nicht interaktiv und weniger komfortabel.
```
$ htop
```
- zum Beenden `q` oder alternativ `F10` drücken


### Temperaturüberwachung mit lm-sensors
```
$ sudo apt install lm-sensors
```
- Sensoren erkennen:
	- Immer mit `YES` bestätigen, um Sensoren zu erkennen
```
$ sudo sensors-detect
```
- Sensoren anzeigen lassen:
```
$ sensors
```


### acpi - Temperatur & Batterie Informationen/ Überwachung
```
$ sudo apt install acpi
```
- CPU-Temperatur anzeigen lassen:
```
$ acpi -t
```
- Ladeastatus:
```
$ acpi
```
- Akkustand und Gesamtkapazität:
```
$ acpi -i
```
- Weitere Parameter:
	- `-a` AC-Adapter
	- `-b` Batterie Informationen
	- `-c` Cooling Informationen


### [hwinfo](https://hwinfo.su/de/linux/)
```
$ sudo apt install hwinfo
```


### CPU-X
```
$ sudo apt install cpu-x
```


----------------------------------------------------------------------------------------------------------------


# 3. Medienwiedergabe

`Anleitung zuletzt bearbeitet am 4.2.2026`

### [vlc Media Player](https://www.videolan.org/vlc/index.de.html)
```
$ sudo apt install vlc
```


----------------------------------------------------------------------------------------------------------------


# 4. Poduktivität

`Anleitung zuletzt bearbeitet am 4.2.2026`

### [Visual Studio Code](https://code.visualstudio.com/)
- Debian Paket herunterladen
- entweder manuell installieren
- oder mit [GDebi](https://packages.debian.org/de/stable/gdebi) (Debian-Paket-Installationsmanager) komfortabel installieren


### [GDebi](https://packages.debian.org/de/stable/gdebi)
- Debian-Paket-Installationsmanager
```
$ sudo apt install gdebi
```


### [remmina](https://remmina.org/) - Remote Desktop Client
```
$ sudo apt install remmina
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


### Videoschnittprogramm [DaVinci Resolve](https://www.blackmagicdesign.com/de/products/davinciresolve)
- DaVinci Resolve ist ein professionelles Videoschnittprogramm von Blackmagic Design. Das Programm vereint Videoschnitt, Farbkorrektur, Effekte und vieles Mehr. Allerdings hat das Programm auch hohe Hardwareanforderungen, diese sollten vor der Installation geprüft werden.
- Die Software hat eine `kostenlose Version`, `ist proprietär, also leider NICHT Open Source`. Eine [Open Source](https://de.wikipedia.org/wiki/Open_Source) Alternative wäre z.B. [Shotcut](https://www.shotcut.org/),  mehr zu Shotcut im nächsten Punkt.
- Das Programm wird häufig auch von `professionellen Studios für Kinofilme verwendet`. 
- DaVinci ist unter anderem für Linux, MacOS und Windows verfügbar.

- Installation
	- Unter Linux kann es zu Problemen mit den Grafiktreibern, den Anforderungen und Abhängigkeiten kommen, sodass sich das Programm nach der Installation nicht öffnen lässt. Hier muss zu der entsprechenden Distribution geschaut werden, was bei der Installation zu beachten ist.
	- Blackmagic Design unterstützt eigentlich nur [CentOS](https://www.centos.org/) als Linux-Distribution, aber es gibt auch viele Community-Anleitungen für z.B. [Ubuntu](https://ubuntu.com/download) oder [Linux Mint](https://linuxmint.com/).
	- Um DaVinci Resolve installieren zu können, müssen zunächst die Abhängigkeiten geprüft/installiert werden, die Grafiktreiber ggf. angepasst und dann kann das entsprechende Linux-Paket von der Webseite heruntergeladen werden.


### Videoschnittprogramm [Shotcut](https://www.shotcut.org/)
- Shotcut ist ein [Open Source](https://de.wikipedia.org/wiki/Open_Source)-Videobearbeitungsprogramm mit vielen Funktionen und sowohl für Anfänger als auch für Fortgeschrittene geeignet.
- Ein Vorteil von Shotcut ist, dass die Hardwareanforderungen und auch der Ressourcenverbrauch deutlich geringer sind als bei DaVinci Resolve und es auch einfacher zu installieren ist.
- Shotcut ist unter anderem für Linux, MacOS und Windows verfügbar.

- Installation
	- Das Programm kann mit dem Befehl `$ sudo apt install shotcut` unter Linux installiert werden.
	- Alternativ ist [Shotcut auch als Flatpak](https://flathub.org/apps/org.shotcut.Shotcut) verfügbar.


----------------------------------------------------------------------------------------------------------------


# Linux Tools

`Anleitung zuletzt bearbeitet am 4.2.2026`

# 5. Metadata Cleaner/ mat2
- Metadaten in einer Datei enthalten viele Informationen.
- Kameras zeichnen Daten darüber auf, wann und wo ein Bild aufgenommen und welche Kamera verwendet wurde. 
- Office-Anwendungen fügen automatisch Autoren- oder Firmeninformationen zu Dokumenten hinzu.
- Nicht immer möchte man, dass diese Informationen für Dritte zugänglich sind.


### Mehr zu Metadaten
- [Weitere Informationen zu Metadaten und einem Tool, um Metadaten zu entfernen, hier](https://github.com/replay45/ethical-hacking-und-cybersecurity/tree/main/cyber-security)


----------------------------------------------------------------------------------------------------------------


# 6. [Warpinator](https://warpinator.com/)

`Anleitung zuletzt bearbeitet am 4.2.2026`

Warpinator ist ein Tool, womit Dateien und Ordner einfach zwischen Linux Computern oder alternativ auch an Android oder Windows Geräte im selben lokalen Netzwerk übertragen werden können.

- Warpinator für Linux, kann als [Flatpak](https://flathub.org/apps/org.x.Warpinator) installiert werden.
- Für Android gibt es mehrere Möglichkeiten. Entweder man lädt sich offizielle Version als [APK](https://warpinator.com/) herunter, oder man greift auf die inoffizielle Version aus dem [Play-Store](https://play.google.com/store/apps/details?id=slowscript.warpinator) zurück.
Aber auch im [F-Droid-Store](https://f-droid.org/de/packages/slowscript.warpinator/) ist der Warpinator zu finden.


----------------------------------------------------------------------------------------------------------------


# 7. [VPN](https://de.wikipedia.org/wiki/Virtual_Private_Network)

`Anleitung zuletzt bearbeitet am 23.4.2026`

## Was ist [WireGuard](https://www.wireguard.com/) ?
- Wireguard ist ein modernes und effizientes [VPN](https://de.wikipedia.org/wiki/Virtual_Private_Network)-Protokoll, welches schnelle und sichere VPN-Verbindungen ermöglicht.
- Zudem ist das Protokoll sowohl für einfache Konfiguration als auch für `hohe Sicherheit bekannt`.
- Das Protokoll wird nicht nur von VPN-Anbietern benutzt, es findet auch Verwendung bei modernen Routern, wie z.B. bei den [FritzBoxen](https://avm.de/).
- Somit kann man eine sichere Verbindung von seinem Handy/Tablet oder Laptop von überall aus, in das Heimnetz herstellen, was sich besonders bei der Nutzung von öffentlichen oder fremden WLANs anbietet, denn bei der VPN-Verbindung wird der eigene Netzwerkverkehr bis in das Heimnetz verschlüsselt. 
- Außerdem kann man über die VPN-Vebindung in das Heimnetz auch auf Geräte, wie Homeserver aus dem Heimnetzwerk zugreifen oder einen eigenen [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System), wie [Pi hole](https://pi-hole.net/) nutzen.

Mehr zu Pi hole und DNS-Servern unter [DNS-Server/Pi-hole](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/dns-server/Pi-hole.md) 


## [WireGuard](https://www.wireguard.com/) auf Windows/Android/iOS/MacOS
- Auf den gängigen Betriebssystemen gibt es eine offizielle App mit grafischer Oberfläche, in der man die Konfigurationsdatei benutzerfreundlich importieren kann.
- Für Linux gibt es leider noch keine "App" mit GUI.


## Full- / Split-Tunneling
In der Regel Unterschiedet man bei VPN-Verbindungen zwischen Fulltunneling und Splittunneling.

- Fulltunneling
    - Bei Fulltunneling werden so gut wie alle Pakete über die VPN-Verbindung gesendet, mit Ausnahme von ARP-Paketen, die aus technischen Gründen nicht über die VPN-Verbindung geleitet werden.
    - Um sich vor Tracking durch ISPs zuschützen oder seinen gesammten Traffic zum VPN-Server zu routen ist Fulltunneling geeignet.
    - Nachteil von Fulltunneling ist, dass die Last auf den VPN-Server erhöht wird, da dieser deutlich mehr Traffic verarbeiten muss.
    
- Splittunneling
    - Hingegen werden bei Splittunneling nur bestimmte Pakete/Pakettypen über die VPN-Verbindung geleitet.
    - Splittunneling hat den Vorteil der geringen Last, denn es werden nur bestimmte Pakete über die VPN-Verbindung geroutet.



## [WireGuard](https://www.wireguard.com/) einrichten - [Debian](https://www.debian.org/index.de.html) basierte Distributionen

`Die Anleitung wurde auf Kali Linux & Debian mit der GNOME Benutzeroberfläche getestet, sollte aber auf anderen Debian basierenden Distributionen, wie Ubuntu etc., problemlos funktionieren.`

`Für diese Anleitungen wird eine Wireguard-Konfigurationsdatei vom VPN-Server vorausgesetzt.`


- Hinweis zu [Ubuntu](https://ubuntu.com/download):
    - Zum Zeitpunkt der Erstellung dieser Anleitung ist es erst in Ubuntu 23.10 möglich, die Wireguard VPN-Verbindung grafisch zu starten.


### Wichtig - Name der Konfigurationsdatei
- Um Probleme bei der Einrichtung zu vermeiden, empiehlt es sich einen kurzen Namen für die Konfigurationsdatei zu wählen, wie z.B. `wg_config.conf`.
- Wer mehrere Verbindungen nutzt, kann die Konfigurationen mit Zahlen ergänzen, wie z.B. `wg_config01.conf`.


-----------------------------------------------------------------------------------------------------------------


# 7.1 Befehlszeilen-Methode - CLI
- Installation:
```
$ sudo apt update
$ sudo apt install wireguard resolvconf
$ sudo apt install wireguard-tools
```

- Konfiguration in /etc/wireguard verschieben
```
$ sudo mv /PLATZHALTER/VERZEICHNIS/wg-client01.conf /etc/wireguard/
$ ls -l /etc/wireguard/
```

- Berechtigungen für das Verzeichnis und die Konfig-Datei setzen
    - Bei `$ sudo chown root:USERNAME /etc/wireguard/wg-client01.conf` den Platzhalter `USERNAME` durch den eigenen Benutzernamen ersetzen.
```
$ ls -l /etc/wireguard/
$ sudo chown root:root /etc/wireguard/
$ whoami
$ sudo chown root:USERNAME /etc/wireguard/wg-client01.conf
$ sudo chmod 640 /etc/wireguard/wg-client01.conf
$ sudo chmod 755 /etc/wireguard
```

- Verbindung über Befehl starten/beenden:
```
$ sudo wg-quick up /etc/wireguard/wg-client01.conf
$ sudo wg show
```
```
$ wg-quick down /etc/wireguard/wg-client01.conf
$ ifconfig
$ ip a
```

- Verbindung über nmtui starten/beenden:
    - Durch das Setzen des `*` kann die Verbindung aufgebaut/beendet werden.
```        
$ nmtui-connect
```


# 7.2 [WireGuard](https://www.wireguard.com/) VPN-Konfiguration unter Debain/Debian basierten Systemen mit [NetworkManager/nmcli](https://wiki.ubuntuusers.de/NetworkManager/nmcli/) - CLI
- Für diese Methode sollte unter KEINEN Umständen das WireGuard Paket installiert sein, da dies zu Konflikten führt.
- WireGuard-Tools (wireguard-tools) kann installiert bleiben.
- Daher unbedingt folgenden Befehlausführen, um WireGuard zu entfernen.
```
$ sudo apt remove --purge wireguard
```

- Eine Verbindung hinzufügen
    - Konfig im WireGuard Server erstellen.
    - Konfig auf Linux-System verschieben und mit Befehl hinzufügen:
    - Bei `"NAME_DER_VERBINDUNG"` den Namen der Verbindung mit den Anführungszeichen angeben, z.B. `wg_config01`
```
$ nmcli connection modify "NAME_DER_VERBINDUNG" connection.autoconnect no
```

- Verbindung anzeigen
```
$ nmcli connection show
```

- Optional den automatischen Start der Verbindung deaktivieren
```
$ nmcli connection modify "NAME_DER_VERBINDUNG" connection.autoconnect no
```

- Überprüfen:
```
$ nmcli connection show "NAME_DER_VERBINDUNG" | grep autoconnect
```

- Verbindungen deaktivieren
```
$ nmcli connection down NAME_DER_VERBINDUNG
```

- Verbindung aufbauen
```
$ nmcli connection up NAME_DER_VERBINDUNG
```

- Wenn eine Distribution mit GUI verwendet wird können nun auch die Funktionen der GUI verwendet werden, um die Verbindung komfortabel zu starten/beenden.
- Alternativ können natürlich weiterhin die Befehle verwendet werden.


# 7.3 [WireGuard](https://www.wireguard.com/) VPN-Konfiguration mit GNOME (GNOME Einstellungen oder [NetworkManager/nm-connection-editor](https://wiki.ubuntuusers.de/NetworkManager/)) - GUI
Diese Methode ist unter [Ubuntu](https://ubuntu.com/download) erst ab der Version 24 möglich !
Diese Anleitung wurde unter [Kali Linux](https://www.kali.org/) & [Debian](https://www.debian.org/index.de.html) getestet.

- [NetworkManager/nm-connection-editor](https://wiki.ubuntuusers.de/NetworkManager/) öffnen
```
$ nm-connection-editor
```
- Im nm-connection-editor nun auf das `+`-Symbol klicken.
- Unter `Virtual`, `WireGuard` auswählen.
- `Create`
- WireGuard Konfigurationen einfügen, aus der erstellten Konfiguartion vom VPN-Server.



-----------------------------------------------------------------------------------------------------------------


# 7.4 VPN-Verbindung überprüfen/ Pakete überprüfen
- [Wireshark](https://www.wireshark.org/)
    - Um zu überprüfen, welche Pakete/Pakettypen über die VPN-Verbindung geleitet werden, kann das Tool [Wireshark](https://www.wireshark.org/) verwendet werden.

- IP Route
    - mit IP Route kann überprüft werden wie die Pakete geroutet werden, also über welche Verbindung/Netzwerkadapter die Pakete gesendet werden.
```
$ ip route
$ ip -4 route get 1.1.1.1
```

- DNS-Auflösung überprüfen
```
$ dig test.de
$ dig google.de
$ dig @9.9.9.9 test.de
```

- Verbindung überprüfen
```
$ ping 1.1.1.1
$ ping 9.9.9.9
```


-----------------------------------------------------------------------------------------------------------------
