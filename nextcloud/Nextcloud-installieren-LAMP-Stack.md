# [Nextcloud](https://nextcloud.com/de/) über LAMP-Stack installieren & konfigurieren

`Anleitung erstellt am 21.6.2026`

`Nextcloud erfolgreich auf Ubuntu 24.04 installiert`


## Inhaltsverzeichnis
1. Was ist die [Nextcloud](https://nextcloud.com/de/) ?
2. interne Domain (DNS-Eintrag) / IP-Adresse
3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
4. Nextcloud installieren - LAMP-Stack
    - Apache2 installieren
    - PHP installieren
    - MySQL (MariaDB) - Database
    - Nextcloud installieren
5. Datenverzeichnis erstellen & Berechtigungen setzen
6. Apache2-Webserver konfigurieren
7. SSL-via IP mit Apache2 (inkl. selbstsigniertem IP-SSL-Zertifikat mit CA)
8. Ersteinrichtung vornehmen
9. CA-Zertifikat auf Clients installieren
10. Nextcloud absichern & konfigurieren


-----------------------------------------------------------------------------------------------


# 1. Was ist die [Nextcloud](https://nextcloud.com/de/) ?
- Die Nextcloud ist eine "Cloudlösung", die man einfach und `kostenlos` selber `im Heimnetz betreiben` oder kostengünstig bei einem Hosting-Anbieter hosten kann.
- Die Nextcloud ist [Open Source](https://de.wikipedia.org/wiki/Open_Source), daher ist sie `datenschutzfreundlich` und man behält selber die `Kontrolle über seine Daten`.


- Vorteile der Nextcloud
    - [Open Source](https://de.wikipedia.org/wiki/Open_Source) & datenschutzfreundlich
    - kostengünstig
    - Umfangreich, viele Erweiterungen & Personalisierungsmöglichkeiten
    - Nutzung für mehrere User möglich
    - Implementierung von Verschlüsselung sehr einfach
    - Ideal für Backups von Smartphones & PCs


-----------------------------------------------------------------------------------------------


# 2. interne Domain (DNS-Eintrag) / IP-Adresse
- Diese Anleitung geht von der Verwendung der Nextcloud über die lokale IP-Adresse aus.
- Die Einrichtung einer Domain wird nicht weiter beschrieben.

### interner DNS-Eintrag
- Wenn man einen internen DNS-Eintrag verwendet, dann sollte man unbedingt folgendes beachten:
    - Typischerweise interpretieren Browser Einträge wie ".lan", ".home", ".intern" als Suchbegriff. Das kann man ausgleichen, indem man das Protokoll, z.B. `https://nextcloud.lan` davor schreibt, jedoch ist das eine unbequeme Lösung.
    - Außerdem sollte man auf `KEINEN FALL` ".local" verwenden, da hier Probleme auftreten können!
    - Stattdessen sollte man auf `offiziell reservierte Test-TLDs` setzen, das wären z.B. `.example` oder `.test`.


# 3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
- Dem Server eine statische [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) vergeben oder im [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) eine IP-Adresse "reservieren".
- Ubuntu-Server installieren.
- Installation beenden.
- Auf Ubuntu-Server einloggen / [SSH](https://de.wikipedia.org/wiki/Secure_Shell)-Verbindung herstellen.
- `$ ssh user@IP-Adresse`


### Betriebssystem-Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt full-upgrade
$ sudo apt autoremove --purge && sudo apt autoclean
```
- [Snap](https://snapcraft.io/)-Paketmanager:
```
$ sudo snap refresh
```


### Auf Server einloggen & [SSH](https://de.wikipedia.org/wiki/Secure_Shell) aktivieren, um remote auf den Server zugreifen zu können
- SSH aktivieren (und in Autostart)
```
$ sudo systemctl start ssh
$ sudo systemctl enable ssh
$ sudo systemctl status ssh
```

- in der [Firewall](https://de.wikipedia.org/wiki/Firewall) SSH erlauben - [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
```
$ sudo ufw allow ssh
$ sudo ufw status
```

- Firewall ggf. aktivieren
```
$ sudo ufw enable
```


### Netzwerkeinstellungen/ Dateimanagement
- [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) von [Ubuntu-Server](https://ubuntu.com/download/server) herausfinden
```
$ ip a
```

- Optional mit `ifconfig` (muss aber zusätzlich installiert werden):
```
$ sudo apt install net-tools
$ ifconfig
```

- Das beste Dateimanagement im Terminal mit dem [Midnight Commander](https://midnight-commander.org/)
```
$ sudo apt install mc
$ sudo mc
```


-------------------------------------------------------------------------------------------------------------


# 4. Nextcloud installieren - LAMP-Stack


### Apache2 installieren
- Apache2 installieren
```
$ sudo apt install apache2
```

### PHP installieren
- Da PHP in `Ubuntu 24.04` direkt in den offiziellen Rpositiories enthalten ist, kann PHP direkt installiert werden, ohne ein Repository hinzufügen zu müssen.
    - Für `Ubuntu 22.04` müsste das Repository manuell hinzugefügt werden, bevor folgende Module hinzugefügt werden.
```
$ sudo apt install php8.3 libapache2-mod-php8.3 php8.3-zip php8.3-xml php8.3-mbstring php8.3-gd php8.3-curl php8.3-imagick libmagickcore-6.q16-6-extra php8.3-intl php8.3-bcmath php8.3-gmp php8.3-cli php8.3-mysql php8.3-zip php8.3-gd  php8.3-mbstring php8.3-curl php8.3-xml php-pear unzip nano php8.3-apcu redis-server ufw php8.3-redis php8.3-smbclient php8.3-ldap php8.3-bz2 php8.3-sqlite3
$ sudo apt update && sudo apt upgrade
```

- RAM kapazität prüfen
```
$ free -m
```

- PHP-Installation konfigurieren
```
$ sudo nano /etc/php/8.3/apache2/php.ini
```

- Mit `STRG+W` nach folgenden Zeilen suchen:
    - `memory_limit =`: hier die RAM-Kapazität für PHP festlegen, NICHT das Maximum angeben, andere Dienste benötigen evtl. auch noch RAM, von Nextcloud empfohlen sinf `512M`, für Power-Nutzer oder größere Instatnzen evtl. auch `1024M`)
    - `upload_max_filesize =`: Max Größe der Dateien, die hochgeladen werden dürfen (z.B. 20G)
    - `post_max_size =`: gleichen Wert wie bei "upload_max_filesize" angeben
    - `date.timezone =`: z.B. Europe/Berlin
    - `output_buffering =`: Wert `off` angeben
    - `opcache.enable=`: Zeile aktivieren, durch entfernen des Semikolons
    - `opcache.enable_cli=`: Zeile aktivieren, Wert: `1`
    - `opcache.interned_strings_buffer=`: Zeile aktivieren, auf Wert `64` setzen (WICHTIG)
    - `opcache.max_accelerated_files=10000`: Zeile aktivieren, durch entfernen des Semikolons
    - `opcache.memory_consumption=`: Zeile aktivieren, ggf. auf `1024` (1G) setzen
    - `opcache.save_comments=1`: Zeile aktivieren, durch entfernen des Semikolons
    - `opcache.revalidate_freq=`: Zeile aktivieren, auf `1` setzen
- Mit `STRG+X` speichern und verlassen


### MySQL (MariaDB) - Database
```
$ sudo apt install mariadb-server
```
- Konfiguration:
```
$ sudo mysql_secure_installation
```
- Konfigurationsdialog:
    - Da noch kein Root-Passwort vorhanden ist, `Enter`
    - Switch to unix_socket authentication: `n`
    - Change the root password: `y`
    - Passwort erstellen, am besten in Passwortmanager speichern und eingeben
    - remove anonymus users: `Enter`
    - Disallow root login remotly: `Enter`

- open SQL dialoge
```
$ sudo mysql
```

- In der Datenbank CLI:
```
> CREATE DATABASE nextcloud;
```
- Benutzer mit Passwort erstellen:
    - neues Passwort erstellen und Platzhalter `password_here` ersetzen
    - Passwort am besten in Passwortmanager speichern
```
> CREATE USER 'nextcloudDBadmin'@'localhost' IDENTIFIED BY 'password_here';
```
- Berechtigungen setzen
```
> GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloudDBadmin'@'localhost';
```
```
> FLUSH PRIVILEGES;
> EXIT;
```

### Nextcloud installieren
- Herunterladen & entpacken
```
$ cd /tmp && wget https://download.nextcloud.com/server/releases/latest.zip
$ unzip latest.zip
$ ls
$ sudo mv nextcloud /var/www/
```


-------------------------------------------------------------------------------------------------------------


# 5. Datenverzeichnis erstellen & Berechtigungen setzen

### Wenn ein Datenverzeichnis bereits angelegt wurde
- Wenn ein Datenverzeichnis bereits angelegt wurde, z.B. ein Festplatten "Array" (ggf. mit RAID), welches über `/mnt` eingehängt wird, wie ein der Anleitung [RAID](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Linux-Server/RAID.md) erklärt, dann den entsprechenden Pfad angeben, z.B. `/mnt/RAID-Array1/Nextcloud`
- Zusätzlich noch die Berechtigungen setzen:
```
$ ls /mnt
$ sudo mkdir /mnt/RAID-Array-Data1-2/nextcloud_data
```
```
$ sudo chown -R www-data:www-data /mnt/RAID-Array1/nextcloud_data
$ sudo chown -R www-data:www-data /var/www/nextcloud/
$ sudo chmod -R 755 /var/www/nextcloud/
```


### neues Datenverzeichnis erstellen
- Alternativ neues Datenverzeichnis erstellen & Berechtigungen setzen:
```
$ sudo mkdir /home/nextcloud_data/
```
```
ls /home
$ sudo chown -R www-data:www-data /home/nextcloud_data/
$ sudo chown -R www-data:www-data /var/www/nextcloud/
$ sudo chmod -R 755 /var/www/nextcloud/
```


-------------------------------------------------------------------------------------------------------------


# 6. Apache2-Webserver konfigurieren

### VirtualHost-Konfiguration
- V-Host anlegen, um mehrere Webseiten auf einem Server zu ermöglichen
```
$ sudo nano /etc/apache2/sites-available/nextcloud.conf
```
- Konfiguration einfügen
    - In der Zeile `ServerName` muss zunächst die lokale IP-Adresse des Servers für die direkte Nutzung über die IP-Adresse eingetragen werden.
    - Wenn eine Domain vorhanden wäre, müsste diese hier eingefügt werden.
```
<VirtualHost *:80>
     DocumentRoot /var/www/nextcloud/
     ServerName IP-ADRESSE

     <Directory /var/www/nextcloud/>
        Options +FollowSymlinks
        AllowOverride All
        Require all granted
          <IfModule mod_dav.c>
            Dav off
          </IfModule>
        SetEnv HOME /var/www/nextcloud
        SetEnv HTTP_HOME /var/www/nextcloud
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
     CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined

</VirtualHost>
```

- Konfig mit der Befehlskette anwenden und Apache-Module konfigurieren
```
$ sudo a2ensite nextcloud.conf
$ sudo a2enmod rewrite
$ sudo a2enmod headers
$ sudo a2enmod env
$ sudo a2enmod dir
$ sudo a2enmod mime
```
- Apache2 neu starten
```
$ sudo service apache2 restart
$ sudo systemctl status apache2
```

- Syntax überprüfen (optional)
```
$ sudo apache2ctl configtest
```

- Fehler/Apache startet nicht
    - Falls es zu Fehlern kommt, prüfen, ob Port 80 bereits belegt ist.
    - Wenn ein anderer Webserver installiert ist, ggf. das Paket erstmal entfernen.

- Zugriff testen (optional)
    - Testen, ob Apache-Seite im Browser geöffnet werden kann.
    - Dafür ggf. noch Port 80 erlauben: `$ sudo ufw allow http`
    - Im Browser aufrufen: `http://IP-ADRESSE` (es müsste sich die Nextcloud-Ersteinrichtungsseite öffnen)


# 7. SSL-via IP mit Apache2 (inkl. selbstsigniertem IP-SSL-Zertifikat mit CA)
- Das Upgrade von HTTP zu HTTPS funktioniert nur, wenn Port 80 offen ist.
```
$ sudo ufw allow http
```

- Alternativ kann man aber auch die Regel entfernen, dann muss man aber immer httops://IP-ADRESSE nutzen.
```
$ sudo ufw status numbered
$ sudo ufw delete NUMMER
```

- SSL-Modul aktivieren
```
$ sudo a2enmod ssl
$ sudo systemctl restart apache2
```

### selbstsigniertes Zertifikat erstellen
- Ordner erstellen
```
$ sudo mkdir /etc/ssl/Nextcloud-Zertifikate
$ cd /etc/ssl/Nextcloud-Zertifikate
```
- San-Konfig erstellen
```
$ sudo nano san.cnf
```
- einfügen & (DNS-Namen), IP-Adresse und Informationen anpassen
    - Besonders der Platzhalter `IP-ADRESSE` muss angepasst werden.
    - Die Punkte `stateOrProvinceName, localityName, organizationName`, wie auch `DNS.1   = Nextcloud.example` sind `optional` und können vollständig aus der Konfig gelöscht werden.
    - Mit `STRG+X` speichern und verlassen

```
[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name
req_extensions     = req_ext
x509_extensions    = v3_req
prompt             = no


[ req_distinguished_name ]
countryName            = DE
stateOrProvinceName    = DeinBundesland
localityName           = DeineStadt
organizationName       = DeineFirma
commonName             = Beschreibung

[ req_ext ]
subjectAltName = @alt_names


[ alt_names ]
DNS.1   = Nextcloud.example
IP.1    = IP-ADRESSE


[ v3_req ]
subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + Nextcloud-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit


- NextcloudCA erstellen (Client-Zertifikat):
    - Den Platzhalter "ORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `NextcloudCA.crt` und `NextcloudCA.key` erstellt wurden.
```
$ sudo openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout NextcloudCA.key -out NextcloudCA.crt -subj "/C=DE/CN=ORGANISATION-Nextcloud-CA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `NextcloudCA.srl, Nextcloud.crt, Nextcloud.csr, NextcloudCA.key` erstellt wurden.
```
$ sudo openssl req -new -nodes -keyout Nextcloud.key -out Nextcloud.csr -config san.cnf
```
```
$ sudo openssl x509 -req -in Nextcloud.csr -CA NextcloudCA.crt -CAkey NextcloudCA.key -CAcreateserial -out Nextcloud.crt -days 3650 -extensions v3_req -extfile san.cnf
```

### VirtualHost-Konfiguration für SSL via IP anpasssen
- V-Host anlegen, um mehrere Webseiten auf einem Server zu ermöglichen
```
$ sudo nano /etc/apache2/sites-available/nextcloud.conf
```
- vorhandene Konfiguration durch folgende Konfig ersetzen:
    - In der Zeile `ServerName` muss die lokale IP-Adresse des Servers für die direkte Nutzung über die IP-Adresse eingetragen sein.
    - Mit `STRG+X` speichern und verlassen
```
<VirtualHost *:80>
    ServerName IP-ADRESSE

    RewriteEngine On
    RewriteRule ^ https://IP-ADRESSE%{REQUEST_URI} [L,R=301]
</VirtualHost>

<VirtualHost *:443>

     DocumentRoot /var/www/nextcloud/
     ServerName IP-ADRESSE

     SSLEngine on
     SSLCertificateFile /etc/ssl/Nextcloud-Zertifikate/Nextcloud.crt
     SSLCertificateKeyFile /etc/ssl/Nextcloud-Zertifikate/Nextcloud.key

     <Directory /var/www/nextcloud/>

        Options +FollowSymlinks
        AllowOverride All
        Require all granted
          <IfModule mod_dav.c>
            Dav off

          </IfModule>
        SetEnv HOME /var/www/nextcloud
        SetEnv HTTP_HOME /var/www/nextcloud
     </Directory>

     Header always set Strict-Transport-Security "max-age=15552000; includeSubDomains"

     ErrorLog ${APACHE_LOG_DIR}/nextcloud_error.log
     CustomLog ${APACHE_LOG_DIR}/nextcloud_access.log combined

</VirtualHost>
```

- Webseite aktivieren & default Apache-Seite deaktivieren
```
$ sudo a2ensite nextcloud.conf
$ sudo a2dissite 000-default.conf
```

- Apache2 neu starten
```
$ sudo a2enmod headers rewrite ssl
$ sudo systemctl restart apache2
$ sudo systemctl status apache2
```

- Syntax überprüfen (optional)
```
$ sudo apache2ctl configtest
```

- Konfigs überprüfen:
```
$ sudo apache2ctl -S
```

### Upgrade zu HTTPS testen
- Mit Curl
    - Erwartete Ausgabe: `HTTP/1.1 301 Moved Permanently ... Location: https://IP-ADRESSE/`
```
$ curl -I http://192.168.178.21
```

- HTTPS-Seite prüfen (optional):
```
$ curl -vk https://IP-ADRESSE
```


### Nextcloud config.php - Trustet Domains überprüfen
- config.php öffnen
```
$ sudo nano /var/www/nextcloud/config/config.php
```
- Unter `trusted_domains` IP-ADRESSE(N) überprüfen & ggf. DNS-Eintrag, der als Domain verwendet wird, ergänzen.


-------------------------------------------------------------------------------------------------------------


# 8. Ersteinrichtung vornehmen

### Ersteinrichtung
- IP-Adresse im Browser aufrufen
- Admin-Benutzer anlegen
    - Benutzername und Passwort festlegen
- Storage & database
    - Hier sollte der `Pfad angegeben werden`, der zuvor unter `Punkt 5. "Datenverzeichnis" erstellt wurde`.
    - Es wird dringed empfohlen einen separaten Datenordner anzugeben und nicht den Standardpfad zu verwenden.
- Datenbank:
    - `MySQL/MariaDB` anwählen
    - Datenbank Benutzername und Passwort eingeben (vom nextcloudDBadmin).
    - Datenbanknamen angeben: `nextcloud`
    - Ersteinrichtung abschließen (kann einige Zeit in Anspruch nehmen)

- Nun sollte die Ersteinrichtung abgeschlossen sein und es können weitere Benutzerkonten angelegt und Einstellungen vorgenommen werden.


-------------------------------------------------------------------------------------------------------------


# 9. CA-Zertifikat auf Clients installieren

## Zertifikat von Server mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) herunterladen


### Windows:
- Für Windows gibt es das Tool [WinSCP](https://winscp.net/eng/index.php).

### Linux (Zertifikat über SCP kopieren)
- Befehle auf Server ausführen:
```
$ ssh user@IP-Adresse
$ sudo cp /etc/ssl/Nextcloud-Zertifikate/NextcloudCA.crt /home/USERNAME/
$ ls
$ sudo chmod 644 /home/USERNAME/NextcloudCA.crt
```
- Befehle auf Client ausführen:
```
$ sudo scp user@IP-ADRESSE:/home/USERNAME/NextcloudCA.crt /dein/lokales/verzeichnis/zielordner
```
- temporär-kopiertes Zertifikat wieder löschen (Befehle auf Server ausführen)
```
$ sudo rm -rf /home/USERNAME/NextcloudCA.crt
```

## CA-Zertifikat in Browser als vertrauenswürdig einstufen
- Die `Chrome basierten Browser` nutzen den `Zertifikatsspeicher des Betriebssystems`.
- Der `Firefox` hingegen nutzt per default nur den `eigenen Zertifikatsspeicher`.

## In einer [Active Directory](https://de.wikipedia.org/wiki/Active_Directory) Umgebung, mit GPO-Richtlinie Zertifikate an Clients automatisch verteilen
- Wenn eine Active Directory (Domäne) genutzt wird, kann eine Gruppenrichtlinie (GPO) erstellt werden, um das CA-Zertifikat automatisch an alle Clients zu verteilen.
- Dabei muss ggf. das Zertifikat in den Zertifikatsspeicher des Betriebssystems für die Chrome-basierten Browser und ggf. separat in den Firefox-Zertifikatsspeicher verteilt werden, wobei es auch möglich ist, Firefox über die GPO so zu konfigurieren, dass dieser den Zertifikatsspeicher des Betriebssystems verwendet.


-------------------------------------------------------------------------------------------------------------


# 10. Nextcloud absichern & konfigurieren

- Wie man einen Linux-Server absichert, wird unter [Linux-Server-absichern](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Linux-Server/Linux-Server-absichern.md) erklärt.

- Mehr zum Thema "Verschlüsselung" folgt in Kürze

- Wie man die Nextcloud weiter konfigurieren und absichern kann, folgt in Kürze


-------------------------------------------------------------------------------------------------------------
