# Speicherplatz in /boot-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) schaffen

`Anleitung verfasst am 8.5.2025, zuletzt bearbeitet am 15.10.2025`

`Anleitung für Debian-basierte Distributionen / getestet auf Kali-Linux`


-------------------------------------------------------------------------------------------------------------


# Speicherplatz in /boot-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) schaffen
- Wenn beim Upgrade-Befehl nicht ausreichend Speicherplatz in `/boot` vorhanden ist, erscheint ggf. eine Warnmeldung, dass nicht ausreichend Speicherplatz vorhanden ist.
- Um Speicherplatz in `/boot` zu schaffen, müssen alte [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem))-Images und ggf. Header entfernt werden.


### /boot-Partition aufräumen
- Speicherplatz prüfen:
```
$ df -h /boot
```


## Aktuell laufenden Kernel ermitteln
- Ausgabe ist der aktuelle Kernel, diesen notieren.
```
$ uname -r
```


## Liste aller installierten Kernel-Pakete anzeigen
- Ausgabe(n) notieren, außer die, die mit `$ uname -r` ermittelt wurde.
- Alle anderen sind alte Kernel, die nun entfernt werden können, um Speicherplatz zu schaffen.
```
$ dpkg --list 'linux-image-*' | grep '^ii'
```


## Alte Kernel entfernen
- alte Kernel löschen (automatisch):
```
$ sudo apt --purge autoremove
$ sudo apt autoclean
```

- Kernel/Pakete manuell entfernen:
	- Der Kernel aus `$ uname -r` wird, sollte unbedingt beibehalten werden, da er ja offensichtlich funktioniert.
```
$ sudo apt remove --purge linux-image-VERSIONSNUMMER linux-headers-VERSIONSNUMMER
$ sudo apt autoremove --purge && sudo apt autoclean
```


## Alte initrd-Dateien aus /boot entfernen
> Dieser Teil gehöhrt zur Anleitung [Kernel-Panic-initramfs-Grub-Fehler](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Fehlerbehebungen) ( Anleitung ebenfalls in diesem Ordner) und ist mit dieser Anleitung verwandt. Dort finden sich mehr technische Informationen und Details sowie Anleitungen zum Kernel-Panic (Fehler wenn man die Fehlermeldung ignoriert hat und das System dann nicht mehr startet).

- Prüfen, ob `/boot` noch alte initrd/initramfs-Dateien enthält:
```
$ ls -lh /boot/
```

- Falls `veraltete initrd.img...` vorhanden sind, dann mit folgendem Befehl entfernen:
	- Auf die genaue Version achten !
	- Nicht die Dateien des aktuell verwendeten Kernels löschen !
```
$ sudo rm /boot/initrd.img-VERSIONSNUMMER
```

- Nochmal eine Bereinigung durchführen:
```
$ sudo apt autoremove --purge && sudo apt autoclean
```


## [GRUB](https://de.wikipedia.org/wiki/Grand_Unified_Bootloader) neu einlesen und abschließend Platz prüfen
- Nach jedem Kernel-Update:
	- initramfs für den aktuellen bzw. alle Kernel erstellen
	- GRUB-Konfig aktualisieren
	- Wenn die /boot-Partition relativ klein ist, nur 1 bis 2 kernel behalten.
```
$ sudo update-initramfs -u -k all
$ sudo update-grub
```


- Speicherplatz prüfen:
```
$ df -h /boot
```


## Upgrade erneut starten
```
$ sudo apt update
$ sudo apt upgrade
```
```
$ sudo apt full-upgrade
$ sudo apt autoremove && sudo apt autoclean
```


-------------------------------------------------------------------------------------------------------------

- Mehr technische Informationen und Details sowie Anleitungen zum Kernel-Panic (Fehler wenn man die fehlermeldung ignoriert hat und das System dann nicht mehr startet), finden sich in der Anleitung [Kernel-Panic-initramfs-Grub-Fehler](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Fehlerbehebungen) ebenfalls in diesem Ordner.
