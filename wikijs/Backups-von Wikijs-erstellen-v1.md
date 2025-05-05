# Backups von Wikijs erstellen

`Anleitung erstellt am 7.3.2025`


Es gibt viele Möglichkeiten, Backups anzulegen. Die Schwierigkeit besteht darin, ein Backup wieder einzuspielen, denn meistens fehlen im Wiki.js selber die Wiederherstellungsfunktionen.
Ein paar sinnvolle Möglichkeiten/ Alternativen werden hier gezeigt.



## Inhaltsverzeichnis
1. Einsatzzweck "Snapshots"
2. Backups der Inhalte auf einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)-Server
3. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte)
4. Erstellte Datenbank-Backups auf einen anderen Server übertragen ([SCP](https://de.wikipedia.org/wiki/Secure_Copy) & [rsync](https://de.wikipedia.org/wiki/Rsync))
5. PostgreSQL-Datenbank-Backups wiederherstellen
6. [Docker](https://de.wikipedia.org/wiki/Docker_(Software))-Volumes sichern - nur in bestimmmten Fällen erforderlich


-------------------------------------------------------------------------------------------------------------


# 1. Einsatzzweck "Snapshots"

- Snapshots erstellt man in der Regel von virtuellen Maschienen auf Host-Systemen (meist mit Server-Betriebssystemen).
- Dabei speichert man einen Zustand einer virtuellen Maschiene, um z.B. Änderungen an dem System vorzunehmen und nach erfolgreichem Implementieren, werden diese wieder gelöscht.
- Sollten Probleme bei den Änderungen auftreten, kann durch den Snapshot der vorherige Zustand schnell wiederhergestellt werden.
- Dabei eignen sich Snapshots nur als `temporäre Sicherung vor der Implementierung von Änderungen`.
- Das dauerhafte Speichern von Snapshots kann sehr Ressourcenintensiv sein und zu Problemen führen, daher empfiehlt sich die Nutzung von Snapshots immer nur temporär.
- Snapshots ersetzen keine vollständige Backupstrategie und Datenbank-Backups !


-------------------------------------------------------------------------------------------------------------


# 2. Backups der Inhalte auf einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)-Server

Wichtig: Diese Methode erstellt Backups `nur von Inhalten`, wie Seiten im HTML format und Bildern. Es werden `keine` Konfigurationen oder Datenbanken gesichert. Außerdem gibt es auch keine Wiederherstellungsfunktion für diese Methode !

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
$ cd wikijs
$ sudo docker-compose ps
$ sudo tail -f /var/log/auth.log
$ sudo docker-compose logs -f
```


-------------------------------------------------------------------------------------------------------------


# 3. Datenbank-Backups: (Konfigurationen, Einstellungen & Inhalte)
- Beim Backup der Datenbank sollten alle Konfigurationen, Einstellungen und Inhalte gebackupt werden.
- Dieses kann ebenfalls automatisiert werden.


## Datenbanktyp prüfen
- Datenbanktyp herausfinden
```
$ nano docker-compose.yml
```
- Da sollte folgender Eintrag stehen:
`DB_TYPE=postgres`

- Die weitere Anleitung beschränkt sich auf PostgreSQL-Datenbanken.


## PostgreSQL-Datenbank manuell sichern
```
$ sudo docker exec CONTAINER_NAME pg_dump -U DB_USER DB_NAME > /PATH/TO/BACKUP/wiki_js_dump.sql
```
```
- Die folgenden Parameter (Platzhalter) sind alle in der `docker-compose.yml` enthalten:
- CONTAINER_NAME: Der Name des Docker-Containers, der die PostgreSQL-Datenbank betreibt.
- DB_USER: Der Benutzername der Datenbank (meistens wiki)
- DB_NAME: Der Name der Datenbank (z. B. wiki)
- /PATH/TO/BACKUP: Verzeichnis, in dem das Backup gespeichert werden soll (muss manuell angelegt werden)
```


## Automatisieren (Cronjob)
- Benutzer in die Docker-Gruppen hinzufügen (damit Befehl korrekt ohne sudo ausgeführt werden kann)
	- Damit die Änderungen wirksam werden, Benutzer abmelden und erneut anmelden.
```
$ sudo usermod -aG docker USER
```

- Prüfen, ob der Benutzer in der gewünschten Gruppe ist
```
$ groups USER
```


- Cronjob einrichten
```
$ crontab -e
```
Editor auswählen, am besten `nano`


- Beispiel für nächtliches Backup um 2:00 Uhr:
```
0 2 * * * /usr/bin/docker exec CONTAINER_NAME pg_dump -U DB_USER -f /tmp/wiki_js_dump.sql DB_NAME && /usr/bin/docker cp CONTAINER_NAME:/tmp/wiki_js_dump.sql /PATH/TO/BACKUP/wiki_js_dump_$(/bin/date +\%F).sql 2>&1 >> /home/USER/wiki-backup/sqldatenbank-backup-cron.log
```
- Mit `STRG+X` speichern

- Großgeschriebenes muss individuell angepasst werden. Dabei können die Informationen zur Datenbank aus der `docker-compose.yml` entnommen werden.
```
- `2>&1 >> /home/USER/wiki-backup/cron.log` ist ein optionaler Teil, Cron legt eine log-Datei an, in die Fehler geschrieben werden können.
- `pg_dump -U DB_USER -f /tmp/wiki_js_dump.sql DB_NAME` sichert die Datenbank in dem Docker-Container selber und mit `&& /usr/bin/docker cp CONTAINER_NAME:/tmp/wiki_js_dump.sql` wird die gesicherte Datenbank aus dem Container in den `/PATH/TO/BACKUP/wiki_js_dump_$(/bin/date +\%F).sql` kopiert.
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

ungefähre erwartete Ausgabe: -rw-r--r--  1 root  root  DATUM UHRZEIT /tmp/testfile
```


## Wenn Probleme mit der Automatisierung auftreten, dann Folgedes in `Crontab -e` einfügen
- Normalerweise muss die PATH-Zeile (die folgende Zeile) nicht angepasst werden.
```
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```


## Cronjob überprüfen
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
$ tail -f /home/USER/wiki-backup/sqldatenbank-backup-cron.log
$ tail /home/USER/wiki-backup/sqldatenbank-backup-cron.log
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
	- KOMMENTAR: ist optional, hier kann notiert werden, wofür das Schlüsselpaar ist.
```
$ ssh-keygen -t rsa -b 4096 -C "KOMMENTAR"
```
- Um Standardeinstellungen und Standardpfad zu nutzen, `ENTER` drücken.
- Die Passphrase sollte für rsync unbedingt übersprungen werden, damit die Automatisierung über Cron funktioniert.
- Die Dateinamen des öffentlichen und privaten Schlüssels sollten bis auf die Endung `.pub`, gleich sein.

- Öffentlichen Schlüssel von Quellserver zum Zielserver kopieren
- Befehl für Standardeinstellungen (standard Dateiname):
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
```
$ ssh USERNAME@ZIELSERVER_IP
```

- Dateiname des privaten Schlüssels wurde angepasst:
```
$ ssh -i ~/PFAD/ZUM/PRIVAT-KEY USER@IP
$ ssh -i ~/.ssh/private-key USER@IP
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
$ rsync -avz /home/USER/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/home/USER/wiki-backup/zielserver/
```

- rsync-Optionen:
```
-a (archive): Erhält Dateirechte, symbolische Links, etc.
-z (compress): Komprimiert die Daten während der Übertragung.
-v (verbose): Für detaillierte Ausgaben.
--delete: Löscht auf dem Ziel veraltete Dateien (für exakte Synchronisation)
```


### Testlauf (optional)
- Befehl manuell ausführen:
	- Hinweis: Mit `--dry-run` wird nur simuliert, ohne Änderungen durchzuführen.
	- Nicht vergessen, den SSH-Teil anzupassen, wenn für das Schlüsselpaar nicht der Standard genutzt wurde.
```
$ rsync -avz --dry-run /home/USER/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/home/USER/wiki-backup/zielserver/
```


### Cronjob einrichten (automatisierung)
```
$ crontab -e
```

- Cronjob hinzufügen:
	- Nicht vergessen, den SSH-Teil anzupassen, wenn für das Schlüsselpaar nicht der Standard genutzt wurde.
	- Beispiel für nächtliche Übertragung um 3:00 Uhr:
```
0 3 * * * /usr/bin/rsync -avz /home/USER/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/home/USER/wiki-backup/zielserver/ >> /home/USER/wiki-backup/rsync-cron.log 2>&1
```

- Mit Angabe von SSH private-Key-Datei:
```
0 3 * * * /usr/bin/rsync -avz -e "ssh -i /home/USER/.ssh/private-key" /home/USER/wiki-backup/quellserver/ USERNAME@ZIELSERVER_IP:/home/USER/wiki-backup/zielserver/ >> /home/USER/wiki-backup/rsync-cron.log 2>&1
```

- Überprüfung und Fehlerbehebung
- eigene Log-Datei (rsync-cron.log) prüfen
```
$ tail -f /home/USER/wiki-backup/rsync-cron.log
$ tail /home/USER/wiki-backup/rsync-cron.log
```


-------------------------------------------------------------------------------------------------------------


# 5. PostgreSQL-Datenbank-Backups wiederherstellen
- Es wird empfohlen, vor der Wiederherstellung einen Testlauf in einer Testumgebung durchzuführen, um sicherzustellen, dass keine unerwarteten Probleme auftreten.
- `Ubuntu-Server installieren`, dafür [Installationsanleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/wikijs) von Wiki.js folgen, die `Ersteinrichtung jedoch NICHT durchführen` !
- `Konfig aus dem alten Wiki übernehmen` und in der `docker-compose.yml` speichern.
- Wenn man alles installiert hat, die Konfig aus dem alten Wiki übernommen hat und nun die Ersteinrichtung für eine gewöhnliche Installation durchführen würde, für das Einspielen des Backups diese jedoch überspringen und stattdessen folgende Anleitung befolgen:
- Die Backups (`wiki_data_backup.tar.gz` und `wiki_js_dump.sql`) mit SCP auf den neuen Server kopieren.
- Sicherstellen, dass der Container läuft:
```
$ sudo docker-compose ps
```


## Wiederherstellung des Datenbank-Dumps (Datenbankwiederherstellung)
- Dieser Befehl lädt den SQL-Dump in die wiki-Datenbank in den PostgreSQL-Container.
- Wenn der Befehl fehlschlägt, nach unten zu Punkt `Datenbank neu erstellen` scrollen.

```
$ sudo docker exec -i DB_CONTAINER psql -U DB_USER -d DB_NAME < BACKUP/PFAD/wiki_js_dump.sql
```
Beispiel
```
$ sudo docker exec -i wikijs-db psql -U wikijs -d wiki < /home/user/wikijs-backups/wiki_js_dump.sql
```
```
- DB_CONTAINER: Name des Docker-Containers für die Datenbank `$ sudo docker-compose ps`
- DB_USER: kann in der docker-compose.yml nachgeschaut werden
- DB_NAME: kann in der docker-compose.yml nachgeschaut werden
- BACKUP/PFAD: Pfad, in dem das Backup abgelegt ist
```


- Container neu starten
```
$ docker-compose down
$ docker-compose up -d
```

- Überprüfen:
	- Browser öffnen und ggf. den Browser-Cache leeren
	- prüfen, ob alles wiederhergestellt wurde



### PostgreSQL-Datenbank neu erstellen (leer), um Wiederherstellung zu ermöglichen

- Mit der Standarddatenbank verbinden.
	- Parameter können aus der `docker-compose.yml` entnommen werden.
```
$ sudo docker exec -it CONTAINER_NAME psql -U DB_USER -d postgres
```
Beispiel
```
$ sudo docker exec -it wikijs-db psql -U wikijs -d postgres
```

- Jetzt sollte man in der Shell des Containers sein.
	- Aktive Verbindungen zur Wiki.js-Datenbank beenden (falls nötig).
```
$ SELECT pg_terminate_backend(pid)
$ FROM pg_stat_activity
$ WHERE datname = 'wiki';
```
- Wiki.js-Datenbank löschen
```
$ DROP DATABASE wiki;
```
- Wiki.js-Datenbank neu erstellen
```
$ CREATE DATABASE wiki OWNER wikijs;
```
```
$ exit
```
- Zurück zum Wiederherstellungsbefehl


-------------------------------------------------------------------------------------------------------------


# 6. [Docker](https://de.wikipedia.org/wiki/Docker_(Software))-Volumes sichern - nur in bestimmmten Fällen erforderlich
- Bei älteren Versionen oder abweichenden Anwendungs- & Konfigurationsszenarien, kann es evtl. sein, dass für ein vollständiges Backup, also Anhänge oder zusätzliche Konfigurationen, die im Docker-Volumen gespeichert werden, noch ein Backup des Docker-Volumens angelegt werden muss.
- In den meisten Fällen sollte das jedoch nicht nötig sein.
- Außerdem kann das Backup des Docker-Volumes bei der Automatisierung nur durchgeführt werden, wenn der Docker-Continer angehalten wird, daher empfielt sich ggf. eine manuelle Methode zu wählen und diese regelmäßig zu sichern.
- Wiki.js speichert persistente Daten (z.B. manche Anhänge & Konfigurationen) in Docker-Volumes.
- Um die vollständige Wiederherstellung sicherzustellen, müssen in einigen Fällen diese Volumes evtl. ebenfalls gesichert werden.


## Volume manuell sichern:
- Meistens heißt das Volume `wiki_data` oder `db-data` (steht ebenfalls in der `docker-compose.yml`)
```
$ sudo docker run --rm -v VOLUME_NAME:/volume -v /PATH/TO/BACKUP:/backup alpine tar czf /backup/wiki_data_backup.tar.gz -C /volume .
```
```
- VOLUME_NAME: Der Name des Docker-Volumes, das gesichert werden soll (kann mit `$ docker volume ls` gefunden werden).
- /PATH/TO/BACKUP: Der Pfad, wo das Backup gespeichert werden soll (Pfad sollte bereits existieren).
```


## Volumen-Wiederherstellung (tar.gz)
```
$ cd wikijs
$ sudo docker-compose down
```

- `VOLUME_NAME` prüfen: vorhandenen Docker-Volumes einsehen
```
$ sudo docker volume ls
```

### Volume wiederherstellen
- Extrahieren des tar.gz-Backups in das Verzeichnis des Volumens
```
$ sudo tar xzf /BACKUP/PFAD/wiki_data_backup.tar.gz -C /var/lib/docker/volumes/VOLUME_NAME/_data
```
Beispiel
```
$ sudo tar xzf /home/user/wikijs-backups/wiki_data_backup.tar.gz -C /var/lib/docker/volumes/wikijs_db-data/_data
```
```
- /BACKUP/PFAD/: Pfad, wo das Backup liegt
- VOLUME_NAME: kann mit dem Befehl `$ sudo docker volume ls` geprüft werden
```


- Container starten
```
$ sudo docker-compose up -d
```
- Browser öffnen und ggf. den Browser-Cache leeren
- testen, ob alles wiederhergestellt wurde


-------------------------------------------------------------------------------------------------------------
