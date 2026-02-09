# Linux-Server

`Anleitung erstellt am 17.8.2025`


## Inhaltsverzeichnis
1. Einleitung, Zweck & Vorteile eines Home-Servers/ eigener Cloudlösung
2. Welche [Linux-Distribution](https://de.wikipedia.org/wiki/Linux-Distribution) für einen Linux-Server wählen
3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
4. [Firewall](https://de.wikipedia.org/wiki/Firewall)
5. [SSH](https://de.wikipedia.org/wiki/Secure_Shell)
6. Verschlüsselung & Sicherheit unter Linux
7. Server-Hardware und Ausfallsicherheit
8. eigene Cloudlösung/ Netzlaufwerk
	- [Nextcloud](https://nextcloud.com/de/)
	- [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)
	- [SMB](https://de.wikipedia.org/wiki/Server_Message_Block)
	- [WebDav](https://de.wikipedia.org/wiki/WebDAV)
9. Anwendungen und Dienste


-----------------------------------------------------------------------------------------------


# 1. Einleitung & Zweck eines Home-Servers/ eigener Cloudlösung
- Einleitung
	- Diese Anleitung soll einen Überblick über das Thema Home-Server/ eigene Cloudlösung geben.
	- Die Anleitung soll vor allem ein Überblick über die Möglichkeiten bei dem Thema geben und ggf. auf konkrete Anleitungen verweisen. 
	- Dabei werden einige grundlegende "Linux-Grundkenntnisse" vorausgesetzt.


- Zweck & Vorteile eines Home-Servers/ eigener Cloudlösung
	- viel günstiger Speicherplatz
	- Performance & schnelle Anbindung im Heimnetz
	- kosteneffizient (keine Abos)
	- Flexibilität, Skalierbarkeit
	- datenschutzfreundlich
	- Kontrolle über eigene Daten
	- Unabhängig von externen Anbietern
	- Langlebigkeit & Nachhaltigkeit 
	- Implementierung vieler Sicherheitsmaßnahmen inkl. Verschlüsselung möglich
	- z.B. durch VPN ins Heimnetz auch von extern sicherer Zugang möglich
	- auch ggf. für mehrere Personen im Haushalt geeignet
	- mit vielen Diensten erweiterbar, z.B. Filesharing ([SMB](https://de.wikipedia.org/wiki/Server_Message_Block), [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol), [WebDav](https://de.wikipedia.org/wiki/WebDAV)), eigene Cloud wie [Nextcloud](https://nextcloud.com/de/), oder [Media-Server](https://de.wikipedia.org/wiki/Streaming-Server), wie Plex, Jellyfin etc.


-----------------------------------------------------------------------------------------------


# 2. Welche Linux-Distribution für einen Linux-Server wählen
- Zunächst mal ist die Wahl der [Linux-Distribution](https://de.wikipedia.org/wiki/Linux-Distribution) eine individuelle Entscheidung und hängt z.B. von den Vorlieben/Erfahrungen des Systemadministrators ab.
- Verschiedene Distributionen haben unterschiedliche Vorteile oder Anwendungsbereiche, allerdings gibt es bei der Wahl der Distribution in der Regel kein "Richtig" oder "Falsch".
- Für einen stabilen Server eignet sich meist eine Distribution ohne grafische Oberfläche, da diese nur unnötige Ressourcen benötigen. Jedoch können Server-Dienste natürlich auch auf Distributionen mit grafischer Oberfläche (GUI) eingerichtet werden.
- Häufig werden gängige Server-Distributionen, wie z.B. [Ubuntu-Server](https://ubuntu.com/download/server), [Debian](https://www.debian.org/index.de.html) oder bei erfahrenen Admins auch [CentOS](https://www.centos.org/), eingesetzt. [Ubuntu-Server](https://ubuntu.com/download/server) ist dabei auch für Anfänger geeignet.


-----------------------------------------------------------------------------------------------


# 3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
- Ubuntu-Server ISO-Datei von der offiziellen Webseite herunterladen.
- Es wird ein USB-Stick mit min. 8GB benötigt.
- Die ISO-Datei auf einen USB-Stick flashen - Unter Windows kann dafür am besten [Rufus](https://rufus.ie/de/) verwendet werden.
- USB-Stick an Server anstecken, Server starten und den Installationsschritten folgen.
- WICHTIG: [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) unter Linux kann NUR bei der Installation des Betriebssystems vorgenommen werden !


### Festplattenverschlüsselung während der Installation
- Unter dem Punkt `Speicherplatzkonfiguration/Partitionierung` das `X` bei `Die LVM-Gruppe mit LUKS verschlüsseln` setzen und eine sichere Passphrase eingeben.
- Die Passphrase kann z.B. in einem Passwortmanager gespeichert werden.
- Man kann auch einen `recovery-key` erstellen. Diesen sollte man ebenfalls sorgfälltig aufbewahren.
    - Der `recovery-key` wird im Verzeichnis `/var/log/installer/recovery-key.txt` gespeichert. Von dort sollte man diesen z.B. in einem Passwortmanager speichern und danach die Datei löschen, um das Risiko zu minimieren, dass unbefgte an diesen "Key" kommen könnten.
    - Sobald der recovery-key extern gesichert wurde, den Befehl verwenden, um die Datei auf dem Server zu löschen: 
    - `$ sudo rm /var/log/installer/recovery-key.txt`
- Erfahrene Linux-Nutzer können die `Benutzerdefinierte Partitionierung` wählen.
- Mehr zu `Verschlüsselung unter Linux` und zur `Verwendung von TPM` in dem Ordner Linux unter [Verschlüsselung auf Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)


### Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove --purge && sudo apt autoclean
```


-----------------------------------------------------------------------------------------------


# 3. [Firewall](https://de.wikipedia.org/wiki/Firewall)
- Mehr zur Firewall
	- Mehr zur [Firewall](https://de.wikipedia.org/wiki/Firewall) unter [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)
	- [Firewall Manager - ufw](https://wiki.ubuntuusers.de/ufw/):

- Installation & Status
```
$ sudo apt install ufw
$ sudo ufw status
```

- Firewall aktivieren/ deaktivieren/ reseten
```
$ sudo ufw enable
$ sudo ufw disable
$ sudo ufw reset
```

- ggf. [SSH](https://de.wikipedia.org/wiki/Secure_Shell) erlauben
```
$ sudo ufw allow ssh
```


-----------------------------------------------------------------------------------------------


# 4. [SSH](https://de.wikipedia.org/wiki/Secure_Shell)

### SSH auf dem Server aktivieren
```
$ sudo systemctl start ssh
$ sudo systemctl enable ssh
$ sudo systemctl status ssh
```


### SSH-Verbindung
- Terminal auf dem Client öffnen um SSH-Verbindung zum Server herzustellen.
	- Befehl sowie unter Linux, MacOS und auch Windows11 nutzbar.
```
$ ssh USER@IP-ADRESSE
```
- Falls keine Verbindung möglich ist, Erreichbarkeit des Servers mit `$ ping IP-Adresse` prüfen, ansonsten prüfen, ob SSH-Dienst auf dem Server aktiv ist.


-----------------------------------------------------------------------------------------------


# 5. Verschlüsselung & Sicherheit unter Linux
- Mehr zu `Sicherheit unter Linux` in dem Ordner Linux unter [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung) zu finden.
- Mehr zu `Verschlüsselung unter Linux` in dem Ordner Linux unter [Verschlüsselung auf Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung).


-----------------------------------------------------------------------------------------------


# 6. Server-Hardware und Ausfallsicherheit
- Um den Server und die Hardware zu schützen und auch die Verfügbarkeit der Daten sicherzustellen, sind hier noch ein paar Maßnahmen beschrieben.

- `physischer Schutz`:
	- Der Server sollte an einem geeigneten Platz stehen, der kühl und trocken ist und an dem die Luftzufuhr gewährleistet werden kann.
	- Außerdem sollte der Server in einer erhöhten Position befestigt werden, sodass der Kontakt mit Wasser vermieden wird, was besonders in Kellern ein wichtiger Punkt ist.
	- Optional können physische Sicherungen vorgenommen werden, um den Server vor unbefugten Zugriffen/Manipulationen zu schützen. Dafür eignen sich z.B. Serverschränke.

- `Schutz vor Überspannung/Stromausfall`:
	- Zunächst kann eine Mehrfachsteckdose mit Überspannungs- und ggf. auch Blitzschutz verwendet werden, um die Hardware vor Spannungsspitzen zu schützen.
	- Einen zusätzlichen Schutz kann auch eine [USV](https://de.wikipedia.org/wiki/Unterbrechungsfreie_Stromversorgung) bieten, sowohl bei Spannungsschwankungen, als auch vor Spannungspitzen.
	- Eine [USV](https://de.wikipedia.org/wiki/Unterbrechungsfreie_Stromversorgung) kann zudem auch bei kompatibler Hardware sicherstellen, dass der Server sich bei einem Stromausfall korrekt herunterfahren kann.

- [RAID](https://de.wikipedia.org/wiki/RAID) - Verfügbarkeit der Daten ([business continuity](https://de.wikipedia.org/wiki/Betriebliches_Kontinuit%C3%A4tsmanagement))
	- RAID-Konfigurationen helfen bei physischen Defekten an Festplatten.
	- Durch die korrekte Wahl der RAID-Konfiguration kann sichergestellt werden, dass die Daten auch erhalten bleiben, wenn eine Festplatte ausfällt.
    - Mehr zu RAID-Konfiguration auf Ubuntu-Server in der Anleitung [RAID einrichten - Ubuntu-Server](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Linux-Server/RAID.md).


-----------------------------------------------------------------------------------------------


# 8. eigene Cloudlösung/ Netzlaufwerk

### [Nextcloud](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/nextcloud) (Selbstgehostete Cloudlösung im Heimnetzwerk)
- Die Nextcloud ist eine [Open Source](https://de.wikipedia.org/wiki/Open_Source)-Cloudlösung, die man einfach und kostengünstig zuhause hosten kann.
- Die Nextcloud ist eine gute und datenschutzfreundliche Alternative zu Dropbox, Google Drive oder OneDrive.
- Mehr zur Nextcloud ist im Ordner [Nextcloud](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/nextcloud) zu finden.

- `Anwendungsfälle & Eigenschaften`
	- Privater Cloud-Speicher für private Haushalte oder Unternehmen
	- Alternative zu Dropbox, Google Drive oder OneDrive
	- Datensicherung und Synchronisation zwischen Geräten
	- Webinterface + Clients für Linux, Windows, MacOS, Android und iOS
	- Unterstützt Ende-zu-Ende-Verschlüsselung
	
- `Vorteile`
	- datenschutzfreundlich
	- Viele Funktionen über Plugins erweiterbar
	- Automatische Synchronisation auf mehreren Geräten

- `Nachteile`
	- Performance stark vom Server abhängig
	- Kann für sehr große Dateien langsamer sein als SMB oder SFTP
	- administrieren kann aufwendiger sein als eine einfache SMB-Netzfreigabe


### [WebDav](https://de.wikipedia.org/wiki/WebDAV) (als Netzlaufwerk)
- WebDAV arbeitet mit dem [HTTP](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol)-Protokoll und kann z.B. als Netzlaufwerk bei Linux, MacOS und Windows eingehängt werden.
	
- `Anwendungsfälle & Eigenschaften`
	- ideal für NAS-Systeme und für das Heimnetz
	- Durch TLS ([HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure)) ist auch Verschlüsselung möglich
	- Netzlaufwerk für gängige Desktop-Betriebssysteme
	- Über WebDAV kann z.B. auch eine Nextcloud als Netzlaufwerk eingehängt werden
	
- `Vorteile`
	- Auch Webzugriff über den Browser ist möglich.
	- Native Unterstützung in Windows, MacOS und Linux
	- Integration auch in Android/iOS möglich
	
- `Nachteile`
	- langsamer als SMB
	- Performance stark von Serverleistung abhängig
	- standardmäßig nur unverschlüsseltes HTTP, HTTP-over-TLS (HTTPS) muss erst eingerichtet werden


### [SMB](https://de.wikipedia.org/wiki/Server_Message_Block) (Server Message Block)
- SMB ist ein sehr weit verbreitetes Protokoll im Server-Bereich und wird von allen gängigen Systemen unterstützt.
- Standardmäßig ist SMB unverschlüsselt, bietet dafür jedoch eine sehr gute Performance.
	
- `Anwendungsfälle & Eigenschaften`
	- ideal für NAS-Systeme und für das Heimnetz
	- Netzlaufwerke in Unternehmen
	- Unterstützt Benutzerrechte und Zugriffssteuerung
	- verschlüsselte Dateiübertragung erst ab Version 3.1.1
	
- `Vorteile`
	- In Linux, Windows und MacOS integriert
	- Schnelle Dateiübertragung im lokalen Netzwerk

- `Nachteile`
	- Unsicher über das Internet, hier VPN unbedingt erforderlich
	- Verschlüsselungsoptionen erst ab Version 3.1.1 verfügbar
	- Sicherheit extrem von Passwortstärke abhängig


### [FTP](https://de.wikipedia.org/wiki/File_Transfer_Protocol) / [FTPS](https://de.wikipedia.org/wiki/FTP_%C3%BCber_SSL) / [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)
- FTP / FTPS / SFTP - Server können als `Filesharing-Server oder Netzlaufwerke` genutzt werden.
- Dabei gibt es unterschiedliche Verschlüsselungsmöglichkeiten und Einsatzzwecke, jedoch unterstützen MacOS und Windows die FTP-Protokolle nicht nativ.
- Mehr zu den FTP-Protokollen in diesem Ordner unter [SFTP-Server](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Linux-Server/SFTP-Server.md)


-----------------------------------------------------------------------------------------------


# 9. Anwendungen und Dienste
- Zusätzlich können noch [Media-Server](https://de.wikipedia.org/wiki/Streaming-Server) wie [Plex](https://www.plex.tv/) oder [Jellyfin](https://jellyfin.org/) eingesetzt werden, um Filme lokal im Netzwerk vom eigenen Server streamen zu können.
- Außerdem können erfahrenere Nutzer auch Virtualisierungssoftware wie [Proxmox](https://www.proxmox.com/) oder [Docker](https://de.wikipedia.org/wiki/Docker_(Software)) verwenden oder Webserver wie [Apache](https://httpd.apache.org/) oder [Nginx](https://nginx.org/) einsetzen.
- Auch Smarthome-Integrationen sind möglich.
- Die Möglichkeiten sind unbegrenzt !


-----------------------------------------------------------------------------------------------
