# [Raspberry Pi](https://www.raspberrypi.com/) & Netzwerk Projekte


`Anleitung verfasst am 15.5.2024`

`zuletzt bearbeitet am 12.11.2024`


## Inhaltsverzeichnis
1. Was ist ein DNS-Server & Was ist [Pi hole](https://pi-hole.net/) ?
    - Was ist ein DNS-Server ?
    - Was ist [Pi hole](https://pi-hole.net/) ?
2. [Pi hole](https://pi-hole.net/) installieren, einrichten & updaten
    - [Installation](https://github.com/pi-hole/pi-hole/#one-step-automated-install)
    - IP-Adresse vergeben
    - [Firewall Konfiguratiuon](https://wiki.ubuntuusers.de/ufw/)
    - Einrichtung
    - Web-Interface
    - Pi als DNS Server im Router/ in einzelnen Geräten einstellen
    - Pi Hole Updates
3. Blocklisten (Filterlisten) hinzufügen
    - Blocklisten finden
    - Blocklisten hinzufügen
4. Domains - blacklisten/whitelisten
    - Domains auf die blacklist/whitelist setzen
5. Welchen DNS Upstream-Server soll ich wählen ?
    - Cloudflare](https://www.cloudflare.com/)
    - [Google](https://dns.google/)
    - Upstream DNS Server im Pi hole wechseln
6. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC
    - Wieso sollte man DNS Verschlüsselung/Signaturverfahren verwenden ?
    - Welche Verschlüsselung oder Signaturverfahren sollte man nutzen: DNSSEC / DoT / DoH ?
    - Aktivieren von DNSSEC im Pi hole


----------------------------------------------------------------------------------------------------------------


# 1. Was ist ein DNS-Server & Was ist [Pi Hole](https://pi-hole.net/) ?

### A. Was ist ein DNS-Server ?
Ein DNS-Server `übersetzt Domainnamen` wie "google.de" `in IP-Adressen`, denn Domains sind für uns Menschen, im Gegensatz zu IP-Adressen, einfacher zu merken.
Außerdem gibt es nicht genügend verfügbare IPv4 Adressen, daher können diese sich auch ändern, was jedoch dank der DNS-Server kein Problem ist.

[Cloudflare - Was ist ein DNS-Server](https://www.cloudflare.com/de-de/learning/dns/what-is-a-dns-server/)


### B. Was ist [Pi Hole](https://pi-hole.net/) ?
Pi-hole ist eine kostenlose Software, die als DNS-Server im Heimnetz verwendet werden kann.
Der Pi-hole hat viele Funktionen und kann Werbung sowie Tracker blockieren und optional als DHCP-Server eingerichtet werden.


----------------------------------------------------------------------------------------------------------------


# 2. [Pi Hole](https://pi-hole.net/) installieren, einrichten & updaten


`Die Installation von Pi hole ist auf den meisten gängigen Linux-Distibutionen möglich, allerdings beschränkt diese Anleitung sich auf die Installation von Pi-hole auf einem RapberryPI.`


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


## B. IP-Adresse vergeben
- Der Raspberry Pi benötigt unbedingt eine im Router (bzw. DHCP-Server), reservierte IP-Adresse, die sich nach dem Festlegen NICHT mehr automatisch ändern kann !


## C. [Firewall Konfiguratiuon](https://wiki.ubuntuusers.de/ufw/)

```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw allow 80,443,53,853/udp
$ sudo ufw allow 80,443,53,853/tcp
```

- Wenn Pi hole auch als DHCP Server genutzt wird:
```
$ sudo ufw allow 67/udp
$ sudo ufw allow 67/tcp
```

- Um das Web-Dashboard auch von anderen PCs aufrufen zu können:
```
$ sudo ufw allow http
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
- Das Passwort, was nun erscheint, notieren !

- Das Passwort des Web-Interfacees ändern:
```
$ sudo pihole -a -p
```


## E. Web-Interface

> http://IP-Adresse/admin

> http://pi.hole/admin


## F. Pi als DNS Server im Router/ in einzelnen Geräten einstellen

- In einem Router, z.B. in der FritzBox den PI als DNS Server einstellen.
- Alternativ den PI nur in ausgewählten Geräten einstellen.
- Dafür entweder im Router oder in einzelnen Geräten die `IP-Adresse` des Raspberry Pis (mit Pi-hole) `als DNS` eintragen.


## G. Pi-hole Updates

```
$ pihole -up
```

## H. Blockierung kurzzeitig unterbrechen
- Dafür einfach auf den Reiter `Disable Blocking` gehen und einen gewünschten Zeitraum wählen.


----------------------------------------------------------------------------------------------------------------

# 3. Blocklisten (Filterlisten) hinzufügen

## A. Blocklisten finden
- Unter [filterlists.com](https://filterlists.com/) kann man die unterschiedlichsten Filterlisten für diverse Ad-Blocker für den Browser und natürlich auch für den Pi-hole finden.
- Es gibt die Möglichkeit, einen Filter zu setzen, womit nur noch die Filterlisten für das gewünschte System angezeigt werden.
- Nun kann man sich nach Belieben Blocklisten kopieren.


## B. Blocklisten hinzufügen
- Um eine neue Blockliste hinzuzufügen, im Dashboard des Pi-holes unter `Adlists` die URL der Filterliste einfügen und optional eine Beschreibung hinzufügen.
- Nun auf `Add` klicken und unter `Tools / Update-Gravity` ein Filterlisten-Update ausführen, oder folgenden Befehl ausführen: `$ pihole -g`


----------------------------------------------------------------------------------------------------------------

# 4. Domains - blacklisten/whitelisten

- Unter `Domains` können einzelne Domains nach Bedarf auf die Blacklist gesetzt werden, falls diese nicht in einer Blockliste enthalten sind.
- Außerdem können auch Domains auf die Whitelist gesetzt werden, falls diese durch eine Blockliste blockiert werden.


----------------------------------------------------------------------------------------------------------------

# 5. Welchen DNS Upstream-Server soll ich wählen ?

Je nach Standort kann die Geschwindigkeit von Anbieter zu Anbieter variieren.

Stellvertretend sind hier die Vor-/ Nachteile von 2 Anbietern aufgelistet:


### [Cloudflare](https://www.cloudflare.com/) - IPv4: `1.1.1.1`, `1.0.0.1`

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
    - Google teilt Daten mit Partnern


### Wo kann ich den Upstream DNS Server im Pi hole wechseln ?
Unter dem Reiter `Settings` und `DNS` kann der Upstream DNS Server gewählt werden.


### DNS-Server bei mobilen Geräten außerhalb des Heimnetzwerkes
Es ist außerdem auch möglich, den `DNS Server bei mobilen Geräten` wie Laptops und Handys etc. zu ändern, um den gewünschten Anbieter nutzen zu können.
Das ist in der Hinsicht interessant, wenn man `verhindern` möchte, dass der `Internetprovider bzw. Mobilfunkanbieter einsehen kann, welche Domains man aufruft`.
Wie oben beschrieben ist [Cloudflare](https://www.cloudflare.com/) ein Anbieter, der den Fokus auf `Datenschutz & Sicherheit` legt.


### VPN ins Heimnetzwerk, um Pi hole nutzen zu können
Viele `moderne Router`, wie u.a. auch die [FritzBox](https://avm.de/), können als `eigener VPN-Server` genutzt werden.
Dabei stellt man vom Laptop, Tablet oder Handy eine VPN Verbindung in das eigene Heimnetz her und nutzt somit auch Pi hole als DNS-Server, sofern dieser als primärer DNS-Server im Router hinterlegt ist.
Außerdem kann man dann von überall auf seine Geräte im Heimnetzwerk zugreifen, wie z.B. auf einen NAS oder Homeserver.
Die VPN-Verbindung kann zudem auch in öffentlichen WLANs genutzt werden, um auch diese sicher nutzen zu können.
Dabei sollte man bedenken, dass alle Suchanfragen über den Router im Heimnetzwerk geleitet werden und dass dieser VPN-Server des eigenen Routers nicht unbedingt den klassischen VPN-Dienst eines externen Anbieters ersetzt.


Dafür eignet sich das moderne [WireGuard](https://www.wireguard.com/) Protokoll, was als besonders sicher gilt und dabei auch noch vergleichsweise wenige Ressourcen verbraucht, was wiederum energieeffizient ist.


----------------------------------------------------------------------------------------------------------------

# 6. DNS Verschlüsselung/Signaturverfahren - DoT / DoH / DNSSEC

## Wieso sollte man DNS Verschlüsselung/Signaturverfahren verwenden ?
- Schutz der `Privatsphäre` (nur DoT/DoH)
- Schutz vor `Tracking` (nur DoT/DoH)
- Schutz vor `Man-in-the-Middle-Angriffen`, bei denen die DNS-Anfragen `manipuliert` werden.
- Schutz vor `Umleitung` auf bösartige Webseiten.


## Welche Verschlüsselung oder Signaturverfahren sollte man nutzen: DNSSEC / DoT / DoH ?

1. DNSSEC (Domain Name System Security Extensions) - Signaturverfahren
    `Vorteile:`
    - Anfragen werden digital signiert (Schutz vor Manipulation)
    - Sicherstellung, dass DNS-Anfragen authentisch und unverändert sind (Schutz vor Manipulation)

    `Nachteile:`
    - Keine Verschlüsselung (Anfragen sind weiterhin im Klartext, werden nur signiert, KEIN Privatsphäre Schutz)
    - Viele Webseiten unterstützen noch kein DNSSEC
    - höherer Ressourcenbedarf


2. DoH (DNS over HTTPS)
    `Vorteile:`
    - sichere Verschlüsselung
    - Schützt die Privatsphäre (Schutz vor Tracking)
    - Schützt vor Manipulationen (Schutz vor `Man-in-the-Middle-Angriffen`)
    - DoH verwendet HTTPS und tarnt den DNS-Traffic als normalen Web-Traffic.

    `Nachteile:`
    - Anfragen werden NICHT digital signiert
    - Namensauflösung auf dem DNS-Server ist unverschlüsselt (lediglich der Traffic zwischen Client und DNS-Server wird verschlüsselt)



3. DoT (DNS over TLS)
    `Vorteile:`
    - sichere Verschlüsselung
    - Schützt die Privatsphäre (Schutz vor Tracking)
    - Schützt vor Manipulationen (Schutz vor `Man-in-the-Middle-Angriffen`)

    `Nachteile:`
    - Anfragen werden NICHT digital signiert
    - Namensauflösung auf dem DNS-Server ist unverschlüsselt (lediglich der Traffic zwischen Client und DNS-Server wird verschlüsselt)
    - DoT-Verschlüsselung ist als DNS-Traffic erkennbar (aber verschlüsselt).

4. `Fazit`
    - DNSSEC schützt vor Manipulation durch Signatur, bietet aber keine Verschlüsselung.
    - DoH-Verschlüsselung bietet mehr Datenschutz und Sicherheit (nur grundlegende Verschlüsselung auf dem Kommunikationsweg), Traffic als normalen Web-Traffic getarnt.
    - DoT-Verschlüsselung bietet mehr Datenschutz und Sicherheit (nur grundlegende Verschlüsselung auf dem Kommunikationsweg), Verschlüsselung als DNS-Traffic erkennbar.


## Aktivieren von DNSSEC im Pi hole
- Im Dashboard des Pi holes unter dem Reiter `Settings` und `DNS` die Option `USE DNSSEC` aktivieren
- Nun noch auf `Speichern` klicken.
- Im Log werden nun die signierten Anfragen mit `SECURE` gekennzeichnet.
- Domains die kein DNSSEC unterstützen, werden mit `INSECURE` gekennzeichnet.


----------------------------------------------------------------------------------------------------------------
