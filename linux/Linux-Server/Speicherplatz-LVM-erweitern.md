# Speicherplatz im LVM auf einem Ubuntu-Server erweitern

`Anleitung erstellt am 17.11.2026`


### Warum existiert dieses Problem überhaupt?
- Ubuntu verwendet standardmäßig LVM (Logical Volume Manager). Dabei gibt es drei Ebenen:
```
Physische Platte (sda, z.B. 60 GB)
   └── Physische Partition (sda3, z.B. 58 GB)
         └── Volume Group "ubuntu-vg"   → ein Speicher-Pool
               └── Logical Volume "ubuntu-lv"   → das, was als "/" gemountet ist
                     └── Dateisystem (ext4)   → das, was df -h anzeigt
```
- Der Ubuntu-Server-Installer legt das Logical Volume standardmäßig nur mit etwa 50 % der Volume-Group-Größe an.
- Der freie Platz ist eigentlich für Snapshots ö.ä. gedacht, führt aber dazu, dass viel Platz ungenutzt bleibt.

### Speicherplatz erweitern
- Speichermedien überprüfen
    - zeigt Geräte-Struktur und Plattengröße
```
$ lsblk
```

- Filesystem/Partitionen überprüfen
```
$ df -h
```

- Freien verfügbaren Speicherplatz  in der Volume Group überprüfen
```
$ sudo vgdisplay ubuntu-vg | grep -i free
```

- Falls Speicherplatz verfügbar ist, kann dieser erweitert werden
    - Der Befehl kann im Live Betrieb ausgeführt werden.
    - Falls der Server eine Virtuelle Maschine ist und der HyperVisor Snapshots unterstützt kann an dieser Stelle sicherheitshalber ein Snapshot der VM angelgt werden.
```
$ sudo lvextend -l +100%FREE -r /dev/ubuntu-vg/ubuntu-lv
```

- Abschließen überprüfen
```
$ df -h /
```

------------------------------------------------------------------------------------------------------
