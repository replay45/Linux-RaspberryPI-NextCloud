# Tor auf Kali Linux installieren


### 1. Tor auf der [offiziellen Webseite herunterladen](https://www.torproject.org/)

- Dabei ist besonders darauf zu achten, dass es die offizielle Webseite ist !


Der Tor-Browser bietet Zugang zum Darknet von allen möglichen Geräten auf dem er installiert ist,
jedoch empfiehlt es sich aus Datenschutzgründen den Tor-Browser über das Linux Tails-Betriebssystem zu nutzen,
um das Darknet zu besuchen.


Auf üblichen Desktop-Systemen eignet sich der Tor-Browser trotzdem, um sich sicher im clear-Web zu bewegen.



### 2. entpacken
- Die runtergeladene `tor-browser-tar.zx` in den gewünschten Pfad/Ordner entpacken.


-------------------------------------------------------------------------------------------------------------------------------


### 3. Befehlszeilen-Methode:


Innerhalb des Tor-Browser-Verzeichnisses das Terminal öffnen und Tor mit dem Befehl starten:

```
‪$ ./start-tor-browser.desktop
```


Wenn dieser Befehl nicht ausgeführt werden kann, muss die Datei ausführbar gemacht werden:
```
$ chmod +x start-tor-browser.desktop 
```


Um Tor Browser als Desktop-Anwendung zu registrieren:
```
$ ./start-tor-browser.desktop --register-app
```

-------------------------------------------------------------------------------------------------------------------------------


### 4. Grafische Methode:


- Mit Rechtsklick `Eigenschaften` von der `start-tor-browser.desktop` Datei öffnen.
- Dann Berechtigung unter dem Punkt `Execute` `Allow executing file as  program` geben, um die Datei als Programm zu öffnen.


- Sollte sich der Text-Editor öffnen, müssen noch ein paar Einstellungen angepasst werden:


- Bei `Eigenschaften` auf `Behavior` klicken und `Run them` oder `Ask what to do` unter  `Executable Text Files`.
- Danach die `start-tor-browser.desktop` Datei ausführen.



- Vor der Nutzung des Tor-Browsers, sollten in den `Einstellungen` noch einige Anpassungen vorgenommen werden,
- Wie z.B. die `Browser-Sicherheit` von Standard auf `Streng` usw.
- Diese sollten immer individuell vorgenommen werden.



-------------------------------------------------------------------------------------------------------------------------------


> [torproject.org/installation](https://tb-manual.torproject.org/de/installation/)


-------------------------------------------------------------------------------------------------------------------------------
