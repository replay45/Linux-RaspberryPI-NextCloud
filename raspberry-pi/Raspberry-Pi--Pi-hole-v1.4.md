# [Pi hole](https://pi-hole.net/)


`Anleitung verfasst am 15.5.2024, zuletzt bearbeitet 5.5.2025`


## Inhaltsverzeichnis
1. Was ist ein DNS-Server & Was ist [Pi hole](https://pi-hole.net/) ?
2. [Pi hole](https://pi-hole.net/) installieren, einrichten, Firewall & updaten
3. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für Web-Interface - über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) (ohne Domain)
4. Backups von Pi hole erstellen
5. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC
6. Welchen DNS Upstream-Server sollte man wählen ?
7. mobile Geräte: gewünschte DNS-Server wie von [Cloudflare](https://www.cloudflare.com/) oder [Pi hole](https://pi-hole.net/) außerhalb des Heimnetzes nutzen können
8. DNS-Filterlisten (Blocklisten) & Domains blacklisten/whitelisten
9. Empfehlungen für Blocklisten
10. DNS-Filterlisten (Blocklisten) selbst erstellen


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


# 3. [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für Web-Interface - über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) (ohne Domain)

`Anleitung mit Version 6.0 von Pi hole getestet`

## 3.1. Einschränkungen
- Wenn man KEINE Domain für Pi hole nutzt, sondern diesen nur über die IP-Adresse betreibt, dann bleibt nur die Möglichkeit HTTPS über das `selbst-signierte Zertifikat` von Pi hole selber zu nutzen.
- Das bedeutet allerdings, dass der Browser jedes mal eine Warnmeldung anzeigen wird, dass die Verbindung nicht sicher sei, das liegt daran, dass der Browser dem Zertifikat nicht "vertraut", aber die `HTTPS-Verbindung ist trotzdem gegeben`.


## 3.2. Wichtige Informationen zum Zertifikat für HTTPS
- Um grundsätzlich eine HTTPS-Verschlüsselung nutzen zu können, benötigt man ein SSL-Zertifikat auf dem Server.
- Pi hole selber erstellt ab der Version 6.0 ein Zertifikat, das genutzt werden kann, um HTTPS nutzen zu können.
- Da das `Zertifikat "selbst signiert" ist`, wird es `nicht als vertrauenswürdig eingestuft`. Das ist allerdings `KEIN Problem`, da die `HTTPS-Verbindung trotzdem gegeben` ist.
- Im [Firefox](https://www.mozilla.org/de/firefox/new/)-Browser kann man eine sogenannte Ausnahme hinzufügen, damit die Warnmeldungen nicht mehr erscheinen.
- In Browsern, die auf Chromium basieren, sind nur temporäre Ausnahmen möglich.


## 3.3. Das Zertifikat von Pi hole erneuern (nur bei Bedarf nötig)
```
$ sudo rm /etc/pihole/tls* && sudo service pihole-FTL restart
```


## 3.4 Prüfen, ob Zertifikat gültig und aktiv ist
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


# 4. Backups von Pi hole erstellen

`Anleitung mit Version 6.0 von Pi hole getestet`

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


# 5. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC

`Anleitung mit Version 6.0 von Pi hole getestet`

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


# 6. Welchen DNS Upstream-Server sollte man wählen ?
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


# 7. mobile Geräte: gewünschte DNS-Server wie von Cloudflare oder [Pi hole](https://pi-hole.net/) außerhalb des Heimnetzes nutzen können

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


# 8. DNS-Filterlisten (Blocklisten) & Domains blacklisten/whitelisten

## A. Blocklisten finden
- Unter [filterlists.com](https://filterlists.com/) kann man die unterschiedlichsten Filterlisten für diverse Ad-Blocker für den Browser und natürlich auch für den Pi hole finden.
- Es gibt die Möglichkeit, einen Filter zu setzen, womit nur noch die Filterlisten für das gewünschte System angezeigt werden.
- Nun kann man sich nach Belieben Blocklisten kopieren.
- Weitere Blocklisten sind unter dem Repository [DNS-Filterlisten](https://github.com/replay45/DNS-Filterlisten) zu finden.
- Unter dem Punkt `DNS-Filterlisten (Blocklisten) erstellen` wird erklärt, wie man seine eigene Blockliste erstellen kann.

## B. Blocklisten hinzufügen
- Um eine neue Blockliste hinzuzufügen, im Web-Interface des Pi holes unter `Adlists` die URL der Filterliste einfügen und optional eine Beschreibung hinzufügen.
- Nun auf `Add` klicken und unter `Tools / Update-Gravity` ein Filterlisten-Update ausführen, oder folgenden Befehl ausführen: `$ pihole -g`

## C. Status von Blocklisten einsehen
- Unter `Lists` kann der Status der einzelnen Listen eingesehen werden.
	- Eine Liste auswählen, und prüfen, ob eine Zahl hinter `Number of entries` steht.
	- Hinter `Number of non-domains` sollte idealerweise `0` stehen.

## D. einzelne Domains blacklisten/whitelisten
- Unter `Domains` können einzelne Domains nach Bedarf auf die Blacklist gesetzt werden, falls diese nicht in einer Blockliste enthalten sind.
- Außerdem können auch Domains auf die Whitelist gesetzt werden, falls diese unerwünscht durch eine Blockliste blockiert werden.


----------------------------------------------------------------------------------------------------------------


# 9. Empfehlungen für Blocklisten
- Eigene Blocklisten finden sich in dem Repository [github.com/replay45/DNS-Filterlisten](https://github.com/replay45/DNS-Filterlisten)
	- Hier sind unterschiedliche Blocklisten, die u.a. Werbedomains oder Trackingdomins enthalten.
	- Diese wurden aus eigener Erfahrung zusammengetragen und das hinzufügen der Listen in den eigenen Pi hole kann sehr hilfreich sein.

- Empfehlung (Standardblockliste von pi hole)
	- Beim Installationsvorgang von Pi hole wird bereits gefragt, ob diese Liste automatisch hinzugefügt werden soll.
	- Das Hinzufügen der Standardblockliste von Steven Black ist dringend empfohlen und ist eine gute Basis für den Einstieg.
	- [Standardblockliste vom pi hole (Steven Black)](https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts)

- Empfehlungen von Dritten
	- [watchlist-internet.at - phishing, fake shops, malicious platforms](https://raw.githubusercontent.com/stonecrusher/filterlists-pihole/master/watchlist-internet-ph.txt)
	- [Steven Blacks Trackers - ads](https://raw.githubusercontent.com/deathbybandaid/piholeparser/master/Subscribable-Lists/ParsedBlacklists/Steven-Blacks-Trackers.txt)
	- [anti-AD - (chinese) - ads, privacy, religious](https://raw.githubusercontent.com/privacy-protection-tools/anti-AD/master/anti-ad-domains.txt)
	- [AdAway default blocklist (ads) - Blocking mobile ad providers and some analytics providers](https://adaway.org/hosts.txt)
	- [1024 Hosts (ads) - Hosts file, used to block some pop-up ads and casino ads](https://raw.githubusercontent.com/Goooler/1024_hosts/master/hosts)
	- [Pi-hole Parser - Italy](https://raw.githubusercontent.com/deathbybandaid/piholeparser/master/Subscribable-Lists/CountryCodesLists/Italy.txt)
	- [ABP Japanese Filters - ads](https://raw.githubusercontent.com/deathbybandaid/piholeparser/master/Subscribable-Lists/ParsedBlacklists/ABP-Japanese-Filters.txt)

- Weitere Empfehlungen
	- Auf der Github Seite von Steven Black unter [github.com/StevenBlack/hosts](https://github.com/StevenBlack/hosts/blob/master/readme.md) finden sich viele weitere Empfehlungen.


----------------------------------------------------------------------------------------------------------------


# 10. DNS-Filterlisten (Blocklisten) selbst erstellen

- Man kann selbst Blocklisten für [Pi hole](https://pi-hole.net/) erstellen, um das Blockieren von Domains nach den eigenen Bedürfnissen anzupassen.
- Grundsätzlich könnte man auch die Domains einzeln unter dem Punkt "Domains" hinzufügen, wenn man jedoch eine gewisse Anzahl an Domains in die Blockliste mit aufnehmen möchte, ist das Erstellen einer eigenen Blockliste einfacher und komfortabler zu managen.
- Zusätzlich kann man die Blockliste auch anderen zur Verfügung stellen. Üblicherweise erfolgt so etwas z.B. über [Github](https://github.com/) oder ähnlichen Diensten.
- Man kann die Blockliste aber auch lokal auf dem Server von Pi hole speichern und dann einbinden, dafür muss man jedoch noch ein paar Dienste konfigurieren.

### DNS-Filterlisten (Blocklisten) sind unter dem Repository [github.com/replay45/DNS-Filterlisten](https://github.com/replay45/DNS-Filterlisten) zu finden.


## Blockliste erstellen
- Textdatei erstellen
- Domains hinzufügen
- Datei als `.txt` speichern


## Wichtige Hinweise zur Blockliste:
- Für Pi hole ist eine reine Domain-Liste ausreichend.
- Es werden keine IP-Adressen in der Liste benötigt.
- Keine Leerzeichen oder zusätzliche Zeichen (wie http:// oder /) benötigt.
- Nur eine Domain pro Zeile.
- Keine Wildcards (wie *.example.com), denn Pi hole arbeitet nur mit exakten Domains oder Subdomains.
- Kommentare können mit `#` gekennzeichnet werden, Pi hole (Gravity) überspringt diese automatisch.

Beispiel:
```
example.com
ads.exmple.net
beispiel.example.org
```


## Blockliste hosten
- Hinweis:
	- Pi hole kann nur Listen laden, die über [HTTP](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol)/ [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) erreichbar sind.

- Blockliste lokal auf Pi hole Server speichern:
	- dafür wird ein Webserver wie z.B. [Nginx](https://nginx.org/) benötigt
	- dort in den entsprechenden Pfad des Webservers die Text-Datei (Blockliste) einfügen
	- Webseite über IP-Adresse im Browser aufrufen und URL kopieren
	- bei Pi hole als Blockliste hinzufügen
	- den Reiter `Tools` öffnen und `Update Gravity` ausführen

- Blockliste online verfügbar machen (z.B. Github)
	- `öffentliches Repository` anlegen
	- Text-Datei hochladen
	- Blockliste öffnen `RAW` auswählen
	- URL (RAW) zur Blockliste kopieren
	- bei Pi hole als Blockliste hinzufügen
	- den Reiter `Tools` öffnen und `Update Gravity` ausführen


----------------------------------------------------------------------------------------------------------------
