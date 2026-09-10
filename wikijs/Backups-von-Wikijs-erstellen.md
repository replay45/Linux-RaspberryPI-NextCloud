# Backups von Wikijs erstellen

`Anleitung erstellt am 7.3.2025, zuletzt bearbeitet am 9.9.2026`


## Inhaltsverzeichnis
1. Einsatzzweck "Snapshots"
2. Backups der Inhalte auf einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)-Server
3. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte) - [Docker](https://de.wikipedia.org/wiki/Docker_(Software)
4. Erstellte Datenbank-Backups auf einen anderen Server übertragen ([SCP](https://de.wikipedia.org/wiki/Secure_Copy) & [rsync](https://de.wikipedia.org/wiki/Rsync))
5. PostgreSQL-Datenbank-Backups wiederherstellen
6. Datenbank-Backups automatisch löschen nach entsprechender Zeit


-------------------------------------------------------------------------------------------------------------


# 1. Einsatzzweck "Snapshots"
- Snapshots erstellt man in der Regel von virtuellen Maschinen auf Host-Systemen (meist mit Server-Betriebssystemen).
- Dabei speichert man einen Zustand einer virtuellen Maschine, um z.B. Änderungen an dem System vorzunehmen und nach erfolgreichem Implementieren, werden diese wieder gelöscht.
- Sollten Probleme bei den Änderungen auftreten, kann durch den Snapshot der vorherige Zustand schnell wiederhergestellt werden.
- Dabei eignen sich Snapshots nur als `temporäre Sicherung vor der Implementierung von Änderungen`.
- Das dauerhafte Speichern von Snapshots kann sehr Ressourcenintensiv sein und zu Problemen führen, daher empfiehlt sich die Nutzung von Snapshots immer nur temporär.
- Snapshots ersetzen keine vollständige Backupstrategie und Datenbank-Backups !


-------------------------------------------------------------------------------------------------------------


# 2. Backups der Inhalte auf einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)-Server
- Wichtig: 
    - Diese Methode erstellt Backups `nur von Inhalten`, wie Seiten im HTML format und Bildern. Es werden `keine` Konfigurationen oder Datenbanken gesichert. Außerdem gibt es auch keine Wiederherstellungsfunktion für diese Methode !

- Voraussetzungen
	- Dafür wird ein SFTP-Server benötigt.
	- Dieser sollte auf einem anderen Server laufen, falls der Server mit Wikijs einen Defekt aufweisen sollte.
	- Was ein SFTP-Server ist und wie dieser installiert werden kann, wird unter [SFTP-Server](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/linux-Software-%26-Pakete) erklärt.

- Wann ist das Einsetzen der Funktion sinnvoll ?
	- Diese Option eignet sich, um die Inhalte zusätzlich auf einem anderen Server zu sichern.
	- Diese Möglichkeit reicht in der Regel nicht alleine aus und muss ggf. mit einer anderen Backup-Option kombiniert werden.

- Anleitung
	- Admin-Dashboard des Wikijs öffnen und zum Reiter `Speicher` -> `SFTP`
	- Als `Authentication Methode` entweder `Passwort` oder `private Key` auswählen (je nachdem was genutzt werden soll).
	- Unter `Host` die `IP-Adresse` des SFTP-Servers eingeben und entweder `Nutzername und Passwort` einfügen oder die `schlüsselbasierte Authentication` nutzen.
	- Den Speicherpfad festlegen: Aus Sicht des SFTP-Benutzers ist der Pfad nach dem Beispiel der verlinkten Anleitung `/sftp/Files` oder nur `/Files`.
	- Zum Zeitpunkt der Erstellung dieser Anleitung ist `nur die Option` `Zum Ziel hochladen` verfügbar.
	- Um den ersten Export anzustoßen oder manuell eine Sicherung zu erstellen, unter `Export All` `Ausführen` wählen.
	- Nun muss noch der Regler oben rechts auf `Active` gesetzt werden.
	- Zuletzt auf `Anwenden` klicken, um zu speichern.
	- Möglicherweise ist die Statusanzeige für die "letzte Synchronisation" nicht ganz korrekt, ein Neustart des Wikis bzw. des Servers sollte das beheben.

### Bei Fehlern die Logs überprüfen:
```
$ cd wiki
$ sudo docker compose ps
$ sudo tail -f /var/log/auth.log
$ sudo docker compose logs -f
```


-------------------------------------------------------------------------------------------------------------


# 3. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte) - [Docker](https://de.wikipedia.org/wiki/Docker_(Software)
- Beim Backup der Datenbank sollten alle Konfigurationen, Einstellungen und Inhalte gebackupt werden.
- Dieses kann ebenfalls automatisiert werden.


## Hinweis - Backups von Konfigurationsdateien
- Je nach verwendeten Konfigurationsdateien sollte man unbedingt diese ebenfalls sichern.
- `docker-compose.yml`
    - Diese Konfigurationsdatei sollte aufgrund der enthaltenen Passwörter für die Datenbank an einem sicheren Ort, z.B. einem Passwortmanager speichern.
- `Cronjobs, Skripte etc.`
    - Außerdem sollte man sich verwendetete Cronjobs und Skripte kopieren und mit den erstellten Backups ablegen.


## Datenbanktyp prüfen
- Datenbanktyp herausfinden
```
$ nano docker-compose.yml
```
- Da sollte folgender Eintrag stehen:
`DB_TYPE: postgres`

- Die weitere Anleitung beschränkt sich auf PostgreSQL-Datenbanken.


## PostgreSQL-Datenbank manuell sichern
```
$ sudo docker exec CONTAINER_NAME pg_dump -U DB_USER DB_NAME > /PATH/TO/BACKUP/wiki-js-dump-manuell_$(date +%d-%m-%Y).sql
```

- Die folgenden Parameter (Platzhalter) sind alle in der `docker-compose.yml` enthalten:
    - `CONTAINER_NAME`: Der Name des Docker-Containers, der die PostgreSQL-Datenbank betreibt.
    - `DB_USER`: Der Benutzername der Datenbank (meistens wikijs)
    - `DB_NAME`: Der Name der Datenbank (z. B. wiki)
    - `/PATH/TO/BACKUP`: Verzeichnis, in dem das Backup gespeichert werden soll (muss manuell angelegt werden)



## Automatisieren (Cronjob)
- Benutzer in die Docker-Gruppen hinzufügen (damit Befehl korrekt ohne sudo ausgeführt werden kann)
	- Damit die Änderungen wirksam werden, Benutzer abmelden und erneut anmelden.
	- Platzhalter `USER` durch tatsächlichen Usernamen austauschen.
```
$ whoami
$ sudo usermod -aG docker USER
```

- Prüfen, ob der Benutzer in der gewünschten Gruppe ist
    - `USER` mit tatsächlichem Usernamen austauschen
```
$ groups USER
```

- Cronjob einrichten
    - Editor auswählen, am besten `nano`
```
$ crontab -e
```

- Beispiel für nächtliches Backup um 2:00 Uhr:
    - Großgeschriebenes muss individuell angepasst werden. Dabei können die Informationen zur Datenbank aus der `docker-compose.yml` und der Containername mithilfe von `$ docker ps` entnommen werden.
    - `pg_dump -U DB_USER -f /tmp/wiki-js-dump_$(date +\%d-\%m-\%Y).sql DB_NAME`: sichert die Datenbank in dem Docker-Container selber und mit `&& /usr/bin/docker cp CONTAINER_NAME:/tmp/wiki-js-dump_$(date +\%d-\%m-\%Y).sql` wird die gesicherte Datenbank aus dem Container in `//PATH/TO/BACKUP/wiki-js-dump_$(date +\%d-\%m-\%Y).sql` kopiert.
    - Hiermit: `&& /usr/bin/docker exec CONTAINER_NAME rm -f /tmp/wiki-js-dump_$(date +\%d-\%m-\%Y)` wird das Backup aus dem temporären verzeichnis entfernt.
    - `>> /PATH/TO/BACKUP/sqldatenbank-backup-cron.log 2>&1`: ist ein optionaler Teil, Cron legt eine log-Datei an, in die Fehler geschrieben werden können.
```
0 2 * * * /usr/bin/docker exec CONTAINER_NAME pg_dump -U DB_USER -f /tmp/wiki-js-dump_$(date +\%d-\%m-\%Y).sql DB_NAME && /usr/bin/docker cp CONTAINER_NAME:/tmp/wiki-js-dump_$(date +\%d-\%m-\%Y).sql /PATH/TO/BACKUP/wiki-js-dump_$(date +\%d-\%m-\%Y).sql && /usr/bin/docker exec CONTAINER_NAME rm -f /tmp/wiki-js-dump_$(date +\%d-\%m-\%Y) >> /PATH/TO/BACKUP/sqldatenbank-backup-cron.log 2>&1
```


- Als nächstes muss im Crontab die folgende Zeile eingefügt werden, da Cron nur eine minimale Shell verwendet
    - Die Zeile definiert dabei die `Shell` auf `/bin/bash`, damit `$(date +\%d-\%m-\%Y)`
```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```


- optional prüfen, ob /tmp im Container vorhanden ist und Schreibrechte existieren
	- Dafür wird in die Shell des Containers gewechselt
```
$ docker-compose ps
$ docker exec -it CONTAINER_NAME bash
$ ls -ld /tmp
$ exit
```


### Schreibrechte prüfen:
```
$ touch /tmp/testfile && ls -l /tmp/testfile && rm /tmp/testfile
```
- ungefähre erwartete Ausgabe: `-rw-r--r--  1 root  root  DATUM UHRZEIT /tmp/testfile`


### Wenn Probleme mit dem Cronjob auftreten, dann prüfen, ob folgendes enthalten ist:
- Normalerweise muss die PATH-Zeile (die folgende Zeile) nicht angepasst werden.
```
$ crontab -e
```
```
# Shell als /bin/bash setzen für Cronjobs: 
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```


### Cronjob überprüfen
- Logs:
```
$ grep CRON /var/log/syslog
```
oder
```
$ journalctl -u cron
```
- eigene Log-Datei (sqldatenbank-backup-cron.log) prüfen
```
$ tail -f /PATH/TO/BACKUP/sqldatenbank-backup-cron.log
$ tail /PATH/TO/BACKUP/sqldatenbank-backup-cron.log
```


-------------------------------------------------------------------------------------------------------------


# 4. Erstellte Datenbank-Backups auf einen anderen Server übertragen ([SCP](https://de.wikipedia.org/wiki/Secure_Copy) & [rsync](https://de.wikipedia.org/wiki/Rsync))

### [SCP](https://de.wikipedia.org/wiki/Secure_Copy)
- SCP eignet sich, um Dateien auf Linux-Systemen manuell zu übertragen.
- SCP ist einfach zu nutzen und da die Verbindung über [SSH](https://de.wikipedia.org/wiki/Secure_Shell) läuft, vorausgesetzt, es werden starke Schlüssel bzw. Anmeldedaten verwendet, auch sehr sicher.
- Für `manuelle Übertragungen ist SCP eine gute Wahl`, für `Automatisierungen sollte allerdings eher rsync genutzt werden`.


### [rsync](https://de.wikipedia.org/wiki/Rsync)
- rsync wird zur Übertragung von Daten von einem Server zu einem anderen Server verwendet.
- rsync läuft ebenfalls über [SSH](https://de.wikipedia.org/wiki/Secure_Shell) was eine sichere Verbindung ermöglicht. 
- Dabei läuft die Übertragung nur von einem Quell- zu einem Zielverzeichnis.
- Eine Besonderheit ist, dass rsync auch Teile von Dateien kopieren kann, sowie abgebrochene Transfers fortgesetzt werden können.
- Daher wird rsync häufig zum Übertragen von Backups von Serversystemen eingesetzt, denn rsync lässt sich mithilfe von Cronjobs oder Skripten gut automatisieren.


## rsync - automatisiertes Übertragen des Datenbank-Backups
- Dafür wird neben dem automatisierten Datenbank-Backup auf dem Quellserver, von dem die Backups übertragen werden sollen, auch ein Zielserver benötigt.


- Zunächst [SSH](https://de.wikipedia.org/wiki/Secure_Shell) und rsync auf beiden Systemen installieren
```
$ sudo apt update
$ sudo apt install openssh-server
$ sudo apt install rsync
```

- Autostarts von SSH & rsync auf beiden Systemen einstellen
```
$ sudo systemctl enable ssh
$ sudo systemctl status ssh
```
```
$ sudo systemctl enable rsync
$ sudo systemctl status rsync
```

- [Firewall](https://de.wikipedia.org/wiki/Firewall) auf beiden Systemen aktivieren und Port 22 öffnen
```
$ sudo ufw allow 22
$ sudo ufw enable
$ sudo ufw status
```


### SSH-Zugang mit schlüsselbasierter Authentifizierung einrichten
- Auf dem Quellserver Schlüsselpaar generieren:
	- KOMMENTAR: ist optional, hier kann notiert werden, wofür das Schlüsselpaar ist, z.B. Wikijs-rsync-Backup-SSH-Key.
```
$ cd .ssh
$ ssh-keygen -t rsa -b 4096 -C "KOMMENTAR"
```
- Um Standardeinstellungen und Standardpfad zu nutzen, `ENTER` drücken.
- Die Passphrase sollte für rsync unbedingt mit `ENTER` übersprungen werden, damit die Automatisierung über Cron funktioniert.
- Die Dateinamen des öffentlichen und privaten Schlüssels sollten bis auf die Endung `.pub`, gleich sein.
- Öffentlichen Schlüssel von Quellserver zum Zielserver kopieren
- Befehl für Standardeinstellungen (standard Dateiname):
    - USER: Benutzernamen des rsync-Backup-Benutzers auf dem Zielserver
    - ZIELSERVER_IP: IP-Adresse des Zielservers
```
$ ssh-copy-id USERNAME@ZIELSERVER_IP
```
- wenn ein anderer Dateiname für den public key oder ein anderer Pfad genutzt wird:
```
$ ssh-copy-id -i ~/.ssh/DATEINAME-PUBLIC-KEY.pub USERNAME@ZIELSERVER_IP
```
```
$ ssh-copy-id -i /PFAD/ZUM/DATEINAME-PUBLIC-KEY.pub USERNAME@ZIELSERVER_IP
```


### SSH-Zugang vom Quellserver zum Zielserver testen:
- An dieser Stelle sollte der Verbindungsaufbau ohne Passwortabfrage funktionieren.
- Wenn der Name des Schlüsselpaares nicht angepasst wurde (Standard):
    - USER: Benutzernamen des rsync-Backup-Benutzers auf dem Zielserver
    - ZIELSERVER_IP: IP-Adresse des Zielservers
```
$ ssh USERNAME@ZIELSERVER_IP
```

- Dateiname des privaten Schlüssels wurde angepasst:
```
$ ssh -i ~/PFAD/ZUM/PRIVAT-KEY USERNAME@ZIELSERVER_IP
$ ssh -i ~/.ssh/PRIVAT-KEY USERNAME@ZIELSERVER_IP
```

- `-v` logging zur Fehlerbehebung:
```
$ ssh -v USERNAME@ZIELSERVER_IP
```


### rsync-Befehl konfigurieren
- Pfade überprüfen und Pfad auf dem Zielserver einstellen.
- manueller Beispielbefehl (ausführen auf Quellserver):
	- Hinweis: Der abschließende Schrägstrich `/` am Quellverzeichnis sorgt dafür, dass der Inhalt synchronisiert wird, nicht das Verzeichnis selbst.
	- Nicht vergessen, den SSH-Teil anzupassen, wenn für das Schlüsselpaar nicht der Standard genutzt wurde.
```
$ rsync -avz /PATH/TO/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/wiki-backup/zielserver/
```

- rsync-Optionen:
    - `-a` (archive): Erhält Dateirechte, symbolische Links, etc.
    - `-z` (compress): Komprimiert die Daten während der Übertragung.
    - `-v` (verbose): Für detaillierte Ausgaben.
    - `--delete`: Löscht auf dem Ziel veraltete Dateien (für exakte Synchronisation)
    - `--no-perms`: Keine Übertragung von Datei-Berechtigungen
    

### Testlauf (optional)
- Befehl manuell ausführen:
	- Hinweis: Mit `--dry-run` wird nur simuliert, ohne Änderungen durchzuführen.
	- Nicht vergessen, den SSH-Teil anzupassen, wenn für das Schlüsselpaar nicht der Standard genutzt wurde.
```
$ rsync -avz --dry-run /PATH/TO/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/wiki-backup/zielserver/
```


### Cronjob einrichten (automatisierung)
```
$ crontab -e
```

- Cronjob hinzufügen:
	- Beispiel für nächtliche Übertragung um 2:30 Uhr:
```
30 2 * * * /usr/bin/rsync -avz /PATH/TO/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/wiki-backup/zielserver/ >> /PATH/TO/wiki-backup/rsync-cron.log 2>&1
```

- Mit Angabe von SSH private-Key-Datei:
```
30 2 * * * /usr/bin/rsync -avz -e "ssh -i /home/USER/.ssh/private-key" /PATH/TO/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/wiki-backup/zielserver/ >> /PATH/TO/wiki-backup/rsync-cron.log 2>&1
```

- Überprüfung und Fehlerbehebung
- eigene Log-Datei (rsync-cron.log) prüfen
```
$ tail -f /PATH/TO/wiki-backup/rsync-cron.log
$ tail /PATH/TO/wiki-backup/rsync-cron.log
```




-------------------------------------------------------------------------------------------------------------


# 5. PostgreSQL-Datenbank-Backups wiederherstellen
- Es wird empfohlen, vor der Wiederherstellung einen Testlauf in einer Testumgebung durchzuführen, um sicherzustellen, dass keine unerwarteten Probleme auftreten.
- [Link zur offiziellen Dokumenation für Docker-Backups-wiederherstellen](https://docs.requarks.io/en/install/transfer)


### Server (Ubuntu-Server) & Docker installieren
- Dafür kann die [Installationsanleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/wikijs) verwendet werden.
- GGf. auch den Reverse Proxy installieren und konfigurieren !
- Noch KEINE Container starten !

### docker-compose.yml Konfiguration (neuer Server)
- Es sollte die gleiche Konfiguration verwendet werden.
- Grundsätzlich sind sind sogar Anpassungen möglich, jedoch sollten, unter "db" `container_name`, `POSTGRES_DB`, `POSTGRES_PASSWORD`, `POSTGRES_USER` und unter "wiki" `container_name`, `DB_TYPE`, `DB_USER`, `DB_PASS` und `DB_NAME` gleich bleiben !


### aktuelles Datenbank-Backup auf den neuen Server übertragen
- Entweder mit rsync oder SCP ein aktuelles Datenbank-Backup vom BAckup-Server auf den neuen Server übertragen.


### Benutzer zur Docker Gruppe hinzufügen
- Aktuellen Benutzer zur Docker-Gruppe hinzufügen, damit kein sudo nötig ist.
    - 
```
$ whoami
$ sudo usermod -aG docker BENUTZERNAME
$ logout
- erneut einloggen
$ id
```


### Docker-Container
- Sicherstellen, dass KEIN Container läuft:
```
$ cd wiki
$ docker compose ps
```

- Nur Datenbank-Container starten:
```
$ docker compose up -d db
```

- Sobald dieser Befehl die Ausgabe `...accepting connections` bringt, ist da Datenbank bereit
```
$ docker compose exec db pg_isready -U wikijs -d wiki
```


### Wiederherstellung des Datenbank-Dumps (Datenbankwiederherstellung)
- Berechtigungnen für die Backup-Datenbank-Datei setzen und anschlißend Datenbank wiederherstellen
    - `/PFAD/ZUR/...` ersetzen mit tatsächlichem Pfad zur SQL-Datenbank
    - `cat /PFAD/ZUR/wiki_js_dump.sql |` ließt die Datenbank
    - `docker compose exec -T db` lädt die Datenbank in den Docker-Container (nimmt dabei die benötigten Variablen aus der docker-compose.yml)
    - `psql -U wikijs -d wiki` Benutzername und Datenbankname
```
$ cd /PFAD/ZUR/BACKUP-DATEI/
$ pwd
$ ls
$ cd ..
$ cd wiki
$ cat /PFAD/ZUR/wiki_js_dump.sql | docker compose exec -T db psql -U wikijs -d wiki
```

- Container neu starten
```
$ docker compose up -d
$ docker compose ps
```

- Überprüfen:
	- Browser öffnen und ggf. den Browser-Cache leeren/ privates Browserfenster nutzen
	- Prüfen, ob alles wiederhergestellt wurde.


-------------------------------------------------------------------------------------------------------------


# 6. Datenbank-Backups automatisch löschen nach entsprechender Zeit
- Damit sich der Speicher des Servers nicht mit Datenbank-Backups füllt, wird folgend gezeigt, wie man die Backups automatisch auch wieder löschen kann.
- Dabei werden natürlich nur die erstellten Datenbank-Backups gelöscht, die lokal auf dem Server auf welchem auch das Wiki.js läuft gelöscht, wenn gewünscht muss ein solches Konzept auch auf dem Backup-Server/Medium eingerichtet werden.


### erstellte Backups nach einiger Zeit automatisch löschen

1. Skript erstellen, welches alte Backups löscht
```
$ sudo nano alte-backups-loeschen.sh
```
- Folgendes in die Datei einfügen und Pfad anpassen:
    - `-type f`: Sucht nur nach Dateien
    - `-name "*.sql"`: Filtert nach SQL-Dateien
    - `-mtime +30`: Dateien, die älter sind als 30 Tage
    - `-delete`: Löscht die entsprechenden Dateien
```
#!/bin/bash
find /PFAD/ZU/DEN/BACKUPS/ -type f -name "*.sql" -mtime +30 -delete
```

- Skript audführbar machen
```
$ sudo chmod +x /PFAD/ZUM/SKRIPT/alte-backups-loeschen.sh
```

2. Cronjob einrichten (damit Skript automatisch ausgeführt wird)
```
$ crontab -e
```
- z.B. Folgendes in einfügen und Pfad anpassen:
    - Führt das Skript jede Nacht um 3:30 Uhr aus.
```
30 3 * * * /PFAD/ZUM/SKRIPT/alte-backups-loeschen.sh
```

- Optional Cronjob, der nur alls X-Tage ausgeführt wird:
    - Cronjob wird nur alle 4 Tage ausgeführt (Ausführung am 1; 5; 9; 13; 17; 21; 25; 29; des Monats)
    - Die "4" kann natürlich ersetzt werden.
```
30 3 */4 * * /PFAD/ZUM/SKRIPT/alte-backups-loeschen.sh
```

3. Skript manuell ausführen, um Funktionsfähigkeit des Skriptes zu überprüfen
- Den Vollständigen Pfad angeben, um das Skript auszuführen oder `./` verwenden, wie folgend gezeigt:
```
$ sudo /PFAD/ZUM/SKRIPT/alte-backups-loeschen.sh
oder
$ sudo ./alte-backups-loeschen.sh
```
- Nun den Backup-Ordner überprüfen, ob noch Backups, die älter als der angegebene Zeitraum sind, vorhanden sind.


### Log-Dateien automatisch leeren, wenn Dateigröße überschritten wird
1. Skript erstellen, welches alte Logs leert
```
$ sudo nano alte-logs-leeren.sh
```
- Folgendes in die Datei einfügen und Pfad anpassen:
    - `MAX_SIZE_MB=20`: Maximale Größe in MB, in angepasst werden
    - `LOG_DIR="/home/USER/wiki-backup"`: Pfad zu den Backups angeben
```
#!/bin/bash
LOG_DIR="/home/USER/wiki-backup"
MAX_SIZE_MB=20  # Maximale Größe in MB
MAX_SIZE_BYTES=$((MAX_SIZE_MB * 1024 * 1024))  # Umrechnung in Bytes (einmalig)

# Prüfe und trimme jede Log-Datei im angegeben Pfad:
for LOG_FILE in "$LOG_DIR"/*.log; do
    # Prüfe, ob die Datei existiert und zu groß ist
    if [ -f "$LOG_FILE" ] && [ $(stat -c%s "$LOG_FILE") -gt $MAX_SIZE_BYTES ]; then
        # Leere die Log-Datei (behält Berechtigungen/Eigentümer)
        truncate -s 0 "$LOG_FILE"
        # Optional: Schreibe eine Info, wann die Log-Datei geleert wurde
        echo "--- Log geleert am $(date) ---" >> "$LOG_FILE"
    fi
done
```

- Skript audführbar machen
```
$ sudo chmod +x /PFAD/ZUM/SKRIPT/alte-logs-leeren.sh
```

2. Cronjob einrichten (damit Skript automatisch ausgeführt wird)
```
$ crontab -e
```
- z.B. Folgendes in einfügen und Pfad anpassen:
    - Führt das Skript jede Nacht um 5:45 Uhr aus.
```
45 5 * * * /PFAD/ZUM/SKRIPT/alte-logs-leeren.sh
```

- Optional Cronjob, der nur alls X-Tage ausgeführt wird:
    - Cronjob wird nur alle 6 Tage ausgeführt (Ausführung am 1. und 16. des Monats)
    - Die "15" kann natürlich ersetzt werden.
```
45 5 */15 * * /PFAD/ZUM/SKRIPT/alte-logs-leeren.sh
```

- Optional Cronjob, der nur einmal im Monat ausgeführt wird:
    - Cronjob wird nur am 2. des Monats ausgeführt
```
45 5 2 * * /PFAD/ZUM/SKRIPT/alte-logs-leeren.sh
```


3. Skript manuell ausführen, um Funktionsfähigkeit des Skriptes zu überprüfen
- Den Vollständigen Pfad angeben, um das Skript auszuführen oder `./` verwenden, wie folgend gezeigt:
```
$ sudo /PFAD/ZUM/SKRIPT/alte-logs-leeren.sh
oder
$ sudo ./alte-logs-leeren.sh
```


-------------------------------------------------------------------------------------------------------------
