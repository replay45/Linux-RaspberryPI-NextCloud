# [SMB](https://de.wikipedia.org/wiki/Server_Message_Block)-Netzlaufwerke über [Nginx](https://nginx.org/) in [Wiki.js](https://js.wiki/) verlinken


`Anleitung verfasst am 15.3.2025`


-------------------------------------------------------------------------------------------------------------


- Ziel ist es, ein Netzlaufwerk über den Browser mit dem HTTP/HTTPS-Protokoll verfügbar zu machen, damit Nutzer verlinkte Dateien und Dokumente über den Webbrowser vom Netzlaufwerk herunterladen/öffnen können.
- Die Einrichtung von [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) im lokalen Netzwerk wird in der Anleitung [HTTPS-Wikijs-über-IP](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/wikijs) gezeigt.
- Diese Anleitung bezieht sich ausschließlich auf `SMB-Netzlaufwerke`.


-------------------------------------------------------------------------------------------------------------


## 1. Überprüfen, ob das Netzlaufwerk das [SMB](https://de.wikipedia.org/wiki/Server_Message_Block)-Protokoll verwendet
- Da für unterschiedliche Protokolle eine andere Vorgehensweise nötig ist, muss zunächst geprüft werden, ob das Netzlaufwerk das SMB-Protokoll nutzt.


