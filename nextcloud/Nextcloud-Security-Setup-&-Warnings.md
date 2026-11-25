# Nextcloud "Security & Setup Warnings"

`Anleitung erstellt am 26.10.2026`

`Anleitung erstellt/geeignet für LAMP-Stack-Installationsmethode`


-----------------------------------------------------------------------------------------------


# Telefonregion festlegen - "Default phone region"
- SSH auf dem Linux-Server
- Optional vor dem Bearbeiten ein Backup der Konfig-Datei anlegen
```
$ sudo mkdir /srv/nextcloud-konfigs
$ sudo cp /var/www/nextcloud/config/config.php /srv/nextcloud-konfigs/config.php
```
- Konfig Datei öffenen
    - Mit `STRG+X` speichern & verlassen
```
$ sudo nano /var/www/nextcloud/config/config.php
```

- folgendes innerhalb dem Reiter `$CONFIG = array` einfügen:
    - z.B. über der Zeile "`'trusted_domains' =>`"
```
'default_phone_region' => 'DE',
```
- Apache-Web-Server neu starten: 
```
$ sudo systemctl restart apache2
```


-----------------------------------------------------------------------------------------------


# "Maintenance window start (nur Cron-Modus)"
- [docs.nextcloud.com - maintenance-window-start](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/background_jobs_configuration.html#maintenance-window-start)
- `maintenance_window_start` legt fest, ab welcher Nachtstunde Nextcloud seine ressourcenintensiven Tages-Jobs ausführt, damit diese nicht tagsüber Nutzer beeinträchtigen.

- Befehl als `www-data`-Benutzer ausführen (wie angegeben):
    - Der Wert `1` ist in UTC Zeitformat
```
$ cd /var/www/nextcloud
$ sudo -u www-data php occ config:system:set maintenance_window_start --type=integer --value=1
```


-----------------------------------------------------------------------------------------------


# "Mimetype migrations available"
- Man kann den Befehl nutzen den die Meldung vorschlägt.
    - Die Warnung tritt in der Regel nach Nextcloud-Updates auf.
    - Die Nextcloud bearbeitet dabei die Dateityp-Zuordnung, damit Dateien entsprehcend korrekt erkannt werden und passende Icons und Vorschauen angezeigt werden können

- Man sollte jedoch beachten:
    - Auf großen Instanzen kann dies etwas länger dauern.

- Befehl als `www-data`-Benutzer ausführen (wie angegeben):
```
$ sudo -u www-data php /var/www/nextcloud/occ maintenance:repair --include-expensive
```


-----------------------------------------------------------------------------------------------


# "Transactional File Locking" & "Memcache"
- [docs.nextcloud.com - caching_configuration](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/caching_configuration.html)
- Diese beiden Meldungen haben die selbe Ursache.
    - Die Nextcloud cacht ohne Konfiguration alles in der Datenbank, was langsamer ist als über einen Cache.
    - Wichtig: Die Dokumetnation weist darauf hin, dass ein Cache nicht zwingend notwendig.
- Je nachdem wie viele Nutzer die Nextcloud nutzen empfihelt die Dokumentation entsprechende Handlungen, es loht sich einen Blick in die [Dokumetnation](https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/caching_configuration.html#recommendations-based-on-type-of-deployment) zu werfen.


### Um beide Meldungen zu entfernen:
- benötigte Pakete installieren
```
$ sudo apt update
$ sudo apt install php-apcu redis-server php-redis
```

- Webserver neu starten
```
$ sudo systemctl restart apache2
```

- Optional vor dem Bearbeiten ein Backup der Konfig-Datei anlegen
```
$ sudo mkdir /srv/nextcloud-konfigs
$ sudo cp /var/www/nextcloud/config/config.php /srv/nextcloud-konfigs/config.php
```
- Konfig öffenen
    - Mit `STRG+X` speichern & verlassen
```
$ sudo nano config.php
```
- Folgendes innerhalb des `$CONFIG = array (` einfügen
    - WICHTIG: Alle `\` müssen grundsätzlich mit einem Backslash ausgeklammert werden, daher wird wie folgt auch `\\` verwendet.
```
'memcache.local' => '\\OC\\Memcache\\APCu',
'memcache.distributed' => '\\OC\Memcache\\Redis',
'memcache.locking' => '\\OC\\Memcache\\Redis',
'redis' => [
     'host' => 'localhost',
     'port' => 6379,
],
```
- Syntax überprüfen:
    - erwartet wird die Ausgabe: "No syntax errors detected in config.php"
```
$ sudo php -l config.php
```

- Optional Webserver neustarten
```
$ sudo systemctl restart apache2
```


-----------------------------------------------------------------------------------------------


# Configuration server ID 
- Es empfieht sich das Setzen einer ID, sowie für neue Server als auch für Server die bereits in Verwendung sind.
- Falls ein Cluster/Load-Balancing/mehrere PHP-Instanzen geplant sind ist das Setzen der ID ein Muss !
- WARNUNG:
    - Eine Nachträgliche Änderung der ID kann bei Objekte/Dateien die bereits eine ID zugewiesen haben zu Problemen führen, wenn die ID einmal gesetzt ist, sollte sie nicht mehr geändert werden.
- Man sollte außerdem ein Backup der config.php aufbewahren, man kann zusätzlich auch die ID z.B. in einem Passwortmanager unter Notizen abspeichern.


### ID erstellen und zuweisen
- ID erzeugen
```
$ echo $((RANDOM % 512))
```
- Optional vor dem Bearbeiten ein Backup der Konfig-Datei anlegen
```
$ sudo mkdir /srv/nextcloud-konfigs
$ sudo cp /var/www/nextcloud/config/config.php /srv/nextcloud-konfigs/config.php
```
- ID kopieren und in Konfig einfügen
    - Mit `Strg+X` verlassen
```
$ cd /var/www/nextcloud/config/
$ sudo nano config.php
```
- Folgende Zeile in die Konfig hinzufügen und Platzhalter durch eigene ID ersetzen:
    - Die Zeile muss sich innerhalb des `array`-Blocks befinden.
```
'serverid' => 'ID-HIER-EINTRAGEN',
```
- Syntax überprüfen:
    - erwartet wird die Ausgabe: "No syntax errors detected in config.php"
```
$ sudo php -l config.php
```
- Webserver neustarten
```
$ sudo systemctl restart apache2
```
- ID überprüfen
    - Ausgabe sollte ID sein
```
$ sudo -u www-data php /var/www/nextcloud/occ config:system:get serverid
```


-----------------------------------------------------------------------------------------------
