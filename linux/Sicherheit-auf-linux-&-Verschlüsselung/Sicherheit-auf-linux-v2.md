# Sicherheit auf Linux

`Anleitung zuletzt bearbeitet am 19.11.2024`

## Inhaltsverzeichnis
1. Benötigt man einen Virenscanner für Linux ?
2. Firewall
3. DNS Server ändern - [Cloudflare DNS](https://www.cloudflare.com/)
    - Was ist ein DNS-Server ?
    - Wieso den DNS Server ändern ? 
    - DNS Server ändern


----------------------------------------------------------------------------------------------------------------


# 1. Benötigt man einen Virenscanner für Linux ?


Es wird __nicht unbedingt__ ein Antivirenscanner für Linux benötigt, da Linux bereits als recht sicher gilt.
Durch die geringe Verbreitung von Linux bei Desktop Systemen, gibt es dementsprechend wenig Viren.


### Stattdessen sollte man:
Viel wichtiger ist es, die Firewall richtig zu konfigurieren und alle nicht benötigten Ports geschlossen zu halten. Hierfür kann z.B. [ufw](https://wiki.ubuntuusers.de/ufw/) genutzt werden.
Außerdem ist es extrem wichtig, das Ausführen von `unbekannten Befehlen im Terminal zu vermeiden` und darauf Acht zu geben, was aus dem `Internet heruntergeladen` wird.
Auch das `regelmäßige Installieren von Updates` ist wichtig.

Man sollte zudem immer auf das `Verhalten im Web`, also welche Webseiten und Links man besucht und auf `E-Mails und ihre Anhänge` achten.
Ebenfalls sollte man auf seine Online-Accounts achten und einen `Passwortmanager`, wie z.B. [KeePassXC](https://keepassxc.org/) oder [BitWarden](https://bitwarden.com/de-de/) nutzen.


- Wenn man trotzdem nicht auf einen Virenscanner für Linux verzichten möchte, empfiehlt sich [Clam AV](https://www.clamav.net/).
- Das Open-Source Programm kann im Terminal genutzt werden, aber es kann auch optional die Benutzeroberfläche installiert werden.


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


# 2. Firewall


[Firewallmanager - ufw](https://wiki.ubuntuusers.de/ufw/):

- Installation & Status
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw status verbose
$ sudo ufw reload
```

- Firewall aktivieren/ deaktivieren/ reseten
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


# 3. DNS Server - [Cloudflare DNS](https://www.cloudflare.com/)

- `mehr zu DNS-Servern` in diesem Repository unter [Raspberry-Pi/ Raspberry-Pi-Netzwerk-Projekte](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi)


### Was ist ein DNS Server ?

Ein DNS-Server `übersetzt Domainnamen` wie "google.de" `in IP-Adressen`, denn Domains sind für uns Menschen, im Gegensatz zu IP-Adressen, einfacher zu merken.
Außerdem gibt es nicht genügend verfügbare IPv4 Adressen, daher können diese sich auch ändern, was jedoch dank der DNS-Server kein Problem ist.
- [Cloudflare - Was ist ein DNS-Server](https://www.cloudflare.com/de-de/learning/dns/what-is-a-dns-server/)


## Wieso den DNS Server ändern ?

- Standardmäßig nutzt man die DNS-Server des Internetanbieters, diese sind jedoch häufig eher langsamer und wenn man `verhindern` möchte, dass der `Internetprovider bzw. Mobilfunkanbieter einsehen kann, welche Domains man aufruft` ist es sehr ratsam, die DNS-Server von [Cloudflare](https://www.cloudflare.com/) zu nutzen.
- Den DNS-Server kann man auf allen gänigen Desktop-Betriebsystemen, sowie auf dem Smartphone, als auch in vielen gänigen Routern ändern.

- Wie man seinen eigenen kleinen DNS-Server mit Pi-hole erstellen kann, wird unter [Raspberry-Pi/RaspberryPI-Netzwerk-Projekte](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi) gezeigt.

- [Cloudflare](https://www.cloudflare.com/) legt dabei den `Fokus` auf `Datenschutz & Sicherheit` und bietet dennoch sehr `schnelle DNS-Server`.

### Hier die Vorteile der [Cloudflare](https://www.cloudflare.com/) DNS-Server

- `Vorteile`
    - empfehlenswerter Anbieter, weite Verbreitung
    - Geschwindigkeit (schnelle Server)
    - Server auf der ganzen Welt
    - Fokus auf Datenschutz & Sicherheit
    - kein Logging
    - ideal für private Nutzer & Haushalte


### DNS Server ändern

- Linux:
    - Der manuelle Eintrag für den DNS-Server muss für jede WLAN / LAN Verbindung manuell vorgenommen werden.
    - Einstellungen öffnen
    - WLAN / LAN Verbinung auswählen.
    - Eigenschaften der Verbindung auswählen.
    - Zu dem Reiter `IPv4` wechseln, `"Automatic" deaktivieren` und IP-Adresse des DNS-Servers einfügen. ([Cloudflare DNS](https://www.cloudflare.com/): `1.1.1.1, 1.0.0.1`).
    - Nun zu dem Reiter `IPv6` wechseln und ebenfalls `"Automatic" deaktivieren` und optional IPv6 des DNS-Servers einfügen.

- Router:
    - Wenn man nicht für jedes Gerät den DNS-Server einzeln einstellen möchte, kann man dies auch im Router tun.
    - Je nach Hersteller und Modell können die Optionen abweichen (Vodafone Easy-Boxen unterstützen das Ändern des DNS-Servers in der Regel nicht.)
    - Um den DNS-Server zu ändern, die Option finden, wo der primäre und der sekundäre-DNS-Server festgelegt werden können. Dabei können der primäre- als auch der sekundäre- DNS-Server unterschildliche Server vom gleichen Anbieter sein, oder der sekundäre Server kann wahlweise auch von dem primären Anbieter abweichen, um eine hohe Ausfallsicherheit zu gewährleisten.

----------------------------------------------------------------------------------------------------------------
