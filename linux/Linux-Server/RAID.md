# [RAID](https://de.wikipedia.org/wiki/RAID) & [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS)

`Anleitung erstellt am 24.1.2026`

`Anleitung getestet mit Ubuntu-Server 24.04 LTS`


--------------------------------------------------------------------------------------------------------------------------------------------


# Welche [RAID](https://de.wikipedia.org/wiki/RAID) Konfiguration wählen
- `RAID 0` - "Striping": Daten werden aufgeteilt, KEINE Redundanz/Ausfallsicherheit, mindestens 2 Festplatten
- `RAID 1` - "Mirroring": Daten werden gespiegelt, volle Redundanz, für 2 Festplatten geeignet, für Homeserver geeignet
- `RAID 5` - "Striping + Parität": Eine Festplatte für Parität, Redundanz bei Ausfall einer Festplatte, mindestens 3 Festplatten
- `RAID 10` - "Mirroring + Striping": Kombination aus RAID 1 und 0, hohe Performance & hohe Ausfallsicherheit, mindestens 4 Festplatten


--------------------------------------------------------------------------------------------------------------------------------------------


# Anforderungen für RAID-Konfigurationen
- Abhängig von der gewählten RAID-Konfiguration sollte man folgende Dinge beachten:
    - Es kann von Vorteil sein, wenn die Speichermedien vom gleichen Hersteller sind.
    - Die Größe der [Partitionen](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) sollte immer die identisch sein.
    - Jede Partition sollte einen unterschiedlichen Namen haben, z.B. `luks_raid_data1`, `luks_raid_data2` oder `raid_data1`, `raid_data2`
    - Für das Dateisystem bei Linux-System, ist das Partitonsschema [Ext4](https://de.wikipedia.org/wiki/Ext4) geeignet.


--------------------------------------------------------------------------------------------------------------------------------------------


# [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) mit [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS) (bevor RAID konfiguriert wird)
- Nicht in allen Szenarien ist es Sinnvoll eine Verschlüsselung einzurichten !
- Außerdem eignen sich auch nicht alle Speichermedien dafür. Durch Verschlüsselung kann es immer zu langsameren Lese- und Schreibraten kommen!


### verschlüsselte Partition erstellen
- Im Optimalfall wird die Erstellung der verschlüsselten Partition von einem anderen Linux-System, als dem [Ubuntu-Server](https://ubuntu.com/download/server) vorgenommen.
- Dafür eignet sich besonders ein [Linux-Live-USB](https://wiki.ubuntuusers.de/Live-USB/).
    - Ein beliebiges Live-Linux wählen und auf einen USB-Stick flashen.
    - Geeignet sind z.B. [Ubuntu-Live](https://wiki.ubuntuusers.de/Desktop-Live-Medium/). [Kali-Live](https://www.kali.org/get-kali/) oder [Linux Tails](https://tails.net/), da diese das Programm [GNOME-Disks](https://apps.gnome.org/en-GB/DiskUtility/) mitbringen, in welchem man benutzerfreundlich die Festplatten formatieren und mit [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS) verschlüsseln kann.

- Partitionierung mit Verschlüsselung vornehmen:
    - Ubuntu-Server ausschalten
    - OPTIONAL: Wer sichergehen möchte, kann das Speichermedium mit Ubuntu-Server trennen/temporär ausbauen um, die zu partitionierenden Festplatten einfacher zu identifizieren (Nicht vergessen, dafür das System vom Strom zu trennen!).
    - USB-Stick anschließen und Live-Linux-System booten.
    - [GNOME-Disks](https://apps.gnome.org/en-GB/DiskUtility/) oder ein vergleichbares Programm öffnen.
    - Die korrekten Speichermedien identifizieren.
    - Nun eine neue Partition erstellen mit `Ext4` und den Haken bei `LUKS` setzen.
    - Einen `eindeutigen & einzigartigen Namen` für die Partition wählen, z.B. für das primäre Speichermedium `luks_raid_data1` und das sekundäre `luks_raid_data2` 
    - Passphrase eingeben und z.B. in einem [Passwortmanager](https://de.wikipedia.org/wiki/Kennwortverwaltung) speichern.
    - Für eine einfachere Wartung und für eine einfacherere RAID-Konfiguration sollten die Partitionen, die im RAID-Array sind, die `gleiche Passphrase` haben. Auch wenn technisch gesehen unterschiedliche Passphrasen möglich sind, bringt dies jedoch keinen Sicherheitsvorteil, daher wird empfohlen die gleiche Passphrase bei der Festplattenverschlüsselung, für eine RAID-Konfiguration zu wählen.
    - Nun das Live-System herunterfahren, USB-Stick entfernen und alle benötigten Speichermedien ggf. wieder anschließen und in [Ubuntu-Server](https://ubuntu.com/download/server) booten.


### überprüfen
- Da die LUKS-verschlüsselten Partitionen noch nicht eingehängt wurden, können diese natürlich noch nicht angezeigt werden, daher werden nur die Festplatten selber angezeigt !

- Übersicht aller "Blockgeräte" und Mountpunkte:
```
$ lsblk -f
```

### [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS)-verschlüsselte Partitionen einhängen (mit Key-File)
- Entschlüsselung über Key-File
    - Bei dieser Methode wird die Passphrase für die LUKS-verschlüsselte Partitionen in einem Key-File auf der Festplatte des Betriebssystems gespeichert.
    - Daher wäre es natürlich gut, wenn die Festplatte mit dem Betriebssystem ebenfalls mit LUKS-verschlüsselt ist, sonst wäre der Schlüssel ja "ungeschützt".
    - Für diese Methode wird nicht zwinged [TPM 2.0](https://de.wikipedia.org/wiki/Trusted_Platform_Module) verwendet, jedoch wenn das Betriebssystem automatisch beim booten entschlüsseltet werden soll, dann benötigt man dafür trotzdem TPM 2.0.

- typischer Ablauf:
    - Betriebssystem startet, wird durch TPM entschlüsselt
    - System startet und Festplatten (RAID-Array) wird dann mit über ein Key-File entschlüsselt
    - RAID-Dateisystem wird gemountet


--------------------------------------------------------------------------------------------------------------------------------------------


# Anleitung: [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS)-verschlüsselte Partitionen einhängen (mit Key-File)

- Key-File erstellen
```
$ sudo mkdir -p /root/keyfiles/
```

- Keyfile erstellen
    - Der Befehl erstellt eine 4KB zufällige Datei, die als "Key" für LUKS genutzt werden kann.
    - Diese ersetzt die Passphrase beim automatischen Entschlüsseln.
```
$ sudo dd if=/dev/urandom of=/root/keyfiles/luks_raid_data1+2.key bs=4096 count=1
```

- Berechtigungen setzen
```
$ sudo chmod 700 /root/keyfiles/
$ sudo chmod 400 /root/keyfiles/luks_raid_data1+2.key
```

- Optional: Falls nötig vergebene Partitonsnamen bei Erstelleung der Ext4-LUKS-Partition einsehen
```
$ lsblk --fs
```

- Key-File zu den LUKS-[Partitionen](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) hinzufügen
    - Nun die Passphrase der LUKS-Festplattenverschlüsselung bei Aufforderung eingeben.
```
$ sudo cryptsetup luksAddKey /dev/sdbX /root/keyfiles/luks_raid_data1+2.key
$ sudo cryptsetup luksAddKey /dev/sdcX /root/keyfiles/luks_raid_data1+2.key
```

- überprüfen
    - Es sollte ein Keyslot mit dem Typ "Key-File" existieren.
```
$ sudo cryptsetup luksDump /dev/sdbX
$ sudo cryptsetup luksDump /dev/sdcX
```

- UUID der Partitionen herausfinden und temporär notieren
    - UUID von sdbX und sdcX (z.B. sdb1/ sdc1) temporär notieren.
    - Unter "FSTYPE" sollte etwa "crypto_LU 2" stehen.
```
$ lsblk -f
```

- `/etc/crypttab` anpassen
```
$ sudo nano /etc/crypttab
```
```
luks_raid_data1 UUID=UUID-von-sdbX /root/keyfiles/luks_raid_data1+2.key luks
luks_raid_data2 UUID=UUID-von-sdcX /root/keyfiles/luks_raid_data1+2.key luks
```

- Systemd und Initramfs aktualisieren
```
$ sudo systemctl daemon-reload
$ sudo update-initramfs -u
```

#### testen, ob Keyfile funktioniert
- manuell testen
    - Beim ausführen der Befehle sollte KEINE Passphrase mehr nötig sein
```
$ sudo cryptsetup luksOpen /dev/sdbX luks_raid_data1 --key-file /root/keyfiles/luks_raid_data1+2.key
$ sudo cryptsetup luksOpen /dev/sdcX luks_raid_data2 --key-file /root/keyfiles/luks_raid_data1+2.key
$ ls /dev/mapper/
```

- neu starten
```
$ sudo reboot
```

- überprüfen
    - Wenn luks_raid_data1 und luks_raid_data2 angezeigt werden, dann wurden die Partitionen entschlüsselt.
```
$ ls /dev/mapper/
```


--------------------------------------------------------------------------------------------------------------------------------------------


# RAID-Konfiguration vornhemen & Mount-Point erstellen
- Wenn die manuelle- & automatische-Entschlüsselung erfolgreich funktioniert, dann sollte nun die RAID-Konfiguration vorgenommen werden und anschließend ein Mount-Point erstellt werden.



- RAID erstellen
    - Falls nötig evtl. "luks_raid_data1" und "luks_raid_data2" anpassen.
    - `--level=1` ist = RAID1
    - `--raid-devices=2` = 2 Festplatten/SSDs
    - mit `y` bestätigen
    - OPTIONAL: Wenn man ein weiteres RAID-ARRAY ergänzen möchte, muss man einen anderen Mount-Point angeben, z.B. `/dev/md1` usw.
```
$ sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/mapper/luks_raid_data1 /dev/mapper/luks_raid_data2
```

- überprüfen, ob bei `md0` `active` steht:
    - Hinter `resync` wird in "%" der Wert angegeben, in wie weit die Synchronisation abgeschlossen ist.
    - Um den Status erneut einzusehen, den Befehl erneut eingeben.
    - Grundsätzlich: Wenn `resync` nicht angezeigt wird, wird aktuell keine Synchronisation vorgenommen, da diese ggf. bereits abgeschlossen ist.
```
$ cat /proc/mdstat
```

#### WICHTIG
- `Warten`, bis die Synchronisation im RAID fertig ist, bevor die weiteren Schritte vorgenommen werden !
    -  Wenn `resync` nicht angezeigt wird, wird aktuell keine Synchronisation vorgenommen, da diese ggf. bereits abgeschlossen.
    - Bei größeren Festplatten an dieser Stelle am besten mit `$ logout` ausloggen und zu einem späteren Zeitpunkt fortfahren.
    - Status einsehen:
```
$ cat /proc/mdstat
```

- RAID (mdadm) persistieren
```
$ sudo mdadm --detail --scan | sudo tee /etc/mdadm/mdadm.conf
```

- OPTIONAL: RAID Konfig prüfen
    - Mit dem Befehl kann geprüft werden, ob alle RAID-Konfigurationen korrekt hinzugefügt wurden.
    - Wenn weitere RAIDs hinzugefügt werden, sollten sie automatisch in der Konfgiuration erscheinen.
```
$ sudo mdadm --detail --scan --verbose
```

- Wenn weitere/ nachträgliche RAIDs hinzugefügt werden:
    - Wenn weitere oder nachträgliche RAIDs hinzugefügt werden, dann sollten diese automatisch in der mdadm-Konfiguration erscheinen, falls nicht, sollte geprüft werden, ob das RAID aktiv ist `$ sudo mdadm --detail /dev/mdXXX` und wenn es aktiv ist, den nächsten Schritten folgen.


- Prüfen, ob `/etc/mdadm/mdadm.conf` eine Zeile entgält die so ähnlich aussieht: `ARRAY /dev/md0 metadata=1.2 UUID=XXXX name=HOSTNAME:0`
```
$ cat /etc/mdadm/mdadm.conf
```

- Falls die Zeile nicht erscheint, oder weitere / nachträgliche RAIDs hinzugefügt werden sollen:
    - UUID des RAIDs herausfinden: `$ sudo mdadm --detail /dev/mdXXX`
    - Folgende Zeile `ARRAY /dev/mdXXX metadata=1.2 UUID=XXXX` in `$ sudo nano /etc/mdadm/mdadm.conf` hinzufügen.
    - Üblicherweise heißt das erste RAID `md0`, weitere RAIDs heißen dann z.B. `md1`, `md2` usw. 


- Initramfs aktualisieren
```
$ sudo update-initramfs -u
```

- Dateisystem im RAID `/dev/md0` erstellen
    - Mit dem folgenden Befehl wird ein [Ext4](https://de.wikipedia.org/wiki/Ext4)-Dateisystem für das RAID erstellt, da das RAID selber noch keines hat.
    - grundsätzlich werden bei diesem Vorgang alle Daten auf den Festplatten im RAID gelöscht.
```
$ sudo mkfs.ext4 /dev/md0
```

- RAID überprüfen:
```
$ cat /proc/mdstat
$ sudo mdadm --detail /dev/md0
```


## Mount-Point
- Nun kann ein Mount-Point erstellt werden, damit das RAID als ein Datenspeicher eingehängt wird.
- Grundsätzlich gibt es mehrere Punkte, an denen man etwas "mounten" kann, dabei eignet sich in diesem Fall `/mnt/...` z.B. `/mnt/RAID-Array-Data1-2`, wenn weitere Arrays hinzugefügt werden, dann: `/mnt/RAID-Array-Data3-4`...

### Mount-Point erstellen
- Pfad für Mount-Point erstellen
```
$ sudo mkdir /mnt/RAID-Array-Data1-2
```

- Rechte setzen
    - aktuellen Benutzernamen herausfidnen
    - `administrator:administrator`: ist der Besitzer und die Gruppe für den Ordner (Mount-Point), ggf. Befehl anpassen
    - `755`: Standardrechte für den Ordner (Mount-Point), bedeutet, der Besitzer hat volle Rechte (lesen, schreiben, ausführen), andere (zukünftige) Dienste dürfen nur lesen und ausführen.
    - Es lassen sich dann natürlich Unterordner für andere Dienste erstellen, z.B. [Webserver](https://de.wikipedia.org/wiki/Webserver), [Nextcloud](https://nextcloud.com/de/), [Fileserver](https://de.wikipedia.org/wiki/Dateiserver) usw.
```
$ whoami
$ sudo chown -R administrator:administrator /mnt/RAID-Array-Data1-2
$ sudo chmod 755 /mnt/RAID-Array-Data1-2
```

- RAID-Konfiguration manuell mounten
```
$ sudo mount /dev/md0 /mnt/RAID-Array-Data1-2
```

- überprüfen
```
$ df -h | grep RAID-Array-Data1-2
```

- `UUID-von-md0` herausfinden
```
$ blkid /dev/md0
oder
$ sudo mdadm --detail /dev/md0
```

- automatischen Mount (beim Systemstart) einrichten
    - Mit `STRG+X` speichern und schließen
```
$ sudo nano /etc/fstab
```
```
UUID=UUID-von-md0 /mnt/RAID-Array-Data1-2 ext4 defaults,nofail  0 2
```

- Nun sollte der RAID-Array beim booten automatisch eingehängt werden.
    - Nach dem Neustart prüfen, ob alles richtig gemountet wurde
```
$ cat /proc/mdstat
$ ls /mnt/RAID-Array-Data1-2
$ mount | grep /mnt/RAID-Array-Data1-2
```

- Falls Probleme auftreten Logs prüfen:
```
$ sudo journalctl -xe | grep -i mdadm
$ sudo journalctl -xe | grep -i mount
```

- neu starten
```
$ sudo reboot
```

#### (Optional) Ordner für Dienste unter `/mnt/RAID-Array-Data1-2` erstellen
    - Hier können nun Unterordner für unterschiedliche Dienste erstellt werden, auf denen dann die Daten der Dienste liegen sollen.
    - Hier sind ein paar Beispiele.
```
$ sudo mkdir -p /mnt/RAID-Array-Data1-2/Nextcloud
$ sudo mkdir -p /mnt/RAID-Array-Data1-2/Filesharing
$ sudo mkdir -p /mnt/RAID-Array-Data1-2/DIENSTNAME
```

- Berechtigungen für diese Ordner setzen
    - Berechtigungen können auch gesetzt werden, wenn der Dienst auf den Server installiert und eingerichtet wird.
```
$ sudo chown -R <dienst-user>:<dienst-group> /mnt/RAID-Array-Data1-2/Nextcloud
$ sudo chown -R <dienst-user>:<dienst-group> /mnt/RAID-Array-Data1-2/Filesharing
$ sudo chown -R <dienst-user>:<dienst-group> /mnt/RAID-Array-Data1-2/DIENSTNAME

$ sudo chmod 755 /mnt/RAID-Array-Data1-2/Nextcloud
$ sudo chmod 755 /mnt/RAID-Array-Data1-2/Filesharing
$ sudo chmod 755 /mnt/RAID-Array-Data1-2/DIENSTNAME
```


--------------------------------------------------------------------------------------------------------------------------------------------

