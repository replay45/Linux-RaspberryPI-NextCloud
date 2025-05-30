# Speicherplatz in /boot-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) schaffen

`Anleitung verfasst am 8.5.2025`

`Anleitung für Debian-basierte Distributionen geeignet`


- Wenn beim Upgrade-Befehl nicht ausreichend Speicherplatz in `/boot` vorhanden ist, erscheint eine Warnmeldung, dass nicht ausreichend Speicherplatz vorhanden ist.
- Um Speicherplatz in `/boot` zu schaffen, müssen alte [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem))-Images und ggf. Header entfernt werden.


## Aktuell laufenden Kernel ermitteln
```
$ uname -r
```
- Ausgabe ist der aktuelle Kernel, diesen notieren.


## Liste aller installierten Kernel-Pakete anzeigen
```
$ dpkg --list 'linux-image-*' | grep '^ii'
```
- Ausgabe(n) notieren, außer die, die mit `$ uname -r` ermittelt wurde.
- Alle anderen sind alte Kernel, die nun entfernt werden können, um Speicherplatz zu schaffen.


## Alte Kernel entfernen

- automatisch (falls möglich)
```
$ sudo apt --purge autoremove
```

- manuell
	- Falls der obige Befehl nicht ausreichend Speicherplatz freigibt, gezielt ältere Kernel entfernen, z. B.:

```
$ sudo apt purge linux-image-VERSIONSNUMMER-kali-amd64
```
- Ersetzen der VERSIONSNUMMER durch die jeweilige Version aus dem zu löschenden Kernel.


## [GRUB](https://de.wikipedia.org/wiki/Grand_Unified_Bootloader) neu einlesen und abschließend Platz prüfen

- Nach dem Entfernen:
```
$ sudo update-grub
```
- Speicherplatz prüfen
```
$ df -h /boot
```


## Upgrade erneut starten
```
$ sudo apt update
$ sudo apt upgrade
```
```
$ sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```


-----------------------------------------------------------------------------------------------------------
