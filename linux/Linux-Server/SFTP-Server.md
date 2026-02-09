# SFTP-Filesharing Server

`Anleitung verfasst am 25.6.2024, zuletzt bearbeitet am 17.8.2025`


## Inhaltsverzeichnis
1. FTP-Protokolle
	- [FTP](https://de.wikipedia.org/wiki/File_Transfer_Protocol) vs. [FTPS](https://de.wikipedia.org/wiki/FTP_%C3%BCber_SSL) vs. [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)
2. Einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol) Server auf [Ubuntu](https://ubuntu.com/download) einrichten
	- 2.1 File Sharing - Ordner für alle Benutzer freigegeben
	- 2.2. Ordner für alle Benutzer getrennt im Home-Verzeichnis
3. Mit SFTP-Server verbinden/ als Netzlaufwerk einhängen


-----------------------------------------------------------------------------------------------


## FTP-Protokolle
- [FTP](https://de.wikipedia.org/wiki/File_Transfer_Protocol) vs. [FTPS](https://de.wikipedia.org/wiki/FTP_%C3%BCber_SSL) vs. [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol)

### [FTP](https://de.wikipedia.org/wiki/File_Transfer_Protocol) (File Transfer Protocol) - nicht empfohlen
- Protokoll für Dateiübertragungen

- `Anwendungsfälle & Eigenschaften`
	- Öffentliche Download-Server
	- Webhosting
	- Keine Verschlüsselung
	
- `Vorteile`
	- Einfach zu implementieren
	- hohe Übertragungsgeschwindigkeit, da keine Verschlüsselung implementieren

- `Nachteile`
	- Unsicher (keine Verschlüsselung von Passwörtern oder Daten
	- manche moderne Systeme blockieren FTP aufgrund von Sicherheitsrisiken


### [FTPS](https://de.wikipedia.org/wiki/FTP_%C3%BCber_SSL) (FTP Secure) - nicht empfohlen
- Erweiterung von FTP mit SSL/TLS-Verschlüsselung.

- `Anwendungsfälle & Eigenschaften`
	- Erweiterung für vorhandener FTP-Server
	- Ähnlich wie FTP, aber mit Verschlüsselung
	- Nutzt TLS/SSL für Verschlüsselung
	
- `Vorteile`
	- Sicherer als FTP
	- Unterstützt von vielen FTP-Clients

- `Nachteile`
	- Komplizierter einzurichten als SFTP, Zertifikate erforderlich
	- Nicht so sicher wie SFTP
	- mehrere verschiedene Ports benötigt


### [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol) ([SSH](https://de.wikipedia.org/wiki/Secure_Shell) File Transfer Protocol) - empfohlen
- sichere Alternative zu FTP, basierend auf SSH

- `Anwendungsfälle & Eigenschaften`
	- Sichere Dateiübertragung durch Verschlüsselung
	- Automatisierungsprozesse mögich (Backups oder Synchronisation)
	- Verschlüsselte Verbindung via SSH
	
- `Vorteile`
	- Dateiübertragung, sowie Login-Daten werden verschlüsselt
	- Sicher durch SSH-Verschlüsselung
	- Einfache Einrichtung

- `Nachteile`
	- Etwas langsamer als FTP aufgrund der Verschlüsselung
	- Für Windows und MacOS zusätzliche Software erforderlich


-----------------------------------------------------------------------------------------------


# Einen [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol) Server auf [Ubuntu](https://ubuntu.com/download) einrichten

`Anleitung verfasst am 25.6.2024, zuletzt bearbeitet am 17.8.2025`

`Anleitung getestet auf Ubuntu 22.04.4 LTS`



## 2.1. File Sharing - Ordner für alle Benutzer freigegeben

- Betriebssystem
	- ggf. Ubuntu-Server installieren und einrichten
	- ggf. Festplattenverschlüsselung und Secure Boot einrichten

- System aktualisieren
```
$ sudo apt update 
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- [SSH](https://de.wikipedia.org/wiki/Secure_Shell) installieren und aktivieren
```
$ sudo apt install ssh
```
```
$ sudo systemctl status ssh
$ sudo systemctl enable ssh
$ sudo systemctl start ssh
```

- [Firewall](https://de.wikipedia.org/wiki/Firewall) installieren und [SSH](https://de.wikipedia.org/wiki/Secure_Shell) erlauben
```
$ sudo apt install ufw
$ sudo ufw allow ssh
$ sudo ufw enable
$ sudo ufw status
```

- SFTP User hinzufügen - es wird ein neuer Nutzer erstellt
```
$ sudo addgroup sftp
$ sudo adduser USERNAME
```
- Passwort für den neuen Benutzer wählen.
- Nehmen eingeben.
- Der Rest kann mit ENTER übersprungen werden.
- Mit `y` bestätigen

```
$ sudo usermod -a -G sftp USERNAME
```

- Pfad erstellen und Rechte für den Pfad setzten
```
$ sudo mkdir -p /var/sftp/Files
```
```
$ sudo chown root:root /var/sftp
$ sudo chmod 755 /var/sftp
```

- Hier Username von dem neu erstellten Benutzer eingeben
```
$ sudo chown USERNAME:USERNAME /var/sftp/Files
```

- SSH Konfigurationen öffnen
```
$ sudo nano /etc/ssh/sshd_config
```

- ganz unten Folgendes ergänzen:
```
Match User USERNAME
ChrootDirectory /var/sftp
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
```
- Zum speichern und schließen `STRG+X`
- SSH neustarten
```
$ sudo systemctl restart ssh
```


-----------------------------------------------------------------------------------------------


## 2.2. Ordner für alle Benutzer getrennt im Home-Verzeichnis


- System aktualisieren
```
$ sudo apt update && sudo apt upgrade
$ sudo apt autoremove && sudo apt autoclean
```


- [SSH](https://de.wikipedia.org/wiki/Secure_Shell) installieren und aktivieren
```
$ sudo apt install ssh
```
```
$ sudo systemctl status ssh
$ sudo systemctl enable ssh
$ sudo systemctl start ssh
```

- [Firewall](https://de.wikipedia.org/wiki/Firewall) installieren und [SSH](https://de.wikipedia.org/wiki/Secure_Shell) erlauben
```
$ sudo apt install ufw
$ sudo ufw allow ssh
$ sudo ufw enable
$ sudo ufw status
```

- separate SFTP User-Gruppe erstellen
```
$ sudo addgroup sftpgroup
```

- User hinzufügen (username ersetzen)
```
$ sudo adduser username
```
```
$ sudo usermod -a -G sftp username
```

- Pfad erstellen und Rechte für den Pfad setzten
```
$ sudo mkdir -p /home/username/sftp/Files
```
```
$ sudo chown root:root /home/username
$ sudo chmod 755 /home/username/sftp
```
```
$ sudo chown username:username /home/username/sftp/Files
```


- [SSH](https://de.wikipedia.org/wiki/Secure_Shell) Konfigurationen öffnen
```
$ sudo nano /etc/ssh/sshd_config
```

- ganz unten Folgendes in die Konfig ergänzen:
```
Match User username
ChrootDirectory /home/username/sftp
X11Forwarding no
AllowTcpForwarding no
ForceCommand internal-sftp
```
- Zum speichern und schließen `STRG+X`
- SSH neustarten
```
$ sudo systemctl restart ssh
```


-----------------------------------------------------------------------------------------------


# Mit SFTP-Server verbinden/ als Netzlaufwerk einhängen

- Unter `Linux` kann dafür der standard Dateimanager genutzt werden:
	- `sftp://IP-ADRESSE:PORT`

- Unter `Windows` kann [FileZilla](https://filezilla-project.org/) verwendet werden.

- Bei `MacOS` kann ebenfalls [FileZilla](https://filezilla-project.org/) oder [CloudMounter](https://cloudmounter.net/de/) verwendet werden.

- Für `Android` kann z.B. der [Total Commander](https://play.google.com/store/apps/details?id=com.ghisler.android.TotalCommander&hl=de) mit der kostenlosen [Erweiterung](https://play.google.com/store/apps/details?id=com.ghisler.tcplugins.SFTP&hl=de) für SFTP eine Verbindung hergestellt werden.
	- `IP-ADRESSE:PORT/Verzeichnis`

Mehr zum [Total Commander](https://play.google.com/store/apps/details?id=com.ghisler.android.TotalCommander&hl=de) und weiteren nützlichen Apps für Android im Repository: [Windows-Apple-und-Android /Android](https://github.com/replay45/Windows-Apple-und-Android/tree/main/Android)

 
 -----------------------------------------------------------------------------------------------
 
