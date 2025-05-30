# Updates & Upgrades (Debian-basierte Distributionen)

`Anleitung zuletzt bearbeitet am 7.2.2025`


# Paketmanager: [apt](https://wiki.ubuntuusers.de/APT/) & [apt-get](https://wiki.ubuntuusers.de/apt/apt-get/)


- Was ist ein Paketmanager ?
    - Ein Paketmanager wird zur komfortablen Verwaltung von Software im Terminal genutzt.
    - Dazu gehören das Installieren, Aktualisieren und Deinstallieren.
    - Mehr zur [Paketverwaltung](https://de.wikipedia.org/wiki/Paketverwaltung)


- Was ist der Unterschied zwischen [apt](https://wiki.ubuntuusers.de/APT/) & [apt-get](https://wiki.ubuntuusers.de/apt/apt-get/) ?
    - Die Paketmanager `apt` und `apt-get` sind nicht das Gleiche, auch wenn beide zur Verwaltung von Software im Terminal verwendet werden.
    - `apt` ist eine `Weiterentwicklung` von `apt-get` mit dem Ziel, die Benutzerfreundlichkeit zu erhöhen.


- Version des Systems überprüfen:

```
$ cat /etc/os-release
```


# Update Befehle


- Paketmanger [apt](https://wiki.ubuntuusers.de/APT/):
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
$ sudo apt full-upgrade
```
```
$ sudo apt autoremove
```
```
$ sudo apt autoclean
```


- Paketmanger [apt-get](https://wiki.ubuntuusers.de/apt/apt-get/):
```
$ sudo apt-get update
```
```
$ sudo apt-get upgrade
```
```
$ sudo apt-get dist-upgrade
```
```
$ sudo apt-get full-upgrade
```
```
$ sudo apt-get autoremove
```
```
$ sudo apt-get autoclean
```


- Kombo-Befehl:
```
$ sudo apt-get update -y && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y && sudo apt-get autoremove -y && sudo apt-get autoclean -y
```

-----------------------------------------------------------------------------------------------------------------


# 2. nach Kernel Update treten Probleme beim booten auf - Module & Treiber in den "initramfs" laden

Wenn nach einem-Kernel Update Probleme auftreten, kann der folgende Befehl genutzt werden, um das initramfs-Abbild auf den neuesten Stand zu bringen.

```
$ sudo update-initramfs -u
```

----------------------------------------------------------------------------------------------------------------
