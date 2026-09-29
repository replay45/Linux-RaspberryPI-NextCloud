# [Nextcloud](https://nextcloud.com/de/) - Verschlüsselung

`Anleitung erstellt am 30.4.2025, zuletzt bearbeitet am 28.9.2026`


## Inhaltsverzeichnis
1. Netzwerkverbindung zur Nextcloud - TLS ([HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure))
2. Verschlüsselungsmodul (Dateiverschlüsselung)
3. Datenbankverschlüsselung (MariaDB)
4. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) & [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)
5. Ende-zu-Ende Verschlüsselung - [E2EE](https://de.wikipedia.org/wiki/Ende-zu-Ende-Verschl%C3%BCsselung)
6. weitere Sicherheitsmaßnahmen
	- [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort
	- [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot)
7. Fazit


- Mehr zu `Sicherheit unter Linux` unter [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung) zu finden.
- Mehr zu `Verschlüsselung unter Linux` unter [Verschlüsselung auf Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)


-----------------------------------------------------------------------------------------------


# 1. Netzwerkverbindung zur Nextcloud - TLS ([HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure))
- Damit die Netzwerkverbindung zwischen dem Client und dem Server verschlüsselt wird, muss eine TLS-Verschlüsselung über HTTP (HTTPS) eingerichtet werden.
- Damit wird die Verbindung inklusive der Anmeldedaten und Dateien, die über das Netzwerk zwischen dem Server und dem Client ausgetauscht werden, verschlüsselt.
- HTTPS ist sowie für interne als auch für öffentlich erreichbare Webseiten ein MUSS !
- Wie TLS verschlüsselung (HTTPS) verwendet werden kann, wird in diesem Ordner in der entsprechenden Installtionsanleitung erklärt.


-----------------------------------------------------------------------------------------------


# 2. Verschlüsselungsmodul (Dateiverschlüsselung der Nextcloud)
- Dieser Abschnitt bezieht sich auf den selbstgehosteten Installationstyp der Nextcloud mit dem Verschlüsselungsmodul der Nextcloud selber.
- Durch das Verschlüsselungsmodul werden nur Dateien verschlüsselt, nicht die Datenbank (Metadaten, Pfade, usw. bleiben unverschlüsselt) !
- Passwörter von Benutzern speichert die Nextcloud mit Argon2 als Hash­algorithmus, mit diesem Einweg-Krypto-Hash sind die Passwörter nicht rekonstruierbar gespeichert. Das gilt jedoch nicht für Benutzernamen oder E-Mail-Adressen.


### Verschlüsselungsmodul vs. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung)
- Die Festplattenverschlüsselung verschlüsselt die gesammte Festplatte/Datenträger im ausgeschalteten Zustand.
- Wenn die Festplatte beim Systemstart entschlüsselt wird, sind alle Daten für das System/Benutzern mit Admin/root-Berechtigungen zugänglich.
- Das Verschlüsseltungsmodul der Nextcloud verschlüsselt die Daten auf Dateiebene.
- Allerdings kann die Verschlüsselung über das Verschlüsselungsmodules der Nextcloud Backups erschweren oder bei Verlusst des Schlüssels werden die Daten unbrauchbar.
- Außerdem kann das Verschlüsselungsmodul die Performance beeinträchtigen.


### Aktivieren des Verschlüsselungsmodules in der Nextcloud
- In der Nextcloud anmelden
- In die `Verwaltungseinstellungen` wechseln
- Den Reiter `Sicherheit` auswählen
- `Serverseitige Verschlüsselung` aktivieren
- `Standard-Verschlüsselungsmodul` Haken setzen, um Dateien zu verschlüsseln die auf dem Server liegen
- `Nach der Aktivierung` werden standardmäßig `nur neue Dateien` verschlüsselt.
- Dateien die sich bereits in der Nextcloud befinden müssen manuell nachträglich noch verschlüsselt werden.


### Nachträgliches verschlüsseln von Dateien mit Standardverschlüsselungsmodul
- Um Dateien nachträglich zu verschlüsseln, die vor der Aktivierung des Verschlüsselungsmodules hochgeladen wurden:
Im Terminal über SSH auf den Server der Nextcloud einloggen
```
$ ssh user@IP-Adresse
```
OCC-Befehl (allgemeiner Befehl):
```
$ occ encryption:encrypt-all
```
Wenn Nextcloud über den [Snap](https://snapcraft.io/)-Paketmanager installiert wurde:
```
$ sudo nextcloud.occ encryption:encrypt-all
```


### Funktionsweise
- Die hochgeladenen Dateien in der Nextcloud werden vor der Speicherung auf den Massenspeicher verschlüsselt und dann verschlüsselt abgespeichert.
- Die Verschlüsselung funktioniert ähnlich wie bei Android, mit einer `dateibasierten Verschlüsselung`, wobei `Dateien unabhänig voneinander mit unterschiedlichen Schlüsseln verschlüsselt` werden. 
- Durch das `mehrstufige Schlüsselmanagement` werden diese `Schlüssel dann durch das Masterpasswort` bzw. die Zugangsdaten zur Nextcloud verschlüsselt.
- Somit können immer die benötigten Dateien entschlüsselt werden, was ein effizientes System ermöglicht.
- Als Verschlüsselungsalgorithmus wird AES verwendet.
- Durch das Verschlüsselungsmodul werden `nur Dateien verschlüsselt`, nicht die Datenbank oder andere Konfigurationen !


### Vorteile
- Schutz vor unbefugtem Zugriff auf Dateien
- gewährleistet Datenschutz & Integrität der Dateien auf dem Massenspeicher


### Nachteile
- möglicher Performanceverlust
- mögliche Schwierigkeiten mit Backups/Wiederherstellungen


### Zusätzliche Optionen
- Um alle Daten zu verschlüsseln, muss die Datenbank verschlüsselt werden, oder eine Verschlüsselung des Massenspeichers (z.B. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung)) vorgenommen werden.
- Mehr zur Festplattenverschlüsselung unter [Verschlüsselung unter Linux](http://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung/Verschl%C3%BCsselung-unter-Linux.md)


-----------------------------------------------------------------------------------------------


# 3. Datenbankverschlüsselung ([MariaDB](https://mariadb.org/))
- `Was die Datenbank beinhaltet:`
	- Die Datenbank enthält keine Dateien, diese liegen in einem separaten Pfad.
	- In der Datenbank sind Benutzer, Gruppen, Logs, Kommentare sowie Metadaten, wie Dateinamen, Pfade, usw. gespeichert.

- `Was verschlüsselt wird:`
	- Die Datenblöcke, also die Daten- und Speicherstrukturen der Datenbank, können mit AES verschlüsselt werden.

- `Vorteile`
	- Automatische Verschlüsselung neuer Tabellen.
	- Schutz vor Diebstahl der Datenbank.

- `Nachteile`
	- möglicher Performanceverlust
	- Metadaten bleiben im Klartext
	- Administratoren mit Zugriff auf den Masterkey können trotzdem entschlüsseln

- Datenbankverschlüsselung kann bei Nextcloud über [Snap](https://snapcraft.io/) zu `Problemen` führen, hier ist eine manuelle Installation der Nextcloud (LAMP-Stack) die bessere Wahl.
- Außerdem ist die Verschlüsselung der Datenbank nur Sinnvoll wenn KEINE Festplattenverschlüsselung verwendet wird, denn bei Verwendung der Festplattenverschlüsselung bringt das zusätzliche Verschlüsseln der Datenbank keinen Sicherheitsvorteil.


-----------------------------------------------------------------------------------------------


# 4. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) & [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)


### [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) mit [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module) 2.0 - Empfohlen
- `Wieso sollte man die Festplattenverschlüsselung nutzen ?`
	- Die Festplattenverschlüsselung ist eine sinnvolle Maßnahme zum Schutz aller Daten auf der Festplatte.
	- Bei der Festplattenverschlüsselung wird die gesamte Partition auf der Festplatte verschlüsselt und alle Daten sind bei physischem Diebstahl des Servers trotzdem sicher.
	- Damit der Schutz gewährleistet werden kann, muss eine `starke Passphrase` gewählt werden !
	- Da das Eingeben der Passphrase normalerweise bei jedem Boot-Vorgang notwendig ist, empfiehlt es sich, das [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)-Modul `auf dem Mainboard` zu verwenden, damit die Passphrase dort verschlüsselt gespeichert werden kann und beim Boot-Vorgang das System automatisch nach der Integritätsprüfung entschlüsselt werden kann.
	- Die Festplattenverschlüsselung zum Schutz der Daten, des Betriebssystems, offline Angriffen oder physischem Diebstahl, ist nicht nur bei Linux notwendig, sondern ebenfalls bei Windows oder MacOS !


- `Vorteile der Festplattenverschlüsselung`
	- Alle Daten auf dem Massenspeicher werden verschlüsselt
	- Schutz bei physischem Diebstahl der Hardware
	- Schutz vor unbefugtem Zugriff
	- Schutz auch nach Entsorgung des Massenspeichers
	- Schutz vor unbefugtem Zugriff auf das Recovery-Menü, wo Passwörtwe von lokalen Benutzern zurückgesetzt werden können
	- Schutz vor weiteren physichen/offline-Angriffen auf das System

- `Nachteile`
	- Die Festplattenverschlüsselung schützt nicht direkt im laufenden Betrieb
	- Bei alter Hardware möglicher Performanceverlust



## Einrichtung von [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) mit [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)
- `Voraussetzungen für TPM`
	- LUKS2 und TPM2.0 auf dem Mainboard,
	- systemd-cryptenroll (ab Ubuntu 20.04+ verfügbar)

- `Vorteile von TPM`
	- Automatische Entsperrung beim Boot durch TPM (nur auf dem installierten System),
	- Veränderungen am Bootloader oder Kernel verhindern automatischen Boot-Vorgang (Passphrase muss manuell bestätigt werden)

- `Nachteile`
	- TPM muss ggf. vorhanden und korrekt konfiguriert sein,
	- etwas komplexere Einrichtung,
	- keine Migration des Speichers in anderen Server ohne Re-Enrollment

- `Funktion von TPM`
	- Das TPM Modul auf dem Mainboard speichert die Passphrase verschlüsselt ab.
	- Beim Systemstart prüft das TPM die Integrität des Systems, denn das System wird nur bei unverändertem Systemzustand entschlüsselt.
	- Sollten Änderungen am System z.B. am UEFI oder Bootloader vorgenommen werden, muss die Passphrase manuell eingegeben werden.

- `Installation einer Linux-Distribution (z.B. Ubuntu-Server):`
	- Den Installationsassistenten starten
	- Einrichtung vornehmen
	- Bei dem Punkt Festplattenverschlüsselung die Option `LUKS` aktivieren
	- Passphrase wählen und eingeben (wird benötigt, wenn TPM defekt sein sollte, um manuell noch entschlüsseln zu können)
	- Es sollte eine starke Passphrase gewählt werden und sicher abgespeichert werden, z.B. in einem [Passwortmanager](https://de.wikipedia.org/wiki/Kennwortverwaltung).
	- Installation fortsetzen und beenden.
	- Bis zur Einrichtung von TPM, muss erstmal mit der Passphrase entschlüsselt werden.


### Einrichtung von [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module):
- Linux starten, entschlüsseln und anmelden.
- notwendige Pakete installieren
```
$ sudo apt update
$ sudo apt install clevis clevis-luks clevis-tpm2
```
- Überprüfen, ob TPM vorhanden und funktionsfähig ist
	- Ausgabe sollten 8 zufällige Bytes sein. Falls nicht, überprüfen, ob TPM-Modul vorhanden ist
```
$ sudo tpm2_getrandom 8
```
- Bind LUKS-Volume an TPM:
	- Ersetzen von `/dev/sdXn` durch eigenen Pfad (z. B. /dev/sda3).
```
$ sudo clevis luks bind -d /dev/sdXn tpm2 '{}'
```
- Testen
	- Server neu starten
	- Wenn der TPM-Unlock funktioniert, wird keine Passphrase abgefragt.
	- TPM-Unlocking hängt von der Plattformkonfiguration (PCRs) ab. Änderungen an BIOS, Kernel oder initramfs können TPM-Unlocking verhindern.


-----------------------------------------------------------------------------------------------


# 5. Ende-zu-Ende Verschlüsselung - [E2EE](https://de.wikipedia.org/wiki/Ende-zu-Ende-Verschl%C3%BCsselung)
- Zuletzt bleibt noch die Ende-zu-Ende Verschlüsselung, wobei die Dateien auf dem Client verschlüsselt werden und danach auf der Nextcloud abgespeichert werden.
- In der Theorie ist das die sicherste Methode, um Dateien auf dem Server zu speichern, da nur der Client den Schlüssel hat, jedoch gibt es viele Nachteile, die die Umsetzung erschwehren.
- E2EE schützt Daten selbst bei Serverkompromittierung, jedoch nicht, wenn der Client kompromittiert ist.
- Die Verwendung ist nur für bestimmte besonders schützenswerte Ordner vorgesehen und nicht global einsetzbar.
- GGf. lassen sich z.B. Kalender oder Kontakte nicht mit der E2EE schützen.
- Außerdem können nur die Desktop- & Mobilen-Clients (Apps) die E2EE nutzen.
- Daher ist die Nutzung der E2EE nur für bestimmte Ordner/Inahlte sinnvoll.


-----------------------------------------------------------------------------------------------


# 6. weitere Sicherheitsmaßnahmen

### [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort
- Das UEFI-Passwort schützt vor unbefugten Änderungen am UEFI, da man zunächst das Passwort eingeben muss, um in das UEFI zu gelangen.
- Es ist durchaus ratsam, die Option zu aktivieren, da sie einen zusätzlichen Schutz vor Manipulationen bietet.
- Jedoch Vorsicht: Wenn man die Zugangsdaten für das UEFI verliert, kann das Gerät/[Mainboard](https://de.wikipedia.org/wiki/Hauptplatine) schnell `unbrauchbar` werden. Daher unbedingt die Zugangsdaten notieren und sicher aufbewahren. Dafür eignet sich z.B. ein [Passwortmanager](https://de.wikipedia.org/wiki/Kennwortverwaltung).
- Die Option kann man meistens unter dem Reiter `Security` aktivieren und heißt häufig `Administrator-Passwort`, das kann jedoch von Hersteller zu Hersteller unterschiedlich sein.


### [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) 
- Secure Boot ist eine Sicherheitsfunktion (Integritätsschutz), die sicherstellt, dass beim Boot-Vorgang vom Betriebssystem nur vertrauenswürdige Software geladen wird.
- Das Ziel ist, einen Schutz vor Manipulationen am Betriebssystem zu implementieren, um das Laden von Schadcode oder veränderten Bootloadern zu verhindern.
- Technisch funktioniert das über eine digitale Signatur, wodurch nicht signierte oder veränderte Software blockiert wird.
- Die `Einrichtung` von Secure-Boot wird in der Anleitug [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux) gezeigt.


-----------------------------------------------------------------------------------------------


# 7. Fazit
- Die Transportverschlüsselung für Netzwerkverbindungen, also `TLS (HTTPS)` muss unbedingt eingerichetet werden.
- Je nach Umgebung kann das `Standardverschlüsselungsmodul für Dateiverschlüsselung`, was einen grundlegenden Schutz für Dateien sicherstellt, verwendet werden.
- Wenn KEINE Festplattenverschlüsselung eingesetzt wird, kann die MariaDB-Datenbank optional verschlüsselt werden.
- Die Verwendung von [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) z.B. in Kombination mit TPM, kann eine effektive Maßnahme sein, um den Verlusts von Daten bei physischem Diebstahl der Hardware zu verhindern.
- Um besonders schützenswerte Daten zu verschlüsseln, kann vereinzelt die Ende-zu-Ende-Verschlüsselung ([E2EE](https://de.wikipedia.org/wiki/Ende-zu-Ende-Verschl%C3%BCsselung)) eingesetzt werden.
- Selbstverständlich sollten auch alle Zugänge durch sichere Zugangsdaten in Kombination mit der 2-Faktor-Authentifizierung abgesichert werden und grundlegende Sicherheitsfeatures wie z.B. eine [Firewall](https://de.wikipedia.org/wiki/Firewall) auf dem Server eingesetzt werden.
- Außerdem sollten zusätzlich Härtungsmaßnahmen, wie die `Absicherung von SSH-Zugängen`, `Logging/Monitoring`, `automatische Sicherheitsupdates` und `physiche Sicherungen` umgesetzt werden.
- Der Idealfall ist die Kombination dieser Optionen und eine Risikobewertung mit konkreten Gegenmaßnahmen, um Daten+ zu schützen.


-----------------------------------------------------------------------------------------------
