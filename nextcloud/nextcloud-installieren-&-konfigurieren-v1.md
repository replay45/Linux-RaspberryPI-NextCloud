# [Nextcloud](https://nextcloud.com/de/) installieren


# Was ist [Nextcloud](https://nextcloud.com/de/) ?

Nextcloud ist eine Cloud, die man einfach und kostenlos selber im Heimnetz betreiben kann oder kostengünstig bei einem Hosting-Anbieter hosten kann.
Die Nextcloud ist open-Source und da man diese selbst sehr einfach betreiben kann, ist sie datenschutzfreundlich und man behält selber die Kontrolle über seine Daten.

-----------------------------------------------------------------------------------------------

# WICHTIG: Bei der folgenden Installationsmethode, darf kein Webserver bereits installiert sein, da dies zu Konflikten führen könnte und man die Cloud separat in den Webserver einbinden müsste.
# Daher sollte vorab geprüft werden, ob bereits ein Webserver installiert ist. Wenn ja, sollte dieser entfernt werden.


# 1. Domain & IP-Adresse

- Dem Server im Router eine feste IP-Adresse zuweisen.
- Wenn man eine Domain nutzen möchte, muss diese bereits vorhanden sein und im Router hinterlegt werden.
- Wer keine Domain hat, kann, sofern der Server im Heimnetz steht, problemlos im Heimnetz mit der IP-Adresse auf die Cloud zugreifen.
- Wenn die Cloud auch über das Internet erreichbar sein soll, sollte man ein gültiges Zertifikat einbinden, um die verschlüsselte `https`-Verbindung nutzen zu können.
- Für die Verwendung im Heimnetz ist keine verschlüsselte Verbindung über `https` notwendig.
- Alternativ kann auch über eine VPN-Verbindung zum Router im eigenen Heimnetz genutzt werden, um von außerhalb auf das Heimnetz und um auf die Nextcloud zugreifen zu können.



## 2. Updates

```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```


# 3. Nextcloud über den Paketmanager [snap](https://wiki.ubuntuusers.de/snap/) installieren

```
$ sudo apt install snapd
$ sudo apt update
$ sudo snap install nextcloud
```


# 4. Firewall

- Wenn eine Firewall genutzt wird müssen ggf. Portfreigaben getätigt werden.
- Dies kann mit dem `Firewallmanager` [ufw](https://wiki.ubuntuusers.de/ufw/) vorgenommen werden.

```
$ sudo ufw status
$ sudo ufw allow 80
$ sudo ufw allow 443
$ sudo ufw enable
```

- Um ggf. SSH nutzen zu können:
```
$ sudo ufw allow 22
```


# 5. Trusted Domains - config.php bearbeiten

```
$ sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php
```

- Unter den Trusted Domains muss die `IP Adresse des Servers` eingetragen werden. 
- Also wenn der Server auf dem die Nextcloud läuft, die Adresse 192.168.1.2 hätte, müsste diese unter trusted Domains eingefügt werden.
- Um die Cloud auch vom Server selber aufrufen zu können, ist es hilfreich `localhost` ebenfalls unter trusted Domains in der Liste zu belassen.

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

Mit `STRG+X` speichern & verlassen.



# 6. Nextcloud aufrufen

- Nun kann die Nextcloud über die IP-Adresse oder falls vorhanden die Domain aufgerufen werden.
- Dafür im Webbrowser oder der mobilen Handy App `http://IP-Adresse` oder `http://Domain` einfügen.



# 7. Verbindungsprobleme / Nextcloud nicht erreichbar

- Firewall-Einstellungen überprüft ? - Sind alle nötigen Ports geöffnet ?

- Über ein beliebiges Terminal kann der `ping` Befehl verwendet werden, um zu prüfen, ob der Server erreichbar ist.
	- `ping IP-Adresse`

- Ist in der `config.php` die Adresse des Servers als `Trusted Domain` eingetragen und sind dort keine Schreibfehler ?


-----------------------------------------------------------------------------------------------


# 8. Nextcloud Updates installieren

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


# 9. Telefonregion festlegen

```
$ sudo nano /var/snap/nextcloud/current/nextcloud/config/config.php
```

- folgendes ganz unten, in die vorletzte Zeile einfügen:

```
'default_phone_region' => 'DE',
```

Mit `STRG+X` speichern & verlassen.


-----------------------------------------------------------------------------------------------
