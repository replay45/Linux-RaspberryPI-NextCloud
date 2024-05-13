# Linux Systemoptimierung



# 1. auf Linux die Systemsprache ändern


### Systemsprache auf `Deutsch` ändern unter Linux


- Befehle:
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


# 2. Dock Kali-Linux GNOME Fix



Benutzeroberfläche:
	- Diese Anleitung bezieht sich auf die Benutzeroberfläche `Gnome` !


## Wenn das Dock (Taskleiste in Linux) unter Gnome auf dem Desktop ausgeblendet wird und erst im App-Menu gezeigt wird.



- Fix:
1) In der App-Übersicht nach `Exensions` suchen.

2) Überprüfen, dass `Extensions` aktiviert ist und unter `Dash to Dock` (sollte aktiviert sein) auf die Option `Settings` klicken.

3) Dann die Einstellungen überprüfen und die gewünschten Änderungen vornehmen.


----------------------------------------------------------------------------------------------------------------


# 3. Google DNS


Google DNS auf Linux verwenden, um Anfragen im Web schneller aufzulösen zu können
```
8.8.8.8, 8.8.4.4
```

- muss für jede WLAN / LAN Verbindung manuell eingerichtet werden

- bei dem `Zahnard` neben dem Verbundenen WLAN klicken und dann zu `IPv4` wechseln

- dann bei DNS `Automatic` ausschalten und die `DNS IP von Google` eingeben



### WICHTIG:
- Der Google DNS Server löst die IP Adressen schneller auf, das heißt im Browser werden Webseiten schneller aufgerufen.
- Dies beschleunigt natürlich nicht die allgemeine Internetgeschwindigkeit, sondern kann ausschließlich das Browser Erlebnis beim Aufrufen von Webseiten verbessern.


----------------------------------------------------------------------------------------------------------------


# 4. Autostart bei Kali Linux unter Gnome


### Apps zum Autostart hinzufügen/ entfernen


- Dafür die App `Tweaks` unter Gnome aus der App-Übersicht öffnen.
- Danach zum Reiter `Startup Applications` wechseln.
- Hier können die Programme nun entfernt werden.
- Mit dem `+` Symbol können weitere Programme hinzugefügt werden.


----------------------------------------------------------------------------------------------------------------


# 5. App Icons `.desktop` in Kali Linux hinzufügen



### A. `.desktop` Dateien finden

- Dateimanager starten
- auf `other locations` klicken
- on this pc: `kali GNU/Linux`
- in den Ordner:
	 - usr
	 - share
	 - applications

- Nun können die `.desktop` Dateien kopiert und auf den Desktop eingefügt werden.
- Auf die ausgegraute Verknüpfung einen Rechtsklick machen und auf `allow launching` klicken.



### B. Weitere Programme, die wo anders aufgeführt sind

- Dateimanager starten
- auf die 3 Punkte oben rechts klicken
- in dem Menü das Häkchen bei `show Hidden Files` setzen

- auf `Home` klicken
- in den Ordner:
	 - .local
	 - share
	 - applications

- Nun können die `.desktop` Dateien kopiert und auf den Desktop eingefügt werden.
- Auf die ausgegraute Verknüpfung einen Rechtsklick machen und auf `allow launching` klicken.



----------------------------------------------------------------------------------------------------------------



# 6. Nützliche Shortcuts unter Linux:



Im Terminal Text vergrößern/ verkleinern:
```
ctrl + / ctrl +
```

neues Terminal:
```
strg + shift+ n
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
- win + Pfeil nach oben
```


Im Dateimanager versteckte Dateien anzeigen/ ausblenden:
```
- strg + h
```


Fenster schließen:
```
- alt + F4
```


PC sperren:
```
win + l
```


Dateien umbenennen:
```
F2
```


----------------------------------------------------------------------------------------------------------------
