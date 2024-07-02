# RaspberryPI & Netzwerk Projekte


# 1. [PI Hole](https://pi-hole.net/)


`Die Installation von PI-Hole ist auf den meisten gängigen Linux-Distibutionen möglich, allerdings beschränkt diese Anleitung sich auf die Installation von PI-Hole auf einem RapberryPI`

`Anleitung verfasst am 15.5.2024`


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
- Der RaspberryPI benötigt unbedingt eine im Router (DHCP-Server), reservierte IP-Adresse !



## C. [Firewall Koniguratiuon](https://wiki.ubuntuusers.de/ufw/)

```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw allow 80,443,53,853/udp
$ sudo ufw allow 80,443,53,853/tcp
```

- Wenn PI-Hole auch als DHCP Server genutzt wird:
```
$ sudo ufw allow 67/udp
$ sudo ufw allow 67/tcp
```

- Um Web-Dashboard auch von anderen PCs aufrufen zu können:
```
$ sudo ufw allow http
```

- Wenn auf den PI über SSH zugegriffen werden soll:
```
$ sudo ufw allow ssh
```


- Firewall aktivieren:
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

- Das Passwort des Web-Interface ändern:
```
$ sudo pihole -a -p
```


## E. Web-Interface

> http://IP-Adresse/admin

> http://pi.hole/admin


## F. PI als DNS Server im Router/ in einzelnen Geräten einstellen

- In einem Router, z.B. in der FritzBox den PI als DNS Server einstellen.
- Alternativ den PI nur in ausgewählten Geräten einstellen.
- Dafür entweder im Router oder in einzelnen Geräten die IP-Adresse des RaspberryPIs als DNS eintragen.


----------------------------------------------------------------------------------------------------------------