1.1 Um in Windows die aktuell eingehängten Netzlaufwerke anzuzeigen
- [CMD](https://de.wikipedia.org/wiki/Cmd.exe) öffnen
- Befehl eingeben: 
```
$ net use
```

1.2. Installation benötigter Tools unter Linux (für den Server, auf dem der Webserver läuft)
- `smbclient` & `cifs-utils` installieren, um Netzlaufwerk mounten zu können.
```
$ sudo apt install cifs-utils
```
```
$ sudo apt install smbclient
```


1.3. Erreichbarkeit und Protokoll unter Linux prüfen
- Unter Linux (z.B. auf dem Wiki.js-Server) Erreichbarkeit und Protokoll des Netzlaufwerkes prüfen.
	- Bei einem Domänenbenutzer ist ggf. `DOMAIN/Benutzername` nötig.
	- Unter Linux gilt bei Domänenbenutzern `DOMAIN\\Benutzername`.
- Verbindung herstellen:
```
$ smbclient //IP-ODER-HOSTNAME/FREIGABE -U BENUTZERNAME
```
Beispiel:
```
$ smbclient //192.168.1.10/Freigabe -U Administrator
```

- Falls das fehlschlägt, dann mit Anführungszeichen versuchen
```
$ smbclient -L //IP-ODER-HOSTNAME -U "DOMAIN\\BENUTZERNAME"
```
Beispiel:
```
$ smbclient //192.168.1.10/Freigabe -U "DOMAIN\\Administrator"
```

- Nach erfolgreichem Einhängen des Netzlaufwerkes sollte der Prompt `smb: \>`
- Mit dem Befehl `$ ls` lässt sich der Inhalt auflisten.
- Hier sollten nun die Freigaben aufgelistet werden.
- Um smbclient-Sitzung zu verlassen, `$ exit` nutzen.


1.4. Mögliche Fehlermeldungen
- mögliche Fehlermeldungen, die auftreten können:
	- `NT_STATUS_BAD_NETWORK_NAME`: Freigabename falsch oder nicht erreichbar
	- `NT_STATUS_LOGON_FAILURE`: Authentifizierung schlägt beim SMB-Server fehl (z.B. falsche Anmeldedaten)
- Hinweis: Sonderzeichen in Passwörtern können zu Schwierigkeiten und Fehlern führen.


-------------------------------------------------------------------------------------------------------------


## 2. Netzlaufwerk (dauerhaft) mounten, um Weiterleitung zu ermöglichen
- Das Netzlaufwerk muss dauerhaft auf dem Linux-Server gemountet werden, damit die Weiterleitung durch den Webserver möglich ist.
- Mit dem Hinzufügen des Mounts sollte dieser auch im Autostart sein.


2.1 Erstellen des lokalen Mount-Pfads
- `Als Platzhalter` für den `lokalen Mount-Pfad` wird im Verlauf `lokaler_mount_pfad` genutzt.
- lokalen Mount-Pfad erstellen:
```
$ $ cd /mnt
```
```
$ sudo mkdir lokaler_mount_pfad
```
```
$ ls
```


2.2. Mount-Eintrag in Konfigurations-Datei hinzufügen
- Mount-Eintrag in `/etc/fstab` hinzufügen:
```
$ sudo nano /etc/fstab
```

- Am Ende die Zeile hinzufügen (Platzhalter entsprechend anpassen):
```
//Server/Shares /mnt/lokaler_mount_pfad cifs credentials=/etc/cifs-creds,vers=3.0,uid=www-data,gid=www-data,iocharset=utf8,file_mode=0644,dir_mode=0755 0 0
```
- Speichern mit `STRG+X`
- Optionen erklärt:
	- `vers=3.0` ist die SMB-Version (falls diese nicht funktioniert/ eine ältere Version genutzt wird, mit `vers=2.0` versuchen)
	- `iocharset=utf8` Umlaute und Sonderzeichen korrekt anzeigen
	- `file_mode=0644,dir_mode=0755` Webserver hat für Dateien und für Verzeichnisse nur leserechte
	- `uid=www-data,gid=www-data,` setzt Berechtigungen für den Webserver
	- `lokaler_mount_pfad` lokales Verzeichnis, in das das Netzlaufwerk gemountet wird



2.3 `credentials`-Datei
	- Um Benutzernamen und Passwort nicht direkt in der /etc/fstab ablegen zu müssen und so die Sicherheit zu erhöhen, werden die Zugangsdaten in eine separate Datei ausgelagert.

- Datei erstellen
```
$ sudo nano /etc/cifs-creds
```
Einfügen:
```
domain=DOMAIN
username=USERNAME
password=PASSWORD
```
- Hinweis:
	- `domain=DOMAIN` mur bei Verwendung eines Domänen Benutzers nutzen, ansonsten die Zeile entfernen.
	- `USERNAME` mit Benutzernamen des Benutzers ersetzen
	- `PASSWORD` mit Passwort des Benutzers ersetzen (Sonderzeichen, können zu Authentifizierungsproblemen führen)

- Berechtigungen der `credentials`-Datei anpassen
```
$ sudo chmod 600 /etc/cifs-creds
```


2.4. Testen des Mounts
- Mounts neu laden
```
$ systemctl daemon-reload
```
```
$ sudo mount -a
```

- aktuelle Mounts überprüfen
```
$ mount | grep cifs
```
```
$ df -h
```
```
$ df -h | grep /mnt/lokaler_mount_pfad
```


2.5 Autostart überprüfen
```
$ sudo reboot
```
```
$ df -h
```
```
$ mount | grep cifs
```

- Falls der Mount nach dem Neustart fehlt, manuell erneut laden
```
$ sudo mount -a
```

- Manuelle Tests
	- überprüfen, ob das Laufwerk korrekt verbunden ist
```
$ ls /mnt/lokaler_mount_pfad
```

- Netzlaufwerk bei Fehlern unmounten und dann erneut mounten
```
sudo umount /mnt/lokaler_mount_pfad
sudo mount -a
```


-------------------------------------------------------------------------------------------------------------


## 3. Gemountetes Netzlaufwerk in [Nginx](https://nginx.org/) einbinden
- Nginx konfigurieren, dass das Netzlaufwerk über den Browser bereitgestellt wird
- Nginx-Konfiguration des Wiki.js anpassen:
```
$ sudo nano /etc/nginx/sites-available/wiki
```
- innerhalb des `ersten server {}-Blocks` folgendes hinzufügen
```
location /lokaler_mount_pfad/ {
	alias /mnt/lokaler_mount_pfad/;
	autoindex on;  # Zeigt eine Dateiliste im Browser an
	autoindex_exact_size off;
	autoindex_localtime on;
}
```

- Konfiguration testen und Nginx neu starten
```
$ sudo nginx -t
```
```
$ sudo systemctl restart nginx
```


-------------------------------------------------------------------------------------------------------------


## 4. Berechtigungen im [Wiki.js](https://js.wiki/)
- Damit man das Netzlaufwerk über den Server im Browser aufrufen kann, muss man `im Wiki.js` die `entsprechenden Berechtigungen` setzen.
- Dafür im Wiki.js als Administrator anmelden und zu dem Reiter `Gruppen` wechseln.
- Nun die entsprechende Gruppe auswählen und unter `Rules` eine neue Regel hinzufügen.
- Dort nun den `Beginn des Pfades` der durch den `Platzhalter (lokaler_mount_pfad) ersetzt wurde`. Der Pfad muss natürlich mit dem aus der Nginx-Konfiguration übereinstimmen.
- `Änderungen speichern`


-------------------------------------------------------------------------------------------------------------


## 5. Testen
- Webbrowser öffnen
- Browser öffnen und `https://IP-ADRESSE/lokaler_mount_pfad/` oder `https://INTERNE-DOMAIN/lokaler_mount_pfad/` eingeben.
	- Nun überprüfen, ob die Inhalte des gemounteten Netzlaufwerks angezeigt werden.


-------------------------------------------------------------------------------------------------------------


## 6. Verbindung schlägt fehl
- Wenn die Verbindung zum Netzlaufwerk oder Wiki.js nicht funktioniert, hier ein paar Anhaltspunkte.

6.1 Erreichbarkeit des Servers prüfen
```
$ ping IP-Adresse
```


6.2 Erreichbarkeit des Wiki.js prüfen
- Wenn das Wiki.js nicht erreichbar ist, aber der Server gepingt werden kann, dann Server neu starten oder Docker-Container des Wiki.js neu starten.
```
$ reboot
```
```
$ cd Wikijs
$ docker-compose down
$ docker-compose up -d
```
- Status des Containers prüfen
```
$ docker-compose ps
```


6.2 Falls das Wiki.js erreichbar ist, Berechtigungen im Wiki.js überprüfen
- Prüfen, ob die Berechtigungen für die Gruppe gesetzt sind.
- Für Gäste, ohne Benutzeraccount die Gruppe Gast bearbeiten.


6.3 Webbrowser
- Webbrowser schließen und Cache löschen / privaten-Modus nutzen
- Einen anderen Webbrowser verwenden, um browserbezogene Probleme auszuschließen.


6.4 Prüfen, ob das Netzlaufwerk korrekt gemountet ist
- aktuelle Mounts überprüfen
```
$ mount | grep cifs
```
```
$ df -h
```

- Manuelle Tests
	- überprüfen, ob das Laufwerk korrekt verbunden ist
```
$ ls /mnt/lokaler_mount_pfad
```


6.5 Nginx Webserver überprüfen
- Konfiguration prüfen lassen und Nginx neu starten
```
$ sudo nginx -t
```
```
$ sudo systemctl restart nginx
```

- den Nginx-Error-Log überprüfen:
```
$ sudo tail -f /var/log/nginx/error.log
```


-------------------------------------------------------------------------------------------------------------
