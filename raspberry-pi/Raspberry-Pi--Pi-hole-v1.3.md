# [Raspberry Pi](https://www.raspberrypi.com/) & [Pi hole](https://pi-hole.net/)


`Anleitung verfasst am 15.5.2024, zuletzt bearbeitet 24.2.2025`


## Inhaltsverzeichnis
1. Was ist ein DNS-Server & Was ist [Pi hole](https://pi-hole.net/) ?
2. [Pi hole](https://pi-hole.net/) installieren, einrichten, Firewall & updaten
3. Blocklisten (Filterlisten) & Domains blacklisten/whitelisten
4. Backups erstellen
5. Welchen DNS Upstream-Server sollte man wählen ?
6. mobile Geräte: gewünschte DNS-Server wie von [Cloudflare](https://www.cloudflare.com/) oder [Pi hole](https://pi-hole.net/) außerhalb des Heimnetzes nutzen können
7. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC
8. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für Web-Interface - über IP-Adresse (ohne Domain)


----------------------------------------------------------------------------------------------------------------


# 1. Was ist ein DNS-Server & Was ist [Pi hole](https://pi-hole.net/) ?

### A. Was ist ein DNS-Server ?
Ein DNS-Server `übersetzt Domainnamen` wie "google.de" `in IP-Adressen`, denn Domains sind für uns Menschen, im Gegensatz zu IP-Adressen, einfacher zu merken.
Außerdem gibt es nicht genügend verfügbare IPv4 Adressen, daher können diese sich auch ändern, was jedoch dank der DNS-Server kein Problem ist.

[Cloudflare - Was ist ein DNS-Server](https://www.cloudflare.com/de-de/learning/dns/what-is-a-dns-server/)


### B. Was ist [Pi hole](https://pi-hole.net/) ?
Pi hole ist eine kostenlose Software, die als DNS-Server im Heimnetz verwendet werden kann.
Der Pi hole hat viele Funktionen und kann Werbung sowie Tracker blockieren und optional als [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) eingerichtet werden.


----------------------------------------------------------------------------------------------------------------


# 2. [Pi hole](https://pi-hole.net/) installieren, einrichten, Firewall & updaten


`Die Installation von Pi hole ist auf den meisten gängigen Linux-Distibutionen möglich, allerdings beschränkt diese Anleitung sich auf die Installation von Pi hole auf einem Rapberry Pi mit Pi OS.`


## A. [Installation](https://github.com/pi-hole/pi-hole/#one-step-automated-install)
1. erste Methode
```
$ curl -sSL https://install.pi-hole.net | bash
```

2. zweite Methode
```
$ git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
$ cd "Pi-hole/automated install/"
$ sudo bash basic-install.sh
```

3. dritte Methode
```
$ wget -O basic-install.sh https://install.pi-hole.net
$ sudo bash basic-install.sh
```


## B. [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) vergeben
- Der Raspberry Pi benötigt unbedingt eine im Router (bzw. DHCP-Server), reservierte IP-Adresse, die sich nach dem Festlegen NICHT mehr automatisch ändern kann !


## C. [Firewall](https://de.wikipedia.org/wiki/Firewall) Konfiguration mit [ufw](https://wiki.ubuntuusers.de/ufw/)
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw allow 80,443,53,853/udp
$ sudo ufw allow 80,443,53,853/tcp
```

- Wenn Pi hole auch als [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) genutzt wird:
```
$ sudo ufw allow 67/udp
$ sudo ufw allow 67/tcp
```

- Wenn auf den Pi über SSH zugegriffen werden soll:
```
$ sudo ufw allow ssh
```

- Firewall aktivieren:
    - Hinweis: Bei einer SSH Verbindung sollte vor der Aktivierung der Firewall der Port 22 für SSH freigegeben werden! - (siehe oben)
```
$ sudo ufw enable
$ sudo ufw status
```


## D. Einrichtung
- Dem Installationsassistenten folgen.
- DNS-Server wählen.
- Die empfohlene Blocklist kann optional hinzugefügt werden.
- Das Admin Web-Interface installieren.
- Empfohlen: `query logging` aktivieren.
- Empfohlen: Die Auswahl `Show everything` beibehalten.
- `Das Passwort, was nun erscheint, notieren !`


Um das Passwort des Web-Interfaces zu ändern den folgenden Befehl nutzen:
```
$ sudo pihole setpassword
```


## E. Web-Interface

> http://IP-Adresse/admin

> http://pi.hole/admin


## F. Pi als DNS Server im Router/ in einzelnen Geräten einstellen
- In einem Router, z.B. in der [FritzBox](https://avm.de/) den Pi als DNS Server einstellen (IP-Adresse des Pis als DNS-Server eingeben).
- Alternativ den Pi nur in ausgewählten Geräten einstellen.
- Dafür entweder in einem Router oder in einzelnen Geräten die `IP-Adresse` des Raspberry Pis (mit Pi hole) `als DNS-Server` eintragen.


## G. [Pi hole](https://pi-hole.net/) Updates
```
$ pihole -up
```

## H. Blockierung kurzzeitig unterbrechen
- Dafür einfach auf den Reiter `Disable Blocking` gehen und einen gewünschten Zeitraum wählen.


----------------------------------------------------------------------------------------------------------------


# 3. Blocklisten (Filterlisten) & Domains blacklisten/whitelisten

## A. Blocklisten finden
- Unter [filterlists.com](https://filterlists.com/) kann man die unterschiedlichsten Filterlisten für diverse Ad-Blocker für den Browser und natürlich auch für den Pi hole finden.
- Es gibt die Möglichkeit, einen Filter zu setzen, womit nur noch die Filterlisten für das gewünschte System angezeigt werden.
- Nun kann man sich nach Belieben Blocklisten kopieren.


## B. Blocklisten hinzufügen
- Um eine neue Blockliste hinzuzufügen, im Web-Interface des Pi holes unter `Adlists` die URL der Filterliste einfügen und optional eine Beschreibung hinzufügen.
- Nun auf `Add` klicken und unter `Tools / Update-Gravity` ein Filterlisten-Update ausführen, oder folgenden Befehl ausführen: `$ pihole -g`


## C. einzelne Domains blacklisten/whitelisten
- Unter `Domains` können einzelne Domains nach Bedarf auf die Blacklist gesetzt werden, falls diese nicht in einer Blockliste enthalten sind.
- Außerdem können auch Domains auf die Whitelist gesetzt werden, falls diese durch eine Blockliste blockiert werden.


----------------------------------------------------------------------------------------------------------------


# 4. Backups erstellen
- Backups erstellen und exportieren
    - Im Web-Interface einloggen
    - `Settings` öffnen
    - `Teleporter`
    - `Einstellungen exportieren`
    - an einen sicheren Ort speichern

- Backups importieren
    - Im Web-Interface einloggen
    - `Settings` öffnen
    - `Teleporter`
    - `Choose File` klicken
    - Backup-Datei auswählen
    - `Einstellungen importieren`


----------------------------------------------------------------------------------------------------------------


# 5. Welchen DNS Upstream-Server sollte man wählen ?
- Je nach Standort kann die Geschwindigkeit von Anbieter zu Anbieter variieren.
- Stellvertretend sind hier die Vor-/ Nachteile von 2 Anbietern aufgelistet:


### Empfohlen: [Cloudflare](https://www.cloudflare.com/) - IPv4: `1.1.1.1`, `1.0.0.1`

- `Vorteile`
    - empfehlenswerter Anbieter, weite Verbreitung
    - Geschwindigkeit (schnelle Server)
    - Server auf der ganzen Welt
    - Fokus auf Datenschutz & Sicherheit
    - kein Logging
    - ideal für private Nutzer & Haushalte

- `Nachteile`
    - in weniger gut angebundenen Regionen gelegentliche Verzögerungen
    - Bei intensiver Nutzung durch sehr große Netzwerke, möglicherweise Rate-Limiting


### [Google](https://dns.google/) - IPv4: `8.8.8.8`, `8.8.4.4`

- `Vorteile`
    - weite Verbreitung
    - Geschwindigkeit (schnelle Server)
    - sehr hohe Verfügbarkeit
    - Zuverlässigkeit

- `Nachteile`
    - manche Informationen, wie Internetdienstanbieter oder Standort werden gespeichert (laut Angaben von Google)
    - abgefragte IP-Adressen werden aus "Sicherheitsgründen" "temporär" gespeichert (laut Angaben von Google)
    - Verbesserung der Dienste durch Auswertung von Daten
    - Google teilt Daten mit Partnerunternehmen


### Wo kann man den Upstream DNS Server im [Pi hole](https://pi-hole.net/) wechseln ?
- Unter dem Reiter `Settings` und `DNS` kann der Upstream DNS Server gewählt werden.


----------------------------------------------------------------------------------------------------------------


# 6. mobile Geräte: gewünschte DNS-Server wie von Cloudflare oder [Pi hole](https://pi-hole.net/) außerhalb des Heimnetzes nutzen können

### DNS-Server bei mobilen Geräten außerhalb des Heimnetzwerkes
- Es ist außerdem auch möglich, den `DNS Server bei mobilen Geräten` wie Laptops und Handys etc. zu ändern, um den gewünschten Anbieter nutzen zu können.
- Das ist in der Hinsicht interessant, wenn man `verhindern` möchte, dass der `Internetprovider bzw. Mobilfunkanbieter einsehen kann, welche Domains man aufruft` (Tracking verhindern).
- Wie oben beschrieben ist [Cloudflare](https://www.cloudflare.com/) ein Anbieter, der den Fokus auf `Datenschutz & Sicherheit` legt.
- Mehr dazu in den einzelnen `Beiträgen zur Sicherheit auf dem entsprechenden Betriebsystem`. - [Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung), [MacOS](https://github.com/replay45/Windows-Apple-und-Android/tree/main/Apple), [Android](https://github.com/replay45/Windows-Apple-und-Android/tree/main/Android)


### VPN ins Heimnetzwerk, um [Pi hole](https://pi-hole.net/) nutzen zu können
- Viele `moderne Router`, wie u.a. auch die [FritzBox](https://avm.de/), können als `eigener VPN-Server` genutzt werden.
- Dabei stellt man vom Laptop, Tablet oder Handy eine `VPN Verbindung in das eigene Heimnetz` her und nutzt somit `auch Pi hole als DNS-Server`, sofern dieser als `primärer DNS-Server im Router` hinterlegt ist.
- Außerdem kann man dann von überall auf seine `Geräte im Heimnetz zugreifen`, wie z.B. auf einen [NAS](https://de.wikipedia.org/wiki/Network_Attached_Storage) oder Homeserver.
- Die VPN-Verbindung kann zudem auch in öffentlichen oder fremden WLANs genutzt werden, um den `eigenen Netzwerk-Traffic zu verschlüsseln`.
- Dabei sollte man bedenken, dass `alle DNS-Anfragen über den Router im Heimnetz` geleitet werden und dass dieser VPN-Server des eigenen Routers nicht immer den klassischen VPN-Dienst eines externen Anbieters ersetzt.
- Dafür eignet sich das moderne [WireGuard](https://www.wireguard.com/) Protokoll, was als besonders sicher gilt und dabei auch noch vergleichsweise wenige Ressourcen verbraucht, was wiederum energieeffizient ist.


----------------------------------------------------------------------------------------------------------------


# 7. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC

## Wieso sollte man DNS Verschlüsselung/Signaturverfahren verwenden ?
- Schutz der `Privatsphäre` vor Dritten, wie Hackern (nur DoT/DoH)
- Schutz vor `Tracking` durch Internetanbieter (nur DoT/DoH)
- Schutz vor `Man-in-the-Middle-Angriffen`, bei denen die DNS-Anfragen `manipuliert` werden. (DoT/DoH/DNSSEC)
- Schutz vor `Umleitung` auf bösartigen Webseiten. (DoT/DoH/DNSSEC)


## Welche Verschlüsselung oder Signaturverfahren sollte man nutzen: DNSSEC / DoT / DoH ?

1. DNSSEC (Domain Name System Security Extensions) - Signaturverfahren

    `Vorteile:`
    - Anfragen werden digital signiert (Schutz vor Manipulation)
    - Sicherstellung, dass DNS-Anfragen authentisch und unverändert sind (Schutz vor Manipulation)
    - im Pi hole einfach zu aktivieren

    `Nachteile:`
    - Keine Verschlüsselung (Anfragen sind weiterhin im Klartext, werden nur signiert, KEIN Schutz vor Tracking)
    - Viele Webseiten unterstützen noch kein DNSSEC
    - Höherer Ressourcenbedarf


2. DoH (DNS over HTTPS)

    `Vorteile:`
    - Sichere Verschlüsselung
    - Schützt die Privatsphäre (Schutz vor Tracking)
    - Schützt vor Manipulationen (Schutz vor `Man-in-the-Middle-Angriffen`)
    - DoH verwendet HTTPS und tarnt den DNS-Traffic als normalen Web-Traffic

    `Nachteile:`
    - Anfragen werden NICHT digital signiert
    - Namensauflösung auf dem DNS-Server ist unverschlüsselt (lediglich der Traffic zwischen Client und DNS-Server wird verschlüsselt)


3. DoT (DNS over TLS)

    `Vorteile:`
    - Sichere Verschlüsselung
    - Schützt die Privatsphäre (Schutz vor Tracking)
    - Schützt vor Manipulationen (Schutz vor `Man-in-the-Middle-Angriffen`)

    `Nachteile:`
    - Anfragen werden NICHT digital signiert
    - Namensauflösung auf dem DNS-Server ist unverschlüsselt (lediglich der Traffic zwischen Client und DNS-Server wird verschlüsselt)
    - DoT-Verschlüsselung ist als DNS-Traffic erkennbar (aber verschlüsselt).



4. `Fazit`
    - DNSSEC schützt vor Manipulation durch Signatur, bietet aber keine Verschlüsselung und keinen Schutz vor Tracking.
    - DoH-Verschlüsselung bietet mehr Datenschutz und Sicherheit (nur grundlegende Verschlüsselung auf dem Kommunikationsweg), Traffic als normaler Web-Traffic getarnt.
    - DoT-Verschlüsselung bietet mehr Datenschutz und Sicherheit (nur grundlegende Verschlüsselung auf dem Kommunikationsweg), als verschlüsselter-DNS-Traffic erkennbar.


## Aktivieren von DNSSEC im [Pi hole](https://pi-hole.net/)
- Im Web-Interface des Pi holes unter dem Reiter `Settings` und `DNS` die Option `USE DNSSEC` aktivieren
- Nun noch auf `Speichern` klicken.
- Im Log werden nun die signierten Anfragen mit `SECURE` gekennzeichnet.
- Domains, die kein DNSSEC unterstützen, werden mit `INSECURE` gekennzeichnet.


----------------------------------------------------------------------------------------------------------------


# 8. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für Web-Interface - über IP-Adresse (ohne Domain)

`Anleitung mit Version 6.0 von Pi hole getestet`

## 1. Einschränkungen (bzw. Nachteile)
- Wenn man KEINE Domain für Pi hole nutzt, sondern diesen nur über die IP-Adresse betreibt, dann sind die `Möglichkeiten HTTPS zu nutzen eingeschränkt`.
- Eigentlich hat man nur die Möglichkeit, das `selbst-signierte Zertifikat` von Pi hole `manuell in den Browser, bzw. das Betriebsystem hinzuzufügen, um HTTPS nutzen zu können`.
- Das ist dann aber nur für den entsprechenden Browser, daher muss man das `Zertifikat in jeden Browser/Betriebssystem einfügen`, mit dem man das Web-Interface aufrufen möchte.


## 2. Wichtige Informationen zum Zertifikat für HTTPS
- Um eine HTTPS-Verschlüsselung nutzen zu können, benötigt man ein SSL-Zertifikat.
- Pi hole selber erstellt ein Zertifikat, das genutzt werden kann, um HTTPS bei einem bestimmten Browser nutzen zu können.
- Da das `Zertifikat "selbst signiert" ist`, wird es `nicht als vertrauenswürdig eingestuft`. Das ist allerdings `KEIN Problem`, da die `HTTPS-Verbindung trotzdem gegeben` ist.
- Im [Firefox](https://www.mozilla.org/de/firefox/new/)-Browser kann man eine sogenannte Ausnahme hinzufügen, damit die Warnmeldungen nicht mehr erscheinen.
- In Browsern, die auf Chromium basieren, sind nur temporäre Ausnahmen möglich.


## 3. Das Zertifikat von Pi hole erneuern (nur bei Bedarf nötig)
```
$ sudo rm /etc/pihole/tls* && sudo service pihole-FTL restart
```


## 4. Das Zertifikat vom Pi hole über [SCP](https://de.wikipedia.org/wiki/Secure_Copy) herunterladen
- Es wird das Zertifikat `tls_ca.crt` im Verzeichnis `/etc/pihole/` benötigt.
- Zertifikat über SCP kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/etc/pihole/tls_ca.crt /dein/lokales/verzeichnis/zielordner
```

- Wenn ein Berechtigungsfehler erscheint (Befehle auf dem pi hole Server ausführen):
```
$ ssh user@IP-Adresse
```
```
$ sudo cp /etc/pihole/tls_ca.crt /home/user
```
```
$ cd /home/user
$ ls
```
```
$ sudo chmod 644 /home/user/tls_ca.crt
```
- Nun nochmal mit SCP versuchen, die Datei zu kopieren.



## 5. Zertifikat in das Betriebssystem einfügen

### 5.1. Zertifikate unter Linux (Debian-basierte Distributionen) einfügen
- `tls_ca.crt` Zertifikat in den Pfad kopieren
```
$ sudo cp /Pfad/zu/tls_ca.crt /usr/local/share/ca-certificates/tls_ca.crt
```
```
$ sudo update-ca-certificates
```
```
$ reboot
```


### 5.2. Zertifikate unter Windows einfügen
- `tls_ca.crt` Zertifikat im Datei-Explorer aufrufen

- Für einen bestimmten Benutzer
	- `Windows-Taste + R` drücken
	- `certmgr.msc`
	- `Enter`
	- Vertrauenswürdige Stammzertifizierungsstellen
	- `Zertifikate`
	- `Alle Tasks`
	- `Importieren`
	- Assisten starten
	- `Weiter`, `Durchsuchen und Zertifiakt` wählen
	- Zertifikatsspeicher manuell auswählen
	- `Vertrauenswürdige Stammzertifizierungsstellen`
	- `Weiter`, `Fertig stellen`

- Für alle Benutzer (systemweit)
	- `Windows-Taste + R` drücken
	- `mmc`
	- `Enter`
	- `Datei`
	- `Snap-In hinzufügen/entfernen`
	- `Zertifikate`, `Hinzufügen`
	- `Computerkonto`
	- `Weiter`
	- `Lokaler Computer`
	- `Fertig stellen`
	- `OK`
	- Doppelklick auf `Zertifikate (lokaler Computer)`
	- `Vertrauenswürdige Stammzertifizierungsstellen`
	- `Zertifikate`
	- Rechtsklick auf `Zertifikate`
	- `Alle Aufgaben`, `Importieren`
	- Es öffnet sich ein Zertifikatimport-Assistent
	- `Weiter`
	- Zertifikat wählen, `Durchsuchen`, Zertifikat auswählen, `Fertig stellen`
	- `Windows neustarten`, damit Änderungen übernommen werden


### 5.3. Zertifikate unter MacOS einfügen
- `tls_ca.crt` Zertifikat im Datei-Explorer aufrufen
- Zertifikat mit einem Doppelklick öffnen
- Es öffnet sich die `Schlüsselbundverwaltung`
- `System` als Schlüsselbund für alle Benutzer
- `Anmeldung` als Schlüsselbund für einen Benutzer
- `Zertifikate`
- `Drag and Drop` des Zertifikates
- importiertes Zertifikat im Schlüsselbund aufrufen
- Doppelklick auf das Zertifikat, um es zu öffnen
- `Vertrauen`
- `Beim Verwenden dieses Zertifikats:` Option `Immer vertrauen` einstellen
- ggf. mit Passwort bestätigen
- Browser schließen und neu öffnen.


### 5.4. Zertifikate unter Android einfügen
- Die Menüpunkte können je nach Android-Version und Herstelller variieren !
- `Zertifikat auf Gerätespeicher` von Android `kopieren`
	- Anleitung aus `Punkt 4.` "Das Zertifikat vom Pi hole herunterladen" folgen
	- die `tls_ca.crt` auf Android in den Gerätespeicher kopieren
- `Einstellungen` öffnen
- `Sicherheit & Datenschutz`
- `weitere Sicherheitseinstellungen`
- den Reiter Zertifikate oder Berechtigungsspeicher finden
- `Von Gerätespeicher installieren` auswählen
- `CA-Zertifikat`
- Risiken lesen, `bestätigen` und `Trotzdem installieren` klicken
- Zertifikat auswählen
- CA-Zertifikat wurde erfolgreich installiert


## 6. Prüfen, ob Zertifikat gültig und aktiv ist
- im Browser:
	- Auch wenn der Browser das Zertifikat nicht als vertrauenswürdig einstuft, besteht trotzdem eine HTTPS-Verbindung.
	- Im Browser die Seiteninformationen aufrufen (Vorhängeschloss-Symbol)
	- Zertifiakt-Details aufrufen
	- Angaben prüfen

- Serverseitig prüfen
	- Ab der Version v6.0 zeigt der Anmeldebildschirm von Pi hole eine Warnmeldung an, wenn die Verbindung eine unsichere HTTP-Verbindung ist.
- Zertifikat des Servers prüfen
```
$ openssl s_client -connect IP-Adresse:443 -showcerts
```


----------------------------------------------------------------------------------------------------------------
