# [Nextcloud](https://nextcloud.com/de/) installieren & konfigurieren


`Anleitung erstellt am 10.7.2024`

`Nextcloud erfolgreich auf Ubuntu 22.04 installiert`

`Die Installationsmethode aus dieser Anleitung eignet sich für die private Nutzung der Nextcloud im Heimnetz.`


## Inhaltsverzeichnis
1. Was ist [Nextcloud](https://nextcloud.com/de/) ?
2. Domain & IP-Adresse
3. ggf. Linux installieren & Updates ausführen
4. Nextcloud über den Paketmanager [snap](https://wiki.ubuntuusers.de/snap/) installieren
5. [Firewall](https://de.wikipedia.org/wiki/Firewall) & SSH
6. Trusted Domains - config.php bearbeiten
7. Nextcloud aufrufen
8. Verbindungsprobleme / Nextcloud nicht erreichbar
9. Nextcloud Updates installieren
10. Telefonregion festlegen
11. Als Admin über das Terminal, einem Benutzer eine Benachrichtigung schicken
12. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure)-Verschlüsselung einrichten (ohne Domain) - optional


-----------------------------------------------------------------------------------------------


# 1. Was ist die [Nextcloud](https://nextcloud.com/de/) ?
- Die Nextcloud ist eine "Cloud", die man einfach und `kostenlos` selber `im Heimnetz` betreiben oder kostengünstig bei einem Hosting-Anbieter hosten kann.
- Die Nextcloud ist [Open Source](https://de.wikipedia.org/wiki/Open_Source), daher ist sie datenschutzfreundlich und man behält selber die Kontrolle über seine Daten.


- Vorteile der Nextcloud
    - [Open Source](https://de.wikipedia.org/wiki/Open_Source) & datenschutzfreundlich
    - kostengünstig
    - Umfangreich, viele Erweiterungen & Personalisierungsmöglichkeiten
    - Nutzung für mehrere User möglich
    - Implementierung von Verschlüsselung sehr einfach
    - Ideal für Backups von Smartphones & PCs


-----------------------------------------------------------------------------------------------


# WICHTIG: Bei der folgenden Installationsmethode darf bereits kein Webserver installiert sein, da dies zu Konflikten führen könnte und man die Cloud separat in den Webserver einbinden müsste.
# Daher sollte vorab geprüft werden, ob bereits ein Webserver installiert ist. Wenn ja, sollte dieser entfernt werden.


# 2. Domain & IP-Adresse
- Dem Server (auf dem die Nextcloud läuft) im Router eine feste [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) zuweisen.
- Wenn man eine Domain nutzen möchte, muss diese bereits vorhanden sein und im Router hinterlegt werden.
- Wer keine Domain hat, kann, sofern der Server im Heimnetz steht, problemlos im `Heimnetz` mit der [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) auf die Cloud zugreifen.
- Wenn die Cloud auch `über das Internet erreichbar` sein soll, sollte man ein gültiges Zertifikat einbinden, um die `verschlüsselte HTTPS-Verbindung` nutzen zu können.
- Für die Verwendung im Heimnetz ist keine verschlüsselte Verbindung über HTTPS notwendig.
- Alternativ kann auch über eine VPN-Verbindung zum Router im eigenen Heimnetz genutzt werden, um von außerhalb auf das Heimnetz und die Nextcloud zugreifen zu können.



# 3. ggf. Linux installieren & Updates ausführen
- Installation einer Linux-Distribution
    - Grundsätzlich kann eine beliebige Debian-basierte Linux-Distribution, wahlweise mit oder auch ohne grafischer-Oberfläche gewählt werden.
    - Für diese Anleitung und für Anfänger empfiehlt sich die aktuellste Ubuntu-LTS-Version oder Ubuntu-Server-LTS-Version.
    - Auf einer anderen Distribution müsste ggf. der Snap-Paketmanger installiert werden, um die Nextcloud mit dieser Methode zu installieren.
    - Alternativ kann die Nextcloud auch über andere Paketmanger installiert oder über [Docker](https://de.wikipedia.org/wiki/Docker_(Software)) betrieben werden.


- Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```


# 4. Nextcloud über den Paketmanager [snap](https://wiki.ubuntuusers.de/snap/) installieren
```
$ sudo apt install snapd
$ snap refresh
$ sudo apt update
$ sudo snap install nextcloud
```


# 5. [Firewall](https://de.wikipedia.org/wiki/Firewall) & SSH
- Wenn eine Firewall genutzt wird müssen ggf. Portfreigaben getätigt werden.
- Dies kann mit dem `Firewall Manager` [ufw](https://wiki.ubuntuusers.de/ufw/) vorgenommen werden.
- Für unverschlüsselte [HTTP](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol)-Verbindung
```
$ sudo ufw status
$ sudo ufw allow 80
$ sudo ufw enable
```

- Für verschlüsselte Verbindung über [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure)
```
$ sudo ufw status
$ sudo ufw allow 443
$ sudo ufw enable
```

- Um SSH nutzen zu können:
```
$ sudo ufw status
$ sudo ufw allow 22
```

- [SSH](https://de.wikipedia.org/wiki/Secure_Shell) aktivieren
```
$ systemctl start ssh
$ systemctl enable ssh
$ systemctl status ssh
```


# 6. Trusted Domains - config.php bearbeiten
```
$ sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php
```

- Unter den Trusted Domains muss die `IP Adresse des Servers` eingetragen werden. 
- Also wenn der Server auf dem die Nextcloud läuft, die [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) 192.168.1.2 hätte, müsste diese unter trusted Domains eingefügt werden.
- Um die Cloud auch vom Server selber aufrufen zu können, ist es hilfreich, `localhost` ebenfalls unter trusted Domains in der Liste zu belassen.
- Wenn eine `Domain` verwendet wird, muss diese ebenfalls in der `Config-Datei` eingefügt werden.

```
'trusted_domains' => 
  array (
    0 => 'localhost',
    1 -> '127.0.0.1',
    2 => 'your_server_ip', // hier die IP-Adresse einfügen
    3 => 'your.server.com', // hier ggf. die Domain einfügen
  ),

```
- Mit `STRG+X` speichern & verlassen.



# 7. Nextcloud aufrufen
- Nun kann die Nextcloud über die IP-Adresse oder falls vorhanden, die Domain aufgerufen werden.
- Dafür im Browser oder der mobilen Handy App `http://IP-Adresse` oder `https://Domain` einfügen.
- Admin-Account erstellen und Installationsassistenten folgen.


# 8. Verbindungsprobleme / Nextcloud nicht erreichbar
- Firewall-Einstellungen überprüft ? - Sind alle nötigen Ports geöffnet ?
- Über ein beliebiges Terminal kann der `ping`-Befehl verwendet werden, um zu prüfen, ob der Server erreichbar ist.
	- `ping IP-Adresse`
- Ist in der `config.php` die Adresse des Servers als `Trusted Domain` eingetragen und sind dort keine Schreibfehler ?


-----------------------------------------------------------------------------------------------


# 9. Nextcloud Updates installieren
- Bevor Updates installiert werden, sollten immer Backups erstellt werden !
- Updates unter `Verwaltung` - `Übersicht` herunterladen, wenn verfügbar
```
$ ssh user@IP-Adresse
$ cd /var/snap/nextcloud
$ sudo nextcloud.occ upgrade
```

- Snap Pakete aktualisieren
```
$ sudo snap refresh 
```

- Linux Updates installieren
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- Informationen über die aktuelle Version
```
$ sudo snap info nextcloud
```

-----------------------------------------------------------------------------------------------


# 10. Telefonregion festlegen
```
$ sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php
```

- folgendes ganz unten, in die vorletzte Zeile einfügen:
```
'default_phone_region' => 'DE',
```
- Mit `STRG+X` speichern & verlassen.


-----------------------------------------------------------------------------------------------


# 11. Als Admin über das Terminal, einem Benutzer eine Benachrichtigung schicken
- Terminal lokal öffnen oder via [SSH](https://de.wikipedia.org/wiki/Secure_Shell) verbinden und Befehl eingeben:
```
$ sudo nextcloud.occ notification:generate -l "deine Nachricht hier einfügen" "username" "Betreff"
```
- Wenn keine Meldung im Terminal erscheint, wurde die Benachrichtigung verschickt.


-----------------------------------------------------------------------------------------------


# 12. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure)-Verschlüsselung einrichten (ohne Domain) - optional
- Eine HTTPS-Verschlüsselung einrichten, um eine verschlüsselte Verbindung über die IP-Adresse zur Nextcloud auch im lokalen Heimnetz herstellen zu können.
- Da die Nextcloud in diesem Fall über den `Snap-Paketmanager installiert wurde`, kann man in der Regel einen von dem Paketmanager integrierten Befehl nutzen.
- Dabei wird auch ein `selbstsigniertes Zertifikat erstellt`.
- Zunächst `mit dem Server verbinden`, auf dem die Nextcloud läuft und Befehl ausführen.
```
$ sudo nextcloud.enable-https self-signed
```
- Nun sollte bereits die HTTPS-Verschlüsselung aktiv sein.
- Das Konfigurieren wird automatisch übernommen, das heißt es ist nicht nötig manuelle Konfigurationen vorzunehmen.
- Der Browser zeigt höchstwahrscheinlich eine `Warnmeldung an, da das Zertifikat selbstsigniert ist`.
- Die `HTTPS-Verschlüsselung ist trotzdem gegeben`.
- Außerdem sollten alle Verbindungen automatisch von HTTP auf HTTPS weitergeleitet werden.
- Das Zertifikat kann sich nicht von selbst erneuern, sollte allerdings meistens lang genug gültig sein.


### Verbindung überprüfen
- Es kann grundsätzlich ein beliebiger Browser verwendet werden, um zu prüfen, ob eine HTTPS-Verbindung möglich ist.
- Zusätzlich kann man die HTTP- & HTTPS-Verbindung mit [curl](https://curl.se/) im Terminal prüfen.
```
$ curl -v https://127.0.0.1
$ curl -v http://127.0.0.1
```


### Zertifika erneuern
```
$ sudo nextcloud.disable-https
$ sudo nextcloud.enable-https self-signed
```

### HTTPS deaktivieren
```
$ sudo nextcloud.disable-https
```


### Warnmeldung im Browser (Verschlüsselung ist trotzdem gegeben !)
- Es ist normal, dass beim Aufrufen der Nextcloud eine Warnmeldung erscheint, da das Zertifikat selbst-signiert ist.
- Das ist allerdings kein Problem, da die HTTPS-Verschlüsselung `trotzdem gegeben ist` !
- Wenn man keine Warmeldung erhalten möchte, muss man das selbst-signierte Zertifikat mit einem weiteren Zertifikat signieren und das Zertifikat, womit signiert wurde, muss dann in den Zertifikatsspeicher des Browser/Betriebsystems geladen werden.


-----------------------------------------------------------------------------------------------
