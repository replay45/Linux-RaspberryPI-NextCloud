# Sicherheit auf Linux



# 1. Linux Update Befehle

## Paketmanager: apt


> [wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/APT/)

	```
	$ sudo apt update
	```
	```
	$ sudo apt upgrade
	```
	```
	$ sudo apt dist-upgrade
	```
	```
	$ sudo apt autoremove
	```
	```
	$ sudo apt autoclean
	```


mit "-get":
	```
	$ sudo apt-get update
	```
	```
	$ sudo apt-get upgrade
	```
	```
	$ sudo apt-get dist-upgrade
	```
	```
	$ sudo apt-get autoremove
	```
	```
	$ sudo apt-get autoclean
	```


Kombo Befehl:
	```
	apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y && apt-get autoremove -y && apt-get autoclean -y
	```


------------------------------------------------------------------------------------------------------------------------------



# 2. Benötigt man einen Virenscanner für Linux ?



Es wird nicht unbedingt ein Antivirenscanner für Linux benötigt, da Linux bereits als recht sicher gilt.
Durch die geringe Verbreitung von Linux bei Desktop Systemen, gibt es dementsprechend wenig Viren.


### Stattdessen sollte man:
Viel wichtiger ist es, die Firewall richtig zu konfigurieren und alle nicht benötigten Ports geschlossen zu halten. Hierfür kann z.B. [ufw](https://wiki.ubuntuusers.de/ufw/) genutzt werden.
Außerdem ist es extrem wichtig, das Ausführen von `unbekannten Befehlen im Terminal zu vermeiden` und darauf Acht zu geben, was aus dem Internet heruntergeladen wird.
Auch das regelmäßige Installieren von Updates ist wichtig.

Man sollte zudem immer auf das Verhalten im Web, also welche Webseiten und Links man besucht und auf E-Mails und ihre Anhänge achten.
Ebenfalls sollte man auf seine Online-Accounts achten und einen `Passwortmanager`, wie z.B. [KeePass]() oder [BitWarden]() nutzen.



- Wenn man trotzdem nicht auf einen Virenscanner für Linux verzichten möchte, empfiehlt sich [Clam AV](https://www.clamav.net/).
- Das Open-Source Programm kann im Terminal genutzt werden, aber es kann auch optional die Benutzeroberfläche installiert werden.




### [Installation von Clam AV](https://www.kali.org/tools/clamav/):
```
$ sudo apt install clamav
```
```
$ clamscan -h
```
```
$ sudo freshclam
```



------------------------------------------------------------------------------------------------------------------------------



# 3. Firewall


Firewallmanager - `ufw`:
	```
        $ sudo apt install ufw
	```
	```
        $ sudo ufw status
	```
	```
        $ sudo ufw app list
	```
	```
        $ sudo ufw allow 'PAKETNAME'
	```
	```
        $ sudo ufw enable
	```
	```
        $ sudo ufw disable
	```
	```
	$ sudo ufw reload
	```


------------------------------------------------------------------------------------------------------------------------------
