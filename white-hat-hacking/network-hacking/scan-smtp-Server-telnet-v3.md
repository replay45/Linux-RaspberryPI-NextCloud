# Network Hacking

# Scan auf smtp (E-Mail) Server


Um z.B. User oder E-Mail Adressen "scans" auf einem Server via Telnet auszuführen.
Aus Sicherheitsgründen sollten smtp Server keine Telnet Verbindungen erlauben, allerdings ist dies nicht immer der Fall.


Sinn des Scans ist, um zu überprüfen, ob Angreifer einen Scan via Telnet auf einem smtp Servern ausführen könnte, um E-Mail-Adressen und User "Listen" zu erhalten. 
Wenn also keine Telnet-Verbindung hergestellt werden kann, wurde erfolgreich festgestellt, dass keine Scans durchgeführt werden können.
Sollte ein Scan/ Verbindungsaufbau erfolgen, bedeutet dies, dass potentielle Angreifer unerwünschte Scans durchführen können.


- Um den Scan Vorgang zu testen, kann z.B. die IP 127.0.0.1 verwendet werden.


- `Hier verwendetes Betriebssystem: Kali Linux`


### WICHTIG:
Das Ausführen eines Scans auf einem smtp Server, ohne Erlaubnis, ist verboten !!!
Ein möglicher Einsatzzweck dieses Tests ist, z.B. eigene smtp-Server zu prüfen.


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


1.
- eine Adresse pingen (ausgegebene IP Adresse kopieren nicht vergessen):
	```
	$ ping smtp.BEISPIEL-ADRESSE.com
	```


- Verbindung aufbauen:
	- (im Bestfall wird das nicht funktionieren -> wenn es nicht funktioniert, können Verbindungen via Telnet nicht hergestellt werden, was das gewünschte Ergebnis wäre)
	```
	$ telnet 127.0.0.1
	```


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


2.

Sollte eine Verbindung aufgebaut werden können, kann dieses Tool verwendet werden:
	- Auf Kali ist das Tool `smtp-user-enum` vorinstalliert, auf andernen Systemen, muss dieses ggf. nachinstalliert werden.
```
$ smtp-user-enum
```


- eine User.txt Datei anlegen, um Namen und E-Mails zu suchen
- In die Datei z.B. folgendes einfügen: 
```
user
admin
sys
noidea
...
```
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


3. 

- Befehl, um Telnet Scan auszuführen, IP muss natürlich angepasst werden:
	```
	$ smtp-user-enum -M VRFY -U users.txt -t 127.0.0.1
	```

- Ausgabe, wenn keine Ergebnisse gefunden wurden:
`"0 results"`




Erwünschtes Ergebnis des Testes ist, dass keine Ausgabe und keine Verbindung via Telnet möglich ist.
Somit kann festgestellt werden, ob durch das Telnet-Protokoll potentiell ein Sicherheitsrisiko ausgeht.


-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
