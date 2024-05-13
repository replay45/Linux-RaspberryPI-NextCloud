# Linux Skripte


# 1. Linux Bash `.sh Skripte` erstellen



- sh Skripte können z.B. Befehle für das Terminal beinhalten, wodurch z.B. Abläufe automatisiert werden können.


- Um das Skript für Linux als `sh Skript` kennbar zu machen, muss in der ersten Zeile das Folgende stehen:
```
#!/bin/bash
```



__Hinweis__:
Die Endung des Skiptes ist Linux egal. Dies bedeutet, dass das Skript NICHT zwingend als `.sh` durch die Endung gekennzeichnet sein muss. Andere Endungen, wie z.B. `.txt` funktionieren daher auch.



Beispiel für ein Skript:
```
#!/bin/bash
whoami
```


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# 2. `.sh` Skripte in Kali-Linux autostarten



Mit dem Terminal in das Verzeichnis `/home/kali` gehen

```
$ ls -a -l
```

nun sollte eine `.profile` Datei zu finden sein.

```
$ sudo nano .profile
```

Bis an das Ende der Datei scrollen und neue Zeile einfügen.

```
/verzeichnis/verzeichnis/Datei.sh &
```


__Hinweis__:
Wenn das Skript das Terminal mit dem Prozess blockiert, einfach ein `&` am Ende ergänzen.


__Beispiel__:

```
/home/kali/Documents/dein-skript.sh &
```

-----------------------------------------------------------------------------------------------------------------


# 3. Skripte in Kali mit einer `.desktop` verknüpfen


`.sh Datei` erstellen (z.B. mit einem Command etc.)
```
$ chmod +x /Pfad/zum/deinem/skript.sh
```


Nun .desktop erstellen und Befehl in das Terminal einfügen:
```
$ nano ~/.local/share/applications/dein_skript.desktop
```


und in die `.desktop_Datei` Folgendes einfügen:
```
[Desktop Entry]
Version=1.0
Type=Application
Name=Dein Skript
Comment=Hier kannst du eine kurze Beschreibung einfügen
Exec=/Pfad/zum/deinem/skript.sh
Icon=/Pfad/zum/deinem/Icon.png  # Optional: Passe dies an, wenn du ein Icon verwenden möchtest
Terminal=false
StartupNotify=false
```

Danach folgenden Befehl in das Terminal einfügen:
```
$ update-desktop-database ~/.local/share/applications/
```


__Datei ausführbar machen__:
- Um eine Datei ausführbar zu machen, muss mit einem Rechtsklick das Menü geöffnet werden und der Haken bei `ausführbar` gesetzt werden.




-----------------------------------------------------------------------------------------------------------------
