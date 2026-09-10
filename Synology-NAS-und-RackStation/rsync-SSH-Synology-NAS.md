# [rsync](https://de.wikipedia.org/wiki/Rsync) Backups (verschlüsselt via [SSH](https://de.wikipedia.org/wiki/Secure_Shell)) von Linux-Server auf [Synology NAS/RackStation](https://www.synology.com/de-de)
- Grundsätzlich ist das Vorgehen relativ ähnlich zu einem normalen [Ubuntu](https://ubuntu.com/download)/[Debian](https://www.debian.org/index.de.html) Server, allerdings gibt es ein paar Punkte zu beachten.

`Anleitung verfasst am 10.9.2026`

`Synology DSM Version: 7.3.1`


-------------------------------------------------------------------------------------------------------------


### rsync-Benutzer erstellen
- Damit die Backups über rsync unabhängig vom Admin-Benutzer laufen, einen neuen rsync-Benutzer erstellen.
- Dafür in der Systemsteuerung unter `Benutzer und Gruppe` einen neuen Benutzer anlegen.
- Diesem dann auch direkt in den Benutzereigenschaften unter `Anwendungen` die Berechtigungen für den Dienst `rsync` zuweisen.


### "Freigegebener Ornder" erstellen
- Zunächst als Admin auf der DSM einloggen.
- Nun die Systemsteuerung öffnen und einen neuen `freigegebenen Ordner` erstellen.
- Unter `Berechtigungen` dem rsync-Benutzer Lese-/Schreibrechte zuweisen.


### [Firewall](https://de.wikipedia.org/wiki/Firewall) SSH-Port & [SSH](https://de.wikipedia.org/wiki/Secure_Shell)/[rsync](https://de.wikipedia.org/wiki/Rsync) aktivieren
- SSH aktivieren & "Automatische Blockierung" konfigurieren
    - Nun unter `Terminal & SNMP` den `SSH-Dienst` aktivieren.
    - Um Brute-Force Angriffen vorzubeugen unter `Sicherheit > Schutz` die `Automatische Blockierung` aktivieren und die maximalen Login-Versuche in X-Minuten festlegen, bevor die Quell-IP gesperrt wird. Zusätzlich die `Blockierung aufheben nach X-Tagen` konfigurieren.

- Firewall
    - Der Port für rsync über SSH (default 22) muss ggf. in der Firewall freigegeben werden, falls ein benutzerdefiniertes Profil verwendet wird.
    - Die Firewall sollte selbstverständlich `aktiviert` sein, daher unter `Sicherheit > Firewall` sollte die Option `Firewall aktivieren` aktiv sein.
    - Firewall-Profil sollte standardmäßig auf `default` stehen, außer es wird ein benutzerdefiniertes Profil eingestellt (Hier muss dann lediglich der SSH-Port namens `verschlüsselter Terminal-Dienst (einschließlich rsync) Port 22` freigegeben werden).

- rsync aktivieren
    - Nun unter `Dateidienste > rsync` den `rsync-Dienst` aktivieren (gleicher Port wie bei SSH)
    - WICHTIG: Die Option des rsync-Kontos wird `NICHT benötigt` ! Diese ist nur für `unverschlüsseltes rsync` (ohne SSH).


-------------------------------------------------------------------------------------------------------------


# [SSH-Schlüsselpaar](https://de.wikipedia.org/wiki/Public-Key-Authentifizierung)

### SSH-Schlüsselpaar auf dem Quellserver erzeugen
- Jetzt muss ein SSH-Schlüsselpaar auf dem Quellserver erzeugt werden.
	- KOMMENTAR: hier sollte notiert werden, wofür das Schlüsselpaar ist, z.B. XY-rsync-Backup-SSH-Key.
```
$ cd .ssh
$ ssh-keygen -t rsa -b 4096 -C "KOMMENTAR"
```
- Um Standardeinstellungen und Standardpfad zu nutzen, `ENTER` drücken.
- Die Passphrase sollte für rsync unbedingt mit `ENTER` übersprungen werden, damit die Automatisierung über Cron funktioniert.
- Die Dateinamen des öffentlichen und privaten Schlüssels sollten bis auf die Endung `.pub`, gleich sein.


### Public-Key von Linux-Server auf Synology NAS/RackStation kopieren
- Wenn der rsync-Backup-Benutzer keine Admin-Rechte auf der Synology hat, ist das Übertragen des Public Keys etwas umständlicher, daher hier der einfachste Trick:
    - Temporär den rsync-Backup-Benutzer auf der Synology zur Admin-Gruppe hinzufügen.
    - Sobald der Transfer des Public-Keys abgeschlossen ist, sollte der rsync-Backup-Benutzer aus der Admin-Gruppe wirder entfernt werden.

- Grund dafür ist, dass auf Synology Systemen nicht-Admins keine Shell zugewiesen haben. Für rsync selber ist diese auch nicht notwendig, allerdings kann das bei dem Versuch vom Übertragen des Public-Keys zu Umständlichkeiten führen, daher der einfache Workaround mit dem temporären Hinzufügen zu Admin-Gruppe.

- Nun auf dem Linux-Server (Quellserver) folgenden Befehl ausführen, um Public-Key zu übertragen:
- Befehl für Standardeinstellungen (standard Dateiname):
    - USER: Benutzernamen des rsync-Backup-Benutzers
    - SYNOLOGY-ZIELSERVER_IP: IP-Adresse der Synology NAS/RackStation
```
$ ssh-copy-id USERNAME@SYNOLOGY-ZIELSERVER_IP
```
- wenn ein anderer Dateiname für den public key oder ein anderer Pfad genutzt wird:
```
$ ssh-copy-id -i ~/.ssh/DATEINAME-PUBLIC-KEY.pub USERNAME@SYNOLOGY-ZIELSERVER_IP
```

### SSH-Zugang vom Quellserver zum Zielserver testen
- An dieser Stelle sollte der Verbindungsaufbau ohne Passwortabfrage funktionieren.
- Wenn der Name des Schlüsselpaares nicht angepasst wurde (Standard):
    - USER: Benutzernamen des rsync-Backup-Benutzers
    - SYNOLOGY-ZIELSERVER_IP: IP-Adresse der Synology NAS/RackStation
```
$ ssh USERNAME@ZIELSERVER_IP
```

- Dateiname des privaten Schlüssels wurde angepasst:
```
$ ssh -i ~/.ssh/PRIVAT-KEY USER@SYNOLOGY-ZIELSERVER_IP
```

- `-v` logging zur Fehlerbehebung:
```
$ ssh -v USERNAME@ZIELSERVER_IP
```

- Die SSH Schlüssel sollten nun in der Datei `~/.ssh/authorized_keys` im Home-Verzeichnis des rsync-Backup-Benutzers auf dem Synology Zielserver sein.



### rsync-Backup-Benutzer wieder aus Admin-Gruppe entfernen
- Nun nicht vergessen den rsync-Backup-Benutzer wieder aus der Admin-Gruppe auf dser Synology zu entfernen.
- Da nach dem entfernen der Admin-Berechtigungen der rsync-Backup-Benutzer auch keine Shell mehr zugewiesen hat, wird nun keine SSH-Sitzung mehr über eine Shell funktionieren, rsync ist davon alleridngs unabhängig.


-------------------------------------------------------------------------------------------------------------


# rsync-Backups
- Jetzt können mit Skripten und [Cronjobs](https://de.wikipedia.org/wiki/Cron) Backups automatisiert von einem Linux-Quell-Server auf ein Synology-System via verschlüsseltem rsync (rsync über SSH) übertragen werden.


-------------------------------------------------------------------------------------------------------------

