# 1. Programme auf Linux über das Terminal deinstallieren


- Liste installierter Pakete:
	```
        $ dpkg --list
	```

- nach einem bestimmten Paket suchen:
	```
	$ dpkg --list | grep [PROGRAMMNAME]
	```

	- `z.B: $ dpkg --list | grep "firefox"`



A. Paketmanager [apt](https://wiki.ubuntuusers.de/APT/)

- Deinstallation aber Konfig-Datein werden behalten:
	```
        $ sudo apt-get remove PAKETNAME
	```

- Deinstallation & Entfernung Konfigurationsdateien:
	```
        $ sudo apt-get --purge remove [PAKETNAME]
	```


B. [Flatpak](https://wiki.ubuntuusers.de/Flatpak/)

- Deinstallation von Programmen:
	```
        $ sudo flatpak uninstall [PAKET]
	```


-------------------------------------------------------------------------------------------------------------------------


# 2. Paket Konfigurationen für Kali Linux - [Tasksel](https://pkg.kali.org/pkg/tasksel)

Mit `Software-selection` können im Nachhinein weitere Tools, Desktopumgebungen etc. nachinstalliert oder entfernt werden.


> [Tasksel - wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/tasksel/)


```
$ sudo apt install tasksel
```
```
$ sudo tasksel
```


- Mit der Leertaste kann die Auswahl bestätigt werden.




WICHTIG:
	- Wenn ein bereits vorhandenes `*` bei einer Option entfernt wird, werden die Programme etc. hinter dieser Option entfernt !
	- Daher bei dem Setzen der `*` sorgfältig arbeiten !



-------------------------------------------------------------------------------------------------------------------------



# 3. Flatpak auf Kali installieren



[Flatpak](https://www.kali.org/docs/tools/flatpak/) ist ein Paketmanager für Linux.


Hier verwendetes Betriebssystem: Kali mit der GNOME Benutzeroberfläche


- Flathub App-Store installieren:

	$ sudo apt install flatpak
	$ sudo flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
	$ sudo apt install gnome-software-plugin-flatpak




- optional die Apps, die über Flatpak installiert wurden, an die Kali-Themes anpassen:

- Themes anpassen (Kali-Themes in den Apps):

	$ mkdir -p ~/.themes
	$ cp -a /usr/share/themes/* ~/.themes/
	$ sudo flatpak override --filesystem=~/.themes/




- Pakete die über Flathub installiert wurden, updaten:
	$ flatpak update


- Liste installierter Programme:
        $ flatpak list


- Deinstallation von Programmen:
        $ sudo flatpak uninstall [PAKET] 


- kaputte Installation Fix:
        $ flatpak repair


- Verlauf anzeigen:
        $ flatpak history





> [Flatpak - Setup](https://flatpak.org/setup/Ubuntu)
> [Flatpak - wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/Flatpak/)



-------------------------------------------------------------------------------------------------------------------------



# 4. kali undercover mode Xfce


Der undercover-Modus verwandelt die Xfce Oberfläche, in eine täuschend echte Windows10 Oberfläche.

Befehl:
	$ kali-undercover


Um den Modus zu deaktivieren, einfach den Befehl erneut in das Terminal eingeben.


Die undercover-Funktion funktioniert ausschließlich in Xfce !



https://www.kali.org/
https://www.xfce.org/



-------------------------------------------------------------------------------------------------------------------------


# 5. ssh Server auf Ubuntu installieren, um über ssh Zugriff auf das Terminal zu erhalten.


Auf dem Host:

sudo apt install openssh-server
sudo systemctl status ssh
sudo systemctl enable ssh
sudo systemctl start ssh
sudo ufw allow ssh


OPTIONAL:
ggf. können zusätlich noch einige Einstellungen angepasst werden
sudo nano /etc/ssh/sshd_config
Speichern mit STRG+X
$ sudo service ssh restart


Dateien über SSH mit SCP kopieren:
Secure Copy (SCP) ist ein älteres Protokoll zur verschlüsselten Übertragung von Daten über SSH.


Eine Datei vom lokalen zum entfernten System kopieren:
        $ scp DATEI.TXT DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD


Eine Datei vom entfernten zum lokalen System kopieren
        $ scp DEIN-USERNAME@IP-ADRESSE:/DEIN/PFAD/ZUR/DATEI.TXT



https://www.kali.org/tools/openssh/
https://wiki.ubuntuusers.de/SSH/



-------------------------------------------------------------------------------------------------------------------------


# 6. Metadata Cleaner


Metadaten in einer Datei enthalten viele Informationen.
Kameras zeichnen Daten darüber auf, wann und wo ein Bild aufgenommen und welche Kamera verwendet wurde. 
Office-Anwendungen fügen automatisch Autoren- oder Firmeninformationen zu Dokumenten hinzu. 
Nicht immer möchte man, dass diese Informationen für Andere zugänglich sind.


Mit dem Tool Metadata Cleaner kann man Metadaten in Dateien anzeigen und weitesgehend entfernen.

Es gibt jedoch niemals eine Garantie dafür, dass alle Metadaten vollständig entfernt werden.


Der Metadata Cleaner kann über Flathub installiert werden, z.B. über den Flathub-Store.


Um nun die Metadaten einzusehen und zu entfernen, muss der Metadata Cleaner geöffnet,
die Datei eingefügt und dann nur noch unten rechts auf den Button "Clean" gedrückt werden.


Es empfiehlt sich immer, eine Kopie der Datei zu sichern, bevor die Metadaten entfernt werden.



Link zum Flathub-Store:
https://flathub.org/apps/fr.romainvigier.MetadataCleaner

Link zur Webseite vom Metadata Cleaner:
https://metadatacleaner.romainvigier.fr/


-------------------------------------------------------------------------------------------------------------------------



# 7. Apache2 Webserver installieren:

$ sudo apt update
$ sudo apt install apache2


Firewall-Einstellungen:
$ sudo ufw app list
$ sudo ufw allow 'Apache'
$ sudo ufw status

Apache2 Status & Autostart:
$ sudo systemctl status apache2
$ sudo systemctl enable apache2
$ sudo systemctl start apache2
$ sudo systemctl status apache2


Für ein besseres Dateimanagement kann optional der Midnight Commander (auch bekannt als "Total Commander") verwendet werden:
$ sudo apt install mc
$ sudo mc



https://wiki.ubuntuusers.de/ufw/

https://httpd.apache.org/
https://wiki.ubuntuusers.de/Apache_2.4/


-------------------------------------------------------------------------------------------------------------------------
