# Verschlüsselung


# 1. verschlüsseln von Ordnern auf Linux mit CryFS


CryFS ist ein Open Source verschlüsselungsprogramm, womit man z.B. lokale Ordner, Verzeichnisse auf dem PC,
oder auch Ordner in der Cloud sicher verschlüsseln kann.

Es ist auch möglich, externe Speichermedien über CryFS zu verschlüsseln,
allerdings eignet sich dafür das Tool "VeraCrypt", das extra für die Verschlüsselung von externen Speichermedien entwickelt wurde 
und auch von allen gängigen Betriebssystemen unterstützt wird.



> [cryfs.org](https://www.cryfs.org)



### A. Installation auf Linux
	$ sudo apt install cryfs
	


-------------------------------------------------------------------------------------------------------------------------------------


### B. Einen Ordner verschlüsseln

- 2 Ordner in einem gewünschten Pfad erstellen
- z.B. unter Dokumente den Ordner "entschlüsselt" und "verschlüsselt" erstellen
	

- Terminal öffnen und Befehl eingeben:
	```
	$ cryfs ~/HIER/PFAD/VERSCHLÜSSELTER/ORDNER ~/HIER/PFAD/ENTSCHLÜSSELTER/ORDNER
	```

- WICHTIG:
	- Die neu erstellten Ordner "entschlüsselt" und "verschlüsselt" müssen leer sein !



- Dann erscheint eine Frage, ob die Standardeinstellungen genutzt werden sollen.
- Hier kann einfach mit `"y"` bestätigt werden.

- Nun muss ein starkes Passwort gewählt werden.

Um das System auszuhängen, erscheint ein unmount Befehl im Terminal.
Optional kann aber auch die Funktion des Dateimanagers genutzt werden, um das System auszuhängen.


- __Hinweis zum Speicherort der Daten__:
	- Die Daten liegen ausschließlich in dem verschlüsselten Ordner !
	- Beim Entschlüsseln, werden diese zwar im Ordner "entschlüsselt" als Laufwerk angezeigt,
	jedoch sind die Daten niemals wirklich in dem unverschlüsselten Teil gespeichert !


- Um die Daten wieder zu entschlüsseln und das erstellte "Laufwerk" wieder einzuhängen, den Befehl anpassen und ausführen:
	```
	$ cryfs ~/HIER/PFAD/VERSCHLÜSSELTER/ORDNER ~/HIER/PFAD/ENTSCHLÜSSELTER/ORDNER
	```


-------------------------------------------------------------------------------------------------------------------------------------


### C. Optional ein Skript erstellen

Nun kann optional ein `Sh-Skript` erstellt werden, um nicht jedesmal den Befehl ausführen zu müssen,
sondern nur das Skript im Terminal zu starten, was den Befehl automatisch ausführt.

Dieses Skript muss lediglich den Entschlüsselungsbefehl enthalten und als `Datei.sh` gespeichert werden.


-------------------------------------------------------------------------------------------------------------------------------------


### D. Skript über eine `.Desktop` verknüpfen und einfach ausführbar machen

Das Skript kann mit einer .Desktop Datei verknüpfen:
Dabei würde diese im App Menü erscheinen und beim Öffnen der ".Desktop-Datei" würde sich automatisch das Skript ausführen
und ein Terminalfenster mit der Passwortabfrage öffnen.

- `Wie man ein Skript mit einer ".Desktop-Datei" verknüpft, wird ebenfalls in diesem Github-Repository gezeigt.`


-------------------------------------------------------------------------------------------------------------------------------------


# 2. VeraCrypt


VeraCrypt ist ein kostenloses Open Source Programm zur Datenverschlüsselung und wird zur Verschlüsselung von Festplatten und Wechseldatenträgern, wie USB-Sticks genutzt.


Das Programm ist u.a. für Linux, Windows und MacOS verfügbar.



> [veracrypt.fr](https://veracrypt.fr/)


-------------------------------------------------------------------------------------------------------------------------------------


- Installation:

	```
	$ sudo apt install software-properties-common
	```
	```
	$ sudo add-apt-repository ppa:unit193/encryption
	```
	```
	$ sudo apt update
	```

	- [download tar.bz2 auf veracrypt.fr](https://veracrypt.fr/)

	```
	$ tar xvf veracrypt-1.26.7-setup.tar.bz2
	```
	```
	$ ./veracrypt-1.26.7-setup-gui-x64
	```
	- Darauf achten, die Version mtit `"gtk"` zu installieren !
	- Installationsassistenten folgen

- deinstallations-Befehl:

	```
	$ ./veracrypt-1.26.7-setup-gui-x64
	```

-------------------------------------------------------------------------------------------------------------------------------------
