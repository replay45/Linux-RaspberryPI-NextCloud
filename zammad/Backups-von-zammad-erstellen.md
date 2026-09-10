# Backups von [Zammad](https://zammad.com/) erstellen

`Anleitung erstellt am 17.7.2025, zuletzt bearbeitet am 8.9.2026`


## Inhaltsverzeichnis
1. Einsatzzweck "Snapshots"
2. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte) - [Docker](https://de.wikipedia.org/wiki/Docker_(Software))
3. Zammad-Docker-Backup-Methode
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


# 2. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte) - [Docker](https://de.wikipedia.org/wiki/Docker_(Software))
- Beim Backup der Datenbank sollten alle Konfigurationen, Einstellungen und Inhalte von Zammad gebackupt werden.
- Dieses kann ebenfalls automatisiert werden.


## Hinweis - Backups von Konfigurationsdateien
- Je nach verwendeten Konfigurationsdateien sollte man unbedingt diese ebenfalls sichern.
- `.env`
    - Diese Konfigurationsdatei sollte aufgrund der enthaltenen Werte für die docker-compose.yml sicheren. Die docker-compose.yml selber muss nicht zwingend gesichert werden, da diese aus dem Repository heruntergeladen wird und sowieso nicht manuell berarbeitet werden sollte (dafür gibt es ja die .env-Datei).
- `Cronjobs, Skripte etc.`
    - Außerdem sollte man sich verwendetete [Cronjobs](https://de.wikipedia.org/wiki/Cron) und Skripte kopieren und mit den erstellten Backups ablegen.


## Datenbanktyp prüfen
- Datenbanktyp überprüfen
```
$ cat docker-compose.yml
```
- Da sollte folgender Eintrag zu finden sein: `POSTGRESQL_DB`
- Die weitere Anleitung beschränkt sich auf PostgreSQL-Datenbanken.


## PostgreSQL-Datenbank manuell sichern
```
$ sudo docker exec CONTAINER_NAME pg_dump -U POSTGRES_USER POSTGRES_DB > /PATH/TO/BACKUP/zammad-dump-manuell_$(date +%d-%m-%Y).sql
```

- Die folgenden Parameter (Platzhalter) sind alle in der `docker-compose.yml` enthalten:
    - `CONTAINER_NAME`: Der Name des Docker-Containers, der die PostgreSQL-Datenbank betreibt (meistens: zammad-docker-compose-zammad-postgresql-1), Container anzeigen: `$ sudo docker ps`
    - `POSTGRES_USER`: Der Benutzername der Datenbank (meistens: zammad)
    - `POSTGRES_DB`: Der Name der Datenbank (z. B. zammad_production)
    - `/PATH/TO/BACKUP`: Verzeichnis, in dem das Backup gespeichert werden soll (Verzeichnis muss ggf. manuell angelegt werden)


## Automatisieren ([Cronjobs](https://de.wikipedia.org/wiki/Cron))
- Benutzer in die Docker-Gruppen hinzufügen (damit Befehl korrekt ohne sudo ausgeführt werden kann)
	- Damit die Änderungen wirksam werden, Benutzer abmelden und erneut anmelden.
	- Platzhalter `USER` durch tatsächlichen Usernamen austauschen.
```
$ whoami
$ sudo usermod -aG docker USER
```

- Prüfen, ob der Benutzer in der gewünschten Gruppe ist
    - Platzhalter `USER` mit tatsächlichem Usernamen austauschen
```
$ groups USER
```

- Cronjob einrichten
    - Editor auswählen, am besten `nano`
```
$ crontab -e
```

- Beispiel für nächtliches Backup um 3:00 Uhr:
    - Großgeschriebenes muss individuell angepasst werden. Dabei können die Informationen zur Datenbank aus der `docker-compose.yml` und der Containername mithilfe von `$ docker ps` entnommen werden.
    - `pg_dump -U POSTGRES_USER -f /tmp/zammad_dump_$(date +\%d-\%m-\%Y).sql POSTGRES_DB`: sichert die Datenbank in dem Docker-Container selber und mit `&& /usr/bin/docker cp CONTAINER_NAME:/tmp/zammad_dump_$(date +\%d-\%m-\%Y).sql` wird die gesicherte Datenbank aus dem Container in `/PATH/TO/BACKUP/zammad_dump_$(/bin/date +\%F).sql` kopiert.
    - Hiermit: `&& /usr/bin/docker exec CONTAINER_NAME rm -f /tmp/zammad_dump_$(date +\%d-\%m-\%Y)` wird das Backup aus dem temporären verzeichnis entfernt.
    - `>> /PATH/TO/BACKUP/sqldatenbank-backup-cron.log 2>&1`: ist ein optionaler Teil, Cron legt eine log-Datei an, in die Fehler geschrieben werden können.
```
0 3 * * * /usr/bin/docker exec CONTAINER_NAME pg_dump -U POSTGRES_USER -f /tmp/zammad_dump_$(date +\%d-\%m-\%Y).sql POSTGRES_DB && /usr/bin/docker cp CONTAINER_NAME:/tmp/zammad_dump_$(date +\%d-\%m-\%Y).sql /PATH/TO/BACKUP/zammad_dump_$(date +\%d-\%m-\%Y).sql && /usr/bin/docker exec CONTAINER_NAME rm -f /tmp/zammad_dump_$(date +\%d-\%m-\%Y) >> /PATH/TO/BACKUP/sqldatenbank-backup-cron.log 2>&1
```


- optional prüfen, ob /tmp im Container vorhanden ist und Schreibrechte existieren
	- Dafür wird in die Shell des Containers gewechselt
	- Container sollte heißen: `zammad-docker-compose-zammad-postgresql-1`
```
$ cd zammad-docker-compose
$ docker ps
$ docker exec -it CONTAINER_NAME bash
# ls -ld /tmp
# exit
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


# 3. Zammad-Docker-Backup-Methode
- Dieses Vorgehen basiert auf der [offiziellen "Backup & Restore" Dokumentation](https://docs.zammad.org/en/latest/appendix/backup-and-restore/docker-compose.html)
- Die offizielle Zammad-Docker-Compose-Installation erzeugt standardmäßig jede Nacht um 03:00 Uhr ein Backup.
- Dabei enthält das Backup sowohl die Zammad-Datenbank als auch den Filesystem-Storage (für Anhänge).


### Docker-Backup einrichten
- Verzeichnis wo die docker-compose.yml liegt:
```
$ cd /zammad-docker-compose
```

- Docker Volume herausfinden:
    - gesucht wird ein Volume, dass etwa so heißen sollte:  `zammad-docker-compose_zammad-backup`
```
$ docker volume ls
```

- aktuellen Stand prüfen:
```
$ docker run --rm \
  -v zammad-docker-compose_zammad-backup:/backup:ro \
  alpine \
  find /backup -type f -print
```

- manuell ein Zammad-Docker-Backup erzeugen
```
$ docker compose run --rm --env BACKUP_ONCE=true zammad-backup
```

- Die Ausgabe sollte folgendes zeigen:
```
Performing a single backup…
DATUMUHRZEIT - backing up zammad...
tar: Removing leading `/' from member names
backup finished :)
```

- Prüfen, was Zammad erstellt hat:
```
$ docker run --rm \
  -v zammad-docker-compose_zammad-backup:/backup:ro \
  alpine \
  find /backup -type f -print
```

- Backups aus Docker-Volumen in lokales Volume kopieren
    - Es muss nur `/PATH/TO/zammad/zammad-docker-backups` angepasst werden.
```
$ docker run --rm \
  -v zammad-docker-compose_zammad-backup:/source:ro \
  -v /PATH/TO/zammad/zammad-docker-backups:/destination \
  alpine \
  cp -a /source/. /destination/
```

- Hinweis:
    - Jedes Backup Paar sollte aus 2 Dateien bestehen
    -`..._zammad_files.tar.gz` und
    - `..._zammad_db.psql.gz`
    - Wenn mehrere Dateien vorhanden sind, dann sind das vermutlich Backups von unterschiedlichen Zeitpunkten.


### automatisieren (Skript + Cronjob)
- Skript erstellen
```
$ cd /PATH/TO/zammad/zammad-docker-backups
$ sudo nano zammad-docker-backup.sh
```

- Skript einfügen
    - Das Skript macht folgendes:
    - Als erstes wird ein Ordner mit aktuellem Datum erstellt.
    - Danach sucht es nach den neusten zammad-docker-Backups die Zammad automatisch bereits erstellt und ermittelt den Zeitstempel
    - Zuletzt werden dann die zusammengehöhrigen Dateien aus dem Docker-Container exportiert und in den lokalen Ordner importiert.
    - Dabei muss ggf. der `DEST-Pfad` "`/PATH/TO/zammad/zammad-docker-backups`" angepasst werden !
```
#!/bin/bash

VOLUME="zammad-docker-compose_zammad-backup"
DEST="/PATH/TO/zammad/zammad-docker-backups/$(date +%Y-%m-%d)"

mkdir -p "$DEST"

docker run --rm \
  -v "$VOLUME:/source:ro" \
  -v "$DEST:/destination" \
  alpine \
  sh -c '
    FILE=$(ls -t /source/*_zammad_files.tar.gz | head -1)
    TS=$(basename "$FILE" | cut -d_ -f1)
    cp /source/${TS}_* /destination/
  '
```
- Speichern mit `STRG+X`
- Nun das Skript noch ausführbar machen
```
$ sudo chmod +x zammad-docker-backup.sh
```

- Nun einmal das Skript manuell testen:
```
$ ./zammad-docker-backup.sh
$ ls 
```

- Wenn das Skript noch nicht ausgeführt werden kann müssen Berechtigungen für den Pfad angepasst werden:
    - Zunächst Berechtigungen prüfen, dann Schreibrechte setzen, ggf. Änderungen prüfen
    - `BENUTZERNAME` ersetzten mit dem Benutzer unter dem der Cronjob ausgeführt werden soll, wenn mit dem aktuellen Benutzer, z.B. "admin", dann entsprechend  den Platzhalter ersetzen.
```
$ ls -ld /PATH/TO/zammad/zammad-docker-backups/
$ whoami
$ sudo chown -R BENUTZERNAME:BENUTZERNAME /PATH/TO/zammad/zammad-docker-backups/
$ sudo chmod 755 /PATH/TO/zammad/zammad-docker-backups/
```

- Wenn die manuelle Ausführung läuft, Cronjob einrichten
```
$ crontab -e
```
```
04 * * * /bin/bash /PATH/TO/zammad/zammad-docker-backups/zammad-docker-backup.sh >> /PATH/TO/zammad/zammad-docker-backups/zammad-docker-backup-export.log 2>&1
```

- Wenn die Backups erfolgreich exportiert werden, dann kann die automatische Übertragung auf einen anderen Server via rsync eingerichtet werden.


-------------------------------------------------------------------------------------------------------------


# 4. Erstellte Datenbank- & Docker-Backups auf einen anderen Server übertragen ([SCP](https://de.wikipedia.org/wiki/Secure_Copy) & [rsync](https://de.wikipedia.org/wiki/Rsync))

### [SCP](https://de.wikipedia.org/wiki/Secure_Copy)
- SCP eignet sich, um Dateien auf Linux-Systemen manuell zu übertragen.
- SCP ist einfach zu nutzen und da die Verbindung über [SSH](https://de.wikipedia.org/wiki/Secure_Shell) läuft, vorausgesetzt, es werden starke Schlüssel bzw. Anmeldedaten verwendet, auch sehr sicher.
- Für `manuelle Übertragungen ist SCP eine gute Wahl`, für `Automatisierungen sollte allerdings rsync genutzt werden`.


### [rsync](https://de.wikipedia.org/wiki/Rsync)
- rsync wird zur Übertragung von Daten von einem Server zu einem anderen Server verwendet.
- rsync läuft ebenfalls über [SSH](https://de.wikipedia.org/wiki/Secure_Shell) was eine sichere Verbindung ermöglicht. 
- Dabei läuft die Übertragung nur von einem Quell- zu einem Zielverzeichnis.
- Eine Besonderheit ist, dass rsync auch Teile von Dateien kopieren kann, sowie abgebrochene Transfers fortgesetzt werden können.
- Daher wird rsync häufig zum Übertragen von Backups von Serversystemen eingesetzt, denn rsync lässt sich mithilfe von Cronjobs oder Skripten gut automatisieren.


## rsync - automatisiertes Übertragen des Datenbank-Backups
- Dafür wird neben dem automatisierten Datenbank & Docker-Backup auf dem Quellserver, von dem die Backups übertragen werden sollen, auch ein Zielserver benötigt.


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
	- KOMMENTAR: ist optional, hier kann notiert werden, wofür das Schlüsselpaar ist, z.B. Zammad-rsync-Backup-SSH-Keys.
```
$ cd .ssh
$ ssh-keygen -t rsa -b 4096 -C "KOMMENTAR"
```
- Um Standardeinstellungen und Standardpfad zu nutzen, `ENTER` drücken.
- Die Passphrase sollte für rsync unbedingt übersprungen werden, damit die Automatisierung über Cron funktioniert.
- Die Dateinamen des öffentlichen und privaten Schlüssels sollten bis auf die Endung `.pub`, gleich sein.
- Öffentlichen Schlüssel von Quellserver zum Zielserver kopieren
- Befehl für Standardeinstellungen (standard Dateiname):
    - USER: Benutzernamen des rsync-Backup-Benutzers auf dem Zielserver
    - ZIELSERVER_IP: IP-Adresse des Zielservers
```
$ ssh-copy-id USERNAME@ZIELSERVER_IP
```
- Wenn ein anderer Dateiname für den public key oder ein anderer Pfad genutzt wird:
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
$ rsync -av /PATH/TO/zammad-backups/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/zammad-backups/zielserver/
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
$ rsync -av --dry-run /PATH/TO/zammad-backups/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/zammad-backups/zielserver/
```


### Cronjob einrichten (automatisierung)
```
$ crontab -e
```

- Cronjob hinzufügen:
	- Beispiel für nächtliche Übertragung um 4:30 Uhr:
```
30 4 * * * /usr/bin/rsync -av /PATH/TO/zammad-backups/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/zammad-backups/zielserver/ >> /PATH/TO/zammad-backups/rsync-cron.log 2>&1
```

- Mit Angabe von SSH private-Key-Datei:
```
30 4 * * * /usr/bin/rsync -av -e "ssh -i /home/USER/.ssh/private-key" /PATH/TO/zammad-backups/quellserver/ USERNAME@ZIELSERVER_IP:/PATH/TO/zammad-backups/zielserver/ >> /PATH/TO/zammad-backups/rsync-cron.log 2>&1
```

- Überprüfung und Fehlerbehebung
- eigene Log-Datei (rsync-cron.log) prüfen
```
$ tail -f /PATH/TO/zammad-backups/rsync-cron.log
$ tail /PATH/TO/zammad-backups/rsync-cron.log
```


-------------------------------------------------------------------------------------------------------------

