# Linux Systemoptimierung

`Anleitung zuletzt bearbeitet am 15.12.2024`


## Inhaltsverzeichnis
1. auf Kali mit [XFCE](https://www.xfce.org/) die Systemsprache ändern
2. Dock Kali Linux [GNOME](https://www.gnome.org/) Fix
3. Autostart bei Kali Linux unter [GNOME](https://www.gnome.org/)
4. App Icons `.desktop` in Linux hinzufügen
5. [GNOME](https://www.gnome.org/) System Monitor
6. nützliche Shortcuts unter Linux


----------------------------------------------------------------------------------------------------------------


# 1. auf Kali mit [XFCE](https://www.xfce.org/) die Systemsprache ändern

### Systemsprache auf `Deutsch` ändern
```
$ sudo nano /etc/locale.gen
```

- mit `STRG+S` speichern und mit `STRG+X` beenden
```
$ sudo locale-gen
```
```
$ sudo localectl set-locale LANG=de_DE.UTF-8
```


----------------------------------------------------------------------------------------------------------------


# 2. Dock Kali Linux [GNOME](https://www.gnome.org/) Fix
- Benutzeroberfläche:
	- Diese Anleitung bezieht sich auf die Benutzeroberfläche `GNOME` !


### Wenn das Dock (Taskleiste in Linux) unter Gnome auf dem Desktop ausgeblendet wird und erst im App-Menu gezeigt wird.


- Fix:
	- In der App-Übersicht nach `Exensions` suchen.
	- Überprüfen, dass `Extensions` aktiviert ist und unter `Dash to Dock` (sollte aktiviert sein) auf die Option `Settings` klicken.
	- Dann die Einstellungen überprüfen und die gewünschten Änderungen vornehmen.


----------------------------------------------------------------------------------------------------------------


# 3. Autostart bei Kali Linux unter [GNOME](https://www.gnome.org/)


### Apps zum Autostart hinzufügen/ entfernen
- Dafür die App `Tweaks` unter Gnome aus der App-Übersicht öffnen.
- Danach zum Reiter `Startup Applications` wechseln.
- Hier können die Programme nun entfernt werden.
- Mit dem `+` Symbol können weitere Programme hinzugefügt werden.


----------------------------------------------------------------------------------------------------------------


# 4. App Icons `.desktop` in Linux hinzufügen


### A. `.desktop` Dateien finden
- Dateimanager öffnen
- in den Pfad `home/user/share/applications` wechseln.
- Nun können die `.desktop` Dateien kopiert und auf den Desktop eingefügt werden.
- Auf die ausgegraute Verknüpfung einen Rechtsklick machen und auf `allow launching` klicken.



### B. Weitere Programme, die wo anders aufgeführt sind
- Dateimanager öffnen
- auf die 3 Punkte oben rechts klicken
- in dem Menü das Häkchen bei `show Hidden Files` setzen
- Dateimanager öffnen
- in den Pfad `home/user/.local/share/applications` wechseln.
- Nun können die `.Desktop` Dateien kopiert und auf den Desktop eingefügt werden.
- Auf die ausgegraute Verknüpfung einen Rechtsklick machen und auf `allow launching` klicken.


----------------------------------------------------------------------------------------------------------------


# 5. [GNOME](https://www.gnome.org/) System Monitor

`verwendetes System: Kali mit GNOME`

- In GNOME kann man sich in der Kontrollleiste neben dem Quick-Panel Systeminformationen, wie CPU- oder RAM-Auslastung, aus dem System Monitor (Taskmanager von GNOME) anzeigen lassen.


- Dafür muss man die App `Erweiterungen` bzw. `Extensions` öffnen und die Erweiterung für den System Monitor aktivieren.
Nun sollte ein Symbol in Leiste neben dem Quick-Panel erscheinen.
Mit einem Rechtsklick kann man jetzt auswählen, welche Informationen man in der Info-Leiste sehen möchte.


### Fix für System Monitor Erweiterung, wenn Deaktivierung gefordert, aber diese ausgegraut ist
- Es kann vorkommen, dass nach einem Fehler die Extension für den System Monitor nicht mehr ordnungsgemäß funktioniert und empfohlen wird, die Extension zu deaktivieren, jedoch die Option zum deaktivieren ausgeraut ist.

- Hier der Fix:
```
$ sudo apt update
```
```
$ sudo apt install gnome-shell-extension-system-monitor --reinstall
```
```
$ dconf reset -f /org/gnome/shell/extensions/
```

- GNOME Shell neu starten:
	- `Alt + F2` drücken, `r` eingeben und `Enter` drücken.
	- Alternativ den Benutzer abmelden und erneut anmelden.


----------------------------------------------------------------------------------------------------------------


# 6. nützliche Shortcuts unter Linux:


Im Terminal Text vergrößern/ verkleinern:
```
ctrl + / ctrl -
```

neues Terminal:
```
strg + shift + n
```

Fenster wechseln:
```
alt + tab
```
```
win + tab
```
```
alt + esc
```

Desktop anzeigen:
```
win + d
```

Fenster anordnen:
```
win + Pfeil links / win + Pfeil rechts
```

Vollbild:
```
win + Pfeil nach oben
```

Im Dateimanager versteckte Dateien anzeigen/ ausblenden:
```
strg + h
```

Fenster schließen:
```
alt + F4
```

Bildschirm sperren:
```
win + l
```

Dateien umbenennen:
```
F2
```


----------------------------------------------------------------------------------------------------------------
