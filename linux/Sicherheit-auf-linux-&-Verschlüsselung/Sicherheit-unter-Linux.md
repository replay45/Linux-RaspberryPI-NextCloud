# Sicherheit auf Linux

`Anleitung erstellt am 11.6.2025, zuletzt bearbeitet am 21.12.2025`

`Anleitung für Debian-basierte Systeme geeignet`


## Inhaltsverzeichnis
1. Sicherheit auf Linux - grundlegende Sicherheitstipps
2. Benötigt man einen Virenscanner für Linux ?
    - Was tun, wenn man trotzdem einen Virenscanner auf Linux nutzen möchte ?
3. [Firewall](https://de.wikipedia.org/wiki/Firewall)
4. [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot)
5. DNS Server ändern - [Cloudflare DNS](https://www.cloudflare.com/)
    - Was ist ein DNS-Server ?
    - Wieso den DNS-Server ändern ? 
    - DNS Server ändern
6. Rootkit-Prüfung


- Mehr zu `Verschlüsselung unter Linux` in diesem Ordner unter [Verschlüsselung auf Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)


----------------------------------------------------------------------------------------------------------------


# 1. Sicherheit auf Linux - grundlegende Sicherheitstipps

### die Grundlagen & allgemeies Verhalten
- Absolute Sicherheit gibt es nie ! - Aufklärung ist der beste Schutz !
- Man sollte immer den gesunden Menschenverstand einschalten.
- Ein gewisses Maß an Skepsis ist immer gut.
- Sicherung von Systemen gegen Diebstahl oder Manipulationen.
- Keine unbekannten/ vermeindlich gefundenen USB-Sticks verwenden !

### Datenschutz & Tracking
- Datenminimierung: Nur personenbezogene Daten angeben, wenn unbedingt notwendig.
- Man sollte sich nur in online-Accounts einloggen, wenn notwendig.
- Die `Werbe-ID löschen` & `personalisierte Werbung deaktivieren`, um Tracking zu minimieren.
- Cloud-Dienste meiden - eigene Cloud, wie Nextcloud oder NAS im Heimnetz nutzen.
- Sichere und datenschutzfreundliche DNS-Server nutzen.

### Browser & Verhalten im Internet
- Acht geben, auf was im Internet heruntergeladen wird und welche Webseiten besucht werden.
- Einen sicheren Browser, wie [Firefox](https://www.mozilla.org/de/firefox/new/) oder den [Brave-Browser](https://brave.com/de/) verwenden.
- Browser datenschutzfreundlich einstellen und Cookies von Dritt-Anbietern blockieren.
- Datenschutzorientierte Suchmaschine verwenden, z.B. [Startpage](https://www.startpage.com/), [DuckDuckGo](https://duckduckgo.com/) oder [Ecosia](https://www.ecosia.org/)
- AdBlocker verwenden - [uBlock Origin](https://ublockorigin.com/de) & [Privacy Badger](https://privacybadger.org/)

### E-Mail
- Vorsicht vor Scam-E-Mails und schädlichen Anhängen.
- Einen sicheren E-Mail-Client ohne Tracker nutzen - [Thunderbird](https://www.thunderbird.net/de/) & [K-9](https://k9mail.app/).

### Authentifizierung & Verschlüsselung
- Festplatten-/ Geräteverschlüsselung aktivieren & Passwortsperre nutzen.
- Passwortmanager mit starken Passwörtern nutzen - empfohlen: [KeePassXC](https://keepassxc.org/) oder [BitWarden](https://bitwarden.com/de-de/)
- 2‑Faktor-Authentifizierung nutzen - [2FAS](https://2fas.com/)

### Software & Daten schützen
- [Open Source](https://de.wikipedia.org/wiki/Open_Source) Software bevorzugen.
- Regelmäßiges Installieren von Updates.
- Nicht mehr benötigte Programme entfernen.
- Backups erstellen.

### Betriebssystem spezifisch (hier Linux)
- Firewall: richtig mit [ufw](https://wiki.ubuntuusers.de/ufw/) konfigurieren und alle nicht benötigten Ports geschlossen halten.
- Keine unbekannten Befehle im Terminal ausführen.


#### Mehr zu `sicher-&-datenschutzorientiert-surfen` unter [ethical hacking & Cyber Security/Cyber-Security/sicher-&-datenschutzorientiert-surfen](https://github.com/replay45/ethical-hacking-und-cybersecurity/tree/main/cyber-security)


----------------------------------------------------------------------------------------------------------------


# 2. Benötigt man einen Virenscanner für Linux (Desktop-Systeme) ?

Es wird `nicht unbedingt` ein Virenscanner für Linux benötigt, da Linux dank seiner Architektur bereits als recht sicher gilt.
Durch die geringe Verbreitung von Linux bei Desktop Systemen gibt es dementsprechend wenig Viren.
Stattdessen sollte man lieber die `grundlegenden Sicherheitstipps (Punkt 1.) befolgen` und die [Firewall](https://de.wikipedia.org/wiki/Firewall) aktivieren.
Wenn man sich also an die wichtigsten Verhaltens-Regeln hält, um sein System sicher zu halten, `Software nur aus vertrauenswürdigen Quellen` nutzt und das System mit den neusten `Updates` versorgt, ist `in der Regel für private Anwender kein Virenscanner nötig`.


### Was tun, wenn man trotzdem einen Virenscanner auf Linux nutzen möchte ?
- Wenn man trotzdem nicht auf einen Virenscanner für Linux verzichten möchte, empfiehlt sich [Clam AV](https://www.clamav.net/).
- Das [Open Source](https://de.wikipedia.org/wiki/Open_Source) Programm kann im Terminal genutzt werden, aber es kann auch optional die Benutzeroberfläche installiert werden.


### [Installation & Nutzung von Clam AV](https://www.kali.org/tools/clamav/)
```
$ sudo apt install clamav
```
```
$ clamscan -h
```
```
$ sudo freshclam
```


----------------------------------------------------------------------------------------------------------------


# 3. [Firewall](https://de.wikipedia.org/wiki/Firewall)

- Was ist eine [Firewall](https://de.wikipedia.org/wiki/Firewall) ?
    - Eine Firewall kontrolliert den Datenfluss zwischen einem internen und einem externen Netzwerk und blockiert unerwünschten Datenverkehr.
    - Die Funktion einer Firewall simpel erklärt: Wenn eine Anfrage aus dem internen Bereich gesendet und von extern beantwortet wurde, (z.B. einen Aufruf einer Webseite) dann können die entsprechenden Datenpakete die Firewall passieren. Wenn jedoch Datenpakete von extern gesendet werden, ohne, dass danach gefragt wurde, sollte die Firewall diese blockieren.
    - Zusammenfassend schützt eine Firewall vor unerwünschtem Datenverkehr in einem Netzwerk, um die Systeme zu schützen.


[Firewall Manager - ufw](https://wiki.ubuntuusers.de/ufw/) (Debian-basierte Distributionen):

- Installation & Status
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw status verbose
$ sudo ufw reload
```

- Firewall aktivieren/ deaktivieren/ zurücksetzen
```
$ sudo ufw enable
$ sudo ufw disable
$ sudo ufw reset
```

- Portfreigabe - App-list
```
$ sudo ufw app list
$ sudo ufw allow PAKETNAME
$ sudo ufw allow ssh
```

- klassische Portfreigabe 
```
$ sudo ufw allow <port> <optional: protocol>

$ sudo ufw allow 53
$ sudo ufw allow 53/tcp
$ sudo ufw allow 53/udp
```

- Portfreigabe für eine bestimmte IP-Adresse
```
$ sudo ufw allow from IP-ADRESSE
$ sudo ufw allow from 203.0.113.4

$ sudo ufw allow from IP-ADRESSE to any port PORT
$ sudo ufw allow from 192.168.2.10 to any port 22
```

- Regeln entfernen
```
$ sudo ufw status numbered
$ sudo ufw delete NUMBER
$ sudo ufw delete 2

$ sudo ufw delete allow PORTFREIGABE
$ sudo ufw delete allow 22
$ sudo ufw delete allow http

$ sudo ufw delete deny PORTFREIGABE
$ sudo ufw delete deny 22
$ sudo ufw delete deny http
```

- Anfragen verbieten
```
$ sudo ufw deny <port> <optional: protocol>

$ sudo ufw deny 53
$ sudo ufw deny 53/tcp
$ sudo ufw deny 53/udp
```

- Logging
```
$ sudo ufw logging on 
$ sudo ufw logging STUFE 
$ sudo ufw logging off 
```


----------------------------------------------------------------------------------------------------------------


# 4. [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot)

### Was ist [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) ?
- Secure Boot ist eine Sicherheitsfunktion (Integritätsschutz), die sicherstellt, dass beim Boot-Vorgang vom Betriebssystem nur vertrauenswürdige Software geladen wird.
- Das Ziel ist, einen Schutz vor Manipulationen am Betriebssystem zu implementieren, um das Laden von Schadcode oder veränderten Bootloadern zu verhindern.
- Technisch funktioniert das über eine digitale Signatur, wodurch nicht signierte oder veränderte Software blockiert wird.




### Einrichtung von [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) - (Debian-basierte Distributionen)

*folgt in Kürze*


----------------------------------------------------------------------------------------------------------------


# 5. DNS Server - [Cloudflare DNS](https://www.cloudflare.com/)

- `mehr zu DNS-Servern` in diesem Repository unter [Raspberry-Pi/Pi-hole](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi)
- Hinweis:
    - Das Ändern des DNS-Servers zu einem sicheren Anbieter, verhindert ohne Implementierung einer Verschlüsselung oder Signierung keine [Man-in-the-Middle-Angriffe](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) oder Tracking !

## Was ist ein [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) ?
Ein DNS-Server `übersetzt Domainnamen` wie "google.de" in [IP-Adressen](https://de.wikipedia.org/wiki/IP-Adresse), denn Domains sind für uns Menschen, im Gegensatz zu IP-Adressen, einfacher zu merken.
Außerdem gibt es nicht genügend verfügbare IPv4-Adressen, daher können diese sich auch ändern, was jedoch dank der DNS-Server kein Problem ist.
- [Cloudflare - Was ist ein DNS-Server](https://www.cloudflare.com/de-de/learning/dns/what-is-a-dns-server/)


## Wieso den DNS Server ändern ?
- Standardmäßig nutzt man die DNS-Server des Internetanbieters, diese sind jedoch häufig eher langsamer und wenn man `verhindern` möchte, dass der `Internetprovider bzw. Mobilfunkanbieter einsehen kann, welche Domains man aufruft`, ist es sehr ratsam, die DNS-Server von [Cloudflare](https://www.cloudflare.com/) zu nutzen.
- Den DNS-Server kann man auf allen gänigen Desktop-Betriebsystemen sowie auf dem Smartphone, als auch in vielen gänigen Routern ändern.
- Wie man seinen eigenen kleinen DNS-Server mit [Pi hole](https://pi-hole.net/) erstellen kann, wird unter [Raspberry-Pi/Pi-hole](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi) gezeigt.
- [Cloudflare](https://www.cloudflare.com/) legt dabei den `Fokus` auf `Datenschutz & Sicherheit`, verspricht `kein Logging` von Daten die zur Identifizierung genutzt werden können und bietet dennoch sehr `schnelle DNS-Server`.


### Hier die Vorteile der [Cloudflare](https://www.cloudflare.com/) DNS-Server
- `Vorteile`
    - empfehlenswerter Anbieter, weite Verbreitung
    - Geschwindigkeit (schnelle Server)
    - Server auf der ganzen Welt
    - Fokus auf Datenschutz & Sicherheit
    - kein Logging von IP-Adressen
    - ideal für private Nutzer & Haushalte


## DNS Server ändern
- Linux (mit [GNOME](https://www.gnome.org/)):
    - Der manuelle Eintrag für den DNS-Server muss für jede WLAN / LAN Verbindung manuell vorgenommen werden.
    - Einstellungen öffnen
    - WLAN / LAN Verbindung auswählen.
    - Eigenschaften der Verbindung auswählen.
    - Zu dem Reiter `IPv4` wechseln, `"Automatic" deaktivieren` und IP-Adresse des DNS-Servers einfügen. ([Cloudflare DNS](https://www.cloudflare.com/): `1.1.1.1, 1.0.0.1`).
    - Nun zu dem Reiter `IPv6` wechseln und ebenfalls `"Automatic" deaktivieren` und optional IPv6 des DNS-Servers einfügen.

- Router:
    - Wenn man nicht für jedes Gerät den DNS-Server einzeln einstellen möchte, kann man dies auch im Router tun.
    - Je nach Hersteller und Modell können die Optionen abweichen (Vodafone Easy-Boxen unterstützen das Ändern des DNS-Servers in der Regel nicht).
    - Um den DNS-Server zu ändern, die Option finden, wo der primäre und der sekundäre-DNS-Server festgelegt werden können. Dabei können sowohl der primäre- als auch der sekundäre-DNS-Server unterschiedliche Server vom gleichen Anbieter sein, oder der sekundäre Server kann wahlweise auch von dem primären Anbieter abweichen, um eine hohe Ausfallsicherheit zu gewährleisten.
    - Einige Router, wie z.B. die [FritzBox](https://avm.de/) unterstützen auch `DNS-Verschlüsselung (DoT)`, um die Anfragen sowie die Antworten zu verschlüsseln, damit [Man-in-the-Middle-Angriffe](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) und Tracking durch Dritte verhindert werden können.


## mobile Geräte (Laptops) - optimalen DNS-Server nutzen & ggf. DNS-Verschlüsselung
- Bei mobilen Geräten, wie Laptops, die auch außerhalb des Heimnetzes genutzt werden, sollte ein geeigneter Anbieter verwendet werden.
- Standardmäßig gibt es mit den Bordmitteln unter Linux mit [GNOME](https://www.gnome.org/) keine Möglichkeit, den DNS-Server für alle Verbindungen global einzustellen oder eine DNS-Verschlüsselung zu aktivieren.
- Die wohl einfachste Möglichkeit könnte eine VPN-Verbindung in das Heimnetzwerk sein, um die gewünschten DNS-Server mit ggf. DoT-Verschlüsselung z.B. über den Router wie die [FritzBox](https://avm.de/) zu nutzen oder den eigenen DNS-Server (z.B. Pi hole) zu nutzen.
- Eine VPN-Verbindung bietet u.a. auch den Vorteil, dass der eigene Netzwerkverkehr bei unsicheren oder fremden WLANs verschlüsselt wird und man auf andere Geräte aus dem Heimnetz zugreifen kann.
- Eine andere Möglichkeit wäre z.B. [Pi hole](https://pi-hole.net/) über Dyn-DNS aus dem Internet erreichbar zu machen/ extern zu hosten, um Pi hole als DNS-Server einzustellen (nur für sehr erfahrene Nutzer geeignet).


Das Aufsetzen von [Pi hole](https://pi-hole.net/) wird unter [Raspberry-Pi/Pi-hole](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi) gezeigt.


----------------------------------------------------------------------------------------------------------------


# 6. Rootkit-Prüfung


### Was ist ein Rootkit
- Ein Rootkit ist Schadsoftware (Maleware), die versucht sich mit Administratorrechten tief im System zu verstecken, um z.B. Backdoors zu installieren.
- Die Gefahr dabei ist, dass Rootkits auch Kernel-Code ändern könnten und sich vor der Erkennung schützen.
- Dabei werden Rootkits in der Regel entweder durch Maleware-Infektion oder durch Angreifer nach erfolgreicher Kompromitierung installiert.

### Erkennung von Rootkits
- Es können z.B. Live-Systeme verwendet werden, von denen aus gebooet wird und dann werden anschließend Diagnose-Tools verwendet, um Rootkits zu erkenen.
- Mit einer Memory-Dump-Anmalyse können auch Speicherinhalte auf verdächtige Aktivitäten geprüft werden.
- Mithilfe von `chkrootkit`, einem Shell-Skript, können Systeme nach bekannten Signaturen von Rootkits durchsucht werden.
    - Eine Prüfung mit chkrootkit ist niemals eine Garantie auf absolute Sicherheit, denn das Shell-Skript kann nur nach bekannten Siganturen von Rootkits suchen, noch unbekannte Rootkits können übersehen werden.
```
$ sudo apt update
$ sudo apt install chkrootkit
$ sudo chkrootkit
```


### Entfernung von Rootkits
- Das Entfernen von Rootkits ist extrem schwierig, nur das `Formatieren/Überschreiben` der Festplatte und eine `Neuinstallation` sind eine angemessene Lösung.


----------------------------------------------------------------------------------------------------------------
