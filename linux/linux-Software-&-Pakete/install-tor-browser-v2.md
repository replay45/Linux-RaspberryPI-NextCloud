# Tor-Browser auf Linux installieren (Debian-basierte Distributionen)

`Anleitung zuletzt bearbeitet am 27.5.2024`


## Disclaimer
Der Tor-Browser bietet Zugang zum Darknet, jedoch empfiehlt es sich aus Sicherheitsgründen und zum Schutz der Identität, den Tor-Browser für Besuche im Darknet nur auf geeigneten Systemen zu nutzen. Das kann z.B. die [Linux Tails-Distribution](https://tails.net/) sein.
Außerdem sollte man sich vor den Besuchen im Darknet umfassend mit dem Thema auseinandersetzen und sich bestmöglich informieren.


### 1. den Tor-Browser auf der [offiziellen Webseite](https://www.torproject.org/) herunterladen

- Dabei ist besonders wichtig, darauf zu achten, dass es die offizielle Webseite ist !


### 2. entpacken

- Die runtergeladene `tor-browser-tar.zx` in den gewünschten Pfad/Ordner entpacken.


-----------------------------------------------------------------------------------------------------------------


### 3. befehlszeilen-Methode

Innerhalb des Tor-Browser-Verzeichnisses das Terminal öffnen und Tor mit dem Befehl starten:
```
‪$ ./start-tor-browser.desktop
```


Wenn dieser Befehl nicht ausgeführt werden kann, muss die Datei ausführbar gemacht werden:
```
$ chmod +x start-tor-browser.desktop 
```


Um den Tor Browser als Desktop-Anwendung zu registrieren:
```
$ ./start-tor-browser.desktop --register-app
```


-----------------------------------------------------------------------------------------------------------------


### 4. grafische-Methode

- Mit einem Rechtsklick die`Eigenschaften` von der `start-tor-browser.desktop` Datei öffnen.
- Dann Berechtigung unter dem Punkt `Execute` `Allow executing file as  program` geben, um die Datei als Programm zu öffnen.
- Sollte sich der Texteditor öffnen, müssen noch ein paar Einstellungen angepasst werden.
- Bei `Eigenschaften` auf `Behavior` klicken und `Run them` oder `Ask what to do` unter  `Executable Text Files`.
- Danach die `start-tor-browser.desktop`-Datei ausführen.


-----------------------------------------------------------------------------------------------------------------


### 5. Hinweise vor der Verwendung des Tor-Browsers

- Vor der Nutzung des Tor-Browsers, sollten in den `Einstellungen` noch einige Anpassungen vorgenommen werden:
    - z.B. sollte die `Browser-Sicherheit` von Standard auf `Strict` gesetzt werden.
    - Außerdem empfiehlt es sich unbedingt, den Tor-Browser auf Englisch zu nutzen.
    - Die Einstellungen sollten immer vor der Verwendung individuell vorgenommen werden.


-----------------------------------------------------------------------------------------------------------------


- Mehr zu Sicherheit auf Linux unter [Linux/Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)
- Mehr zu Sicherheit im Internet und zu Sicherheit im Browser unter [cybersecurity/anonym-&-sicher-im-internet-surfen](https://github.com/replay45/ethical-hacking-und-cybersecurity/tree/main/browser-%26-sicher-surfen)



-----------------------------------------------------------------------------------------------------------------


> [torproject.org/installation](https://tb-manual.torproject.org/de/installation/)
