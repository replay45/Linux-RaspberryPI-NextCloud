# Kernel-Panic / GRUB-Fehler (fehlerhafter-Boot-Vorgang)/ boot-Partition voll

`Anleitung erstellt am 15.10.2025`

`Anleitung für Debian-basierte Distributionen / getestet auf Kali-Linux`


-------------------------------------------------------------------------------------------------------------


# Fehlerbild & Ursachen des Fehlerbildes (Kernel-Panic / Unable to mount root fs...)


### Fehlerbild
- Das System Bootet nicht mehr und gibt ein `Kernel-Panic` oder `Unable to mount root fs...` aus.
- Das bedeutet, dass der Kernel die Root-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) nicht einbinden kann, also das Haupt-Dateisystem auf dem das Betriebssystem liegt.
- Dadurch ergibt sich, dass das System nicht starten kann und der Panikmodus (Kernel-Panic) erscheint.
- Mögliche Fehlermeldungen: `Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)`


### Ursachen
- Ursachen dafür können ein fehlgeschlagenes [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem))-Update, ein fehlendes/fehlerhaftes initramfs oder eine volle /boot-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)), wodurch initramfs nicht geschrieben werden kann. 


### Was ist der [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) & Was ist initrd/ initramfs ?
- Was ist der [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) ?
	- Der [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) ist das Herzstück des Betriebssytems und übernimmt die Kommunikation zwischen Soft- und Hardware.
	- Beim Bootvorgang startet der Kernel zuerst und mountet die Root-[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) und lädt initrd/initramfs

- Was ist initrd/ initramfs
	- `initrd` steht für `Initial RAM Disk` und ist ein älter Begriff, hingen ist `Initramfs` der moderne Begriff und steht für `Initial RAM Filesystem`.
	- Dabei ist es ein temporäres Dateisystem, das vom Kernel beim Booten in den [RAM](https://de.wikipedia.org/wiki/Random-Access_Memory) geladen wird und enthält z.B. Treiber für Storage ([SATA](https://de.wikipedia.org/wiki/Serial_ATA)/[NVMe](https://de.wikipedia.org/wiki/NVM_Express)) oder für LUKS/LVM ([Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung)), damit der Kernel dann die Root-Partition mounten kann und das System starten kann.


# Mögliche Lösung um in das System zu booten
- Beim Boot-Vorgang im [GRUB](https://de.wikipedia.org/wiki/Grand_Unified_Bootloader)-Menü ([GRUB](https://de.wikipedia.org/wiki/Grand_Unified_Bootloader)=[Bootloader](https://de.wikipedia.org/wiki/Bootloader)) auf `Erweiterte Optionen` klicken.
	- Hinweis: Im Boot-Menü des [Bootloaders](https://de.wikipedia.org/wiki/Bootloader), kann man in der Regel nicht nur normal booten, sondern auch ins [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) wechseln, den "abgesicherten Modus" aktivieren und eben die Kernel-Version auswählen, von der gebootet werden soll.  
- Boot-Menü nun eine andere Kernel-Version auswählen und versuchen zu booten.
- Booten -> Wenn das System nun bootet zum nächsten Punkt springen, um die Reparatur durchzuführen.
- Sollte das system nicht booten, alle weiteren Kernel-Versionen ausprobieren.
- `Wenn keine weiteren Kernel-Versionen zur Auswahl stehen`, bleibt meist nur eine Neu-Installation des Systems, sofern LUKS/LVM-[Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) verwendet wird.
- `Wenn keine Festplattenverschlüsselung verwendet wird`, kann man versuchen das System mit einem Live-System z.B. von einem USB-Stick druch manuelle Anpassungen im eigentlichen Host-System, zu retten.


# Reparatur im System durchführen
- Folgendes funktioniert nur, wenn man über eine andere Kernel-Version in das system booten konnte.


### Aktuellen [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) prüfen
- Zeigt aktuellen Kernel, mit dem das System läuft:
	- Der Kernel der hierbei angezeigt wird, sollte unbedingt beibehalten werden, da er ja offensichtlich funktioniert.
```
$ uname -r
```

- Zeigt nur installierte Kernelpakete:
```
$ dpkg --list 'linux-image-*' | grep ^ii
```

- Prüfen welche Pakete in /boot liegen:
	- Jede Kernel-Version besteht aus einem Paar: "vmlinuz..." & "initrd.img..."
```
$ ls -lh /boot/
```

### Alte/ problematische [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) entfernen:
- Kernel/Pakete automatisch entfernen lassen:
```
$ sudo apt autoremove --purge && sudo apt autoclean
```

- Kernel/Pakete manuell entfernen:
	- Der Kernel aus `$ uname -r` wird, sollte unbedingt beibehalten werden, da er ja offensichtlich funktioniert.
```
$ sudo apt remove --purge linux-image-VERSIONSNUMMER linux-headers-VERSIONSNUMMER
$ sudo apt autoremove --purge && sudo apt autoclean
```

### Alte initrd-Dateien aus /boot entfernen
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


### Initramfs & [GRUB](https://de.wikipedia.org/wiki/Grand_Unified_Bootloader) neu erstellen (damit Boot mit aktuellem [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)) funktionieren kann)
- initramfs für den aktuellen Kernel bzw. für alle Kernel erstellen:
```
$ sudo update-initramfs -u -k all
```

- GRUB-Konfig aktualisieren:
	- Falls eine Warnung zu `os-prober` erscheint, kann diese in der Regel ignoriert werden, es sei denn auf anderen [Partitionen](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) des gleichen Speichermediums sind noch andere Betriebssystme installiert.
```
$ sudo update-grub
```

- OPTIONAL: Wenn man manuell einen bestimmten Kernel aktualisieren möchte (initrd & GRUB-Konfig)
```
$ sudo update-initramfs -u -k $(uname -r)
$ sudo update-grub
```


### /boot-Partition aufräumen
- Speicherplatz prüfen:
```
$ df -h /boot
```

- alte Kernel löschen (automatisch):
```
$ sudo apt autoremove --purge
```

- Kernel/Pakete manuell entfernen:
	- Der Kernel aus `$ uname -r` wird, sollte unbedingt beibehalten werden, da er ja offensichtlich funktioniert.
```
$ sudo apt remove --purge linux-image-VERSIONSNUMMER linux-headers-VERSIONSNUMMER
$ sudo apt autoremove --purge && sudo apt autoclean
```

- Nach jedem Kernel-Update:
```
$ sudo update-initramfs -u -k all
$ sudo update-grub
```

- Empfehlung: Wenn die /boot-Partition relativ klein ist, nur 1 bis 2 kernel behalten.


-------------------------------------------------------------------------------------------------------------
