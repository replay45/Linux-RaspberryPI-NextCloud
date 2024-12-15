# Linux Paketmanager (Debian-basierte Distributionen)

`Anleitung zuletzt bearbeitet am 11.12.2024`


## Inhaltsverzeichnis
1. installierte Pakete anzeigen
2. Paketmanager [apt](https://wiki.ubuntuusers.de/APT/)
3. Paketmanager [Flatpak](https://flatpak.org/)
4. Paketmanager [Snap](https://snapcraft.io/)


----------------------------------------------------------------------------------------------------------------


### 1. installierte Pakete anzeigen

- alle installierten Pakete anzeigen
```
$ dpkg --list
```

- nach einem bestimmten Paket suchen
```
$ dpkg --list | grep [PROGRAMMNAME]
```

- Beispiel
```
$ dpkg --list | grep "firefox"
```


----------------------------------------------------------------------------------------------------------------


# 2. Paketmanager [apt](https://wiki.ubuntuusers.de/APT/)
- APT ist ein Tool zur Debian-Paketverwaltung (Installation & Verwaltung von Programmen) auf Linux-Systemen.
- Das Ausführen von Softwareupdates und Upgrades ist ebenfalls in diesem Ordner unter [Updates-&-Upgrades](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/linux-Software-%26-Pakete) beschrieben.


### Pakete deinstallieren

- Deinstallation, Konfigurationsdateien werden behalten
```
$ sudo apt-get remove PAKETNAME
```

- Deinstallation & Entfernung der Konfigurationsdateien
```
$ sudo apt-get --purge remove PAKETNAME
```


----------------------------------------------------------------------------------------------------------------


# 3. Paketmanager [Flatpak](https://flatpak.org/)

`Hier verwendetes Betriebssystem: Kali mit der GNOME Benutzeroberfläche`


### Flatpak installieren

- Flatpak installieren
```
$ sudo apt install flatpak
$ sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
$ sudo apt update
$ reboot
```

- Flatpak-App-Store für GNOME-Software installieren
```
$ sudo apt install gnome-software-plugin-flatpak
```


### optional die Apps, die über Flatpak installiert werden, an die Kali-Themes anpassen

- Themes anpassen (Kali-Themes in den Apps)
```
$ mkdir -p ~/.themes
$ cp -a /usr/share/themes/* ~/.themes/
$ sudo flatpak override --filesystem=~/.themes/
```


### Flatpak verwenden

- Pakete, die über Flathub installiert wurden, updaten
```
$ flatpak update
```

- Liste installierter Programme
```
$ flatpak list
```

- Deinstallation von Programmen
```
$ sudo flatpak uninstall PAKETNAME
```

- kaputte Installation fixen
```
$ flatpak repair
```

- Verlauf anzeigen
```
$ flatpak history
```

- nicht mehr benötigte Runtimes und Extensions entfernen
```
$ flatpak uninstall --unused
```


> [Flatpak - Setup](https://flatpak.org/setup/Ubuntu)

> [Flatpak - wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/Flatpak/)

> [Flatpak auf Kali installieren](https://www.kali.org/docs/tools/flatpak/)


----------------------------------------------------------------------------------------------------------------


# 4. Paketmanager [Snap](https://snapcraft.io/)

`Hier verwendetes Betriebssystem: Kali mit der GNOME Benutzeroberfläche`


### Snap installieren

- Snap installieren
```
$ sudo apt update
$ sudo apt install snapd
$ sudo apt update
```

- snapd & snapd.apparmor starten
```
$ sudo systemctl enable --now snapd apparmor
$ reboot
```

- Funktionalität testen
```
$ snap install hello-world
$ hello-world
```

### Snap verwenden

- Snap-Pakete aktualisieren
```
$ sudo snap refresh 
```

- Sollte es zu Problemen kommen, kann der folgende Befehl genutzt werden, um Ereignisse anzuzeigen.
    - Falls der `snapd.apparmor service` deaktiviert ist, sollte der auszuführende Befehl im Terminal nach der Ausführung des Befehls erscheinen.
```
$ snap warnings
```


### [Snap App-Store installieren](https://snapcraft.io/docs/installing-snap-store-app)

```
$ sudo snap install snap-store
```

- Wenn der Store sich nicht über das Icon öffnen lässt, über das Terminal mit dem Befehl versuchen:
```
$ snap-store
```


> [Snap auf Kali installieren](https://snapcraft.io/docs/installing-snap-on-kali)


----------------------------------------------------------------------------------------------------------------