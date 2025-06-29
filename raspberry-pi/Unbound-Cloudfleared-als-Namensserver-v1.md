# Einrichtung von [Unbound](https://de.wikipedia.org/wiki/Unbound), [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) oder [Quad9](https://www.quad9.net/de/) als DNS-Resolver in Kombination mit [Pi hole](https://pi-hole.net/) - rekursiv/Forwarder & DNS-Verschlüsselung


`Anleitung verfasst am 20.5.2024, zuletzt bearbeitet 1.6.2025`


## Inhaltsverzeichnis
1. Einleitung
	- Ziel dieser Anleitung
	- Was ist ein rekursiver-Resolver oder Forwarder ?
2. Was sind die Anwendungsfälle, Vor- & Nachteile von [Unbound](https://de.wikipedia.org/wiki/Unbound) / [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) / [Quad9](https://www.quad9.net/de/)
	- 2.1 [Unbound](https://de.wikipedia.org/wiki/Unbound)
	- 2.2 [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/)
	- 2.3 [Quad9](https://www.quad9.net/de/)
3. Installation & Einrichtung von [Unbound](https://de.wikipedia.org/wiki/Unbound) / [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) / [Quad9](https://www.quad9.net/de/)
	- 3.1 Unbound als rekursiven DNS-Resolver einrichten
	- 3.2 Cloudflared als Forwarder mit Cloudflare als DNS-Resolver und DoH-Verschlüsselung
	- 3.3 Unbound als Forwarder mit Cloudflare & Quad9 (inkl. DoT)


----------------------------------------------------------------------------------------------------------------


# 1. Einleitung

## Ziel dieser Anleitung
- Diese Anleitung ist die Weiterführung der [Pi-hole-Anleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi).
- Diese Anleitung basiert darauf, dass [Pi hole](https://pi-hole.net/) bereits installiert wurde und Pi hole mit einem rekursiven-Resolver oder Forwarder erweitert werden soll.
- Die Zielgruppe dieser Anleitung ist für Administratoren, private Anwender, aber auch für kleine Unternehmen oder in Subnetzen von Unternehmen geeignet.


## Was ist ein rekursiver-Resolver oder Forwarder ?
- `rekursiver Resolver`
	- Ein rekursiver Resolver löst DNS-Anfragen selbstständig auf. Das heißt, er löst die DNS-Anfragen direkt bei den Root- und TLD-Nameservern auf, ohne einen anderen externen Resolver einzubeziehen.
	- Die Root- und TLD-Nameserver werden auch von externen DNS-Anbietern genutzt, um DNS-Anfragen aufzulösen, z.B. wenn man einen externen DNS-Anbieter wie Cloudflare oder Google nutzt, stellt man an diesen seine Anfragen und der externe DNS-Anbieter löst dann für einen die Anfrage bei den Root- und TLD-Nameservern auf.
	- Mit einem rekursivem-Resolver kann man direkt bei den Root- und TLD-Nameservern die Anfragen auflösen, wodurch der DNS-Anbieter wegfällt, was den Datenschutz erhöht.
	- Ein Beispiel für einen rekursiven-Resolver wäre Unbound.

- `Forwarder`
	- Ein Forwarder ist ein [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System), der Anfragen entgegennimmt, aber nicht selber auflöst, im gegensatz zu den rekursiven, sondern diese Anfrage an einen externen DNS-Anbieter weiterleitet.
	- Ein Forwarder ist also quasi der Vermittler zwischen dem Client und dem eigentlichen rekursiven Resolver.
	- Forwarder werden dann verwendet, wenn man Verschlüsselungsoptionen nutzen möchte, die Nativ (z.B. in Pi hole) nicht verfügbar sind.
	- Damit wäre ein üblicher Anwendungsfall eines Forwarders, das Entgegennehmen von den DNS-Anfragen vom Pi hole, diese (wenn konfiguriert) verschlüsselt an einen externen DNS-Anbieter weiterzuleiten, damit der externe Anbieter die Namensauflösung vornehmen kann.
	- Ein Beispiel für einen Forwarder wäre Cloudflared.


----------------------------------------------------------------------------------------------------------------


# 2. Was sind die Anwendungsfälle, Vor- & Nachteile von [Unbound](https://de.wikipedia.org/wiki/Unbound) / [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) / [Quad9](https://www.quad9.net/de/)



## 2.1 [Unbound](https://de.wikipedia.org/wiki/Unbound)
- Unbound ist ein rekursiver Nameserver der als DNS-Resolver entweder direkt oder z.B. in Kombination mit Pi hole genutzt werden kann.
- Da Unbound die DNS-Anfragen direkt an die Root-Nameserver stellt, werden keine externen DNS-Dienste wie z.B. Cloudflare verwendet, was bedeutet, dass Unbound die DNS-Auflösung selbständig übernimmt.
- Die Root-Nameserver sind quasi die oberste Instanz für DNS-Anfragen, das heißt, dass der lokale Unbound, genauso wie andere DNS-Dienste, wie Cloudflare die DNS-Anfragen an die DNS-Anfragen an diese Root-Nameserver stellt.
- Mit einem rekursivem-Resolver kann man direkt bei den Root- und TLD-Nameservern die Anfragen auflösen, wodurch der DNS-Anbieter wegfällt, was den Datenschutz erhöht.
- Ablauf der DNS-Anfragen (vereinfachtes Beispiel):
	- Client → Pi-hole → Unbound → Root Nameserver → TLD Nameserver → Autoritativer Nameserver → Unbound → Pi-hole → Client

### Anwendungsfälle, Vor- & Nachteile von Unbound (rekursiv)
- Bestmögliche Unabhängigkeit & Dezentralität
- Fokus auf Datenschutz, da kein externer DNS-Anbieter nötig (direkt an die Root-Nameserver)
- Nur Signaturverfahren (DNSSEC) möglich
- DNSSEC muss von der Ziel-Domain ebenfalls unterstützt werden, wenn der Anbieter DNSSEC nicht unterstützt, dann ist kein Signaturverfahren möglich
- Schnell durch lokales Caching & flexibel konfigurierbar
- Keine Verschlüsselungsmöglichkeiten, kein Schutz vor Tracking durch ISPs (Internetanbieter), höherer Wartungsaufwand bei Problemen, viele Domains unterstützen kein DNSSEC

### Anwendungsfälle, Vor- & Nachteile von Unbound (als Forwarder)
- Unbound kann ebenfalls als Forwarder eingesetzt werden
- Dabei löst Unbound NICHT rekursiv auf, sondern funktioniert ähnlich wie Cloudflared
- Der Vorteil dabei ist, dass man mehrere Upstream-Server und Anbieter konfigurieren kann, was die Ausfallsicherheit erhöht
- Auch mit Unbound als Forwarder ist DoT-Verschlüsselung möglich
- Durch die Nutzung von Unbound als Forwarder kann man von dem Cache profitieren



## 2.2 [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/)
- Cloudflared ist ein DNS-Forwarder, mit dem DNS-Anfragen von Pi hole verschlüsselt an Cloudflare oder andere öffentliche DNS-Resolver übertragen werden können.
- Das Verschlüsseln der DNS-Anfragen schützt vor Zensur, Überwachung durch ISPs oder Dritte, sowie vor Manipulationen an den Anfragen.
- Dabei ist man jedoch abhängig von Cloudflare und muss Cloudflare "vertrauen".
- Ablauf der DNS-Anfragen (vereinfachtes Beispiel):
	- Client → Pi-hole → Cloudflared → Cloudflare-DNS-Server → Cloudflared → Pi-hole → Client

### Anwendungsfälle, Vor- & Nachteile von Cloudflared (Forwarder)
- Bestmöglichen Schutz vor Dritten-Akteuren durch verfügbare Verschlüsselungsoptionen wie DoH
- Schutz vor [Man-in-the-Middle-Angriffen](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) bei Verwendung von DoH/DoT
- Schutz vor ISPs (Internetanbietern), denn durch Verschlüsselung kann dieser nicht mitlesen
- Cloudflare ist für gute Datenschutzrichtlinien und die Zuverlässigkeit bekannt
- einfache Einrichtung von Cloudflared
- Cloudflared ist wartungsarm und bietet eine hohe Performance in Kombination mit Verschlüsselung
- Trotz DoH-Verschlüsselung sind die Latenzen deutlich geringer als bei DoT-Verbindungen
- Ein Nachteil ist jedoch die Abhängigkeit von Cloudflare (als DNS-Anbieter), dem in Puncto Sicherheit und Datenschutz vertraut werden muss, also keine echte Dezentralität oder Unabhängigkeit



## 2.3 [Quad9](https://www.quad9.net/de/)
- Quad9 ist ein öffentlicher DNS-Dienst der von einer geminnützigen Organisation aus der Schweiz betrieben wird.
- Der Vorteil von Quad9 sind die Filterlisten, die bereits vom DNS-Anbieter auf deren DNS-Servern angewendet werden.
- Quad9 kann als DNS-Resolver verwendet werden, wenn man die Verschlüsselungsoptionen jedoch nutzen möchte, benötigt man noch einen Forwarder.
- Man kann entweder `Unbound im nicht rekursiven Modus` `als Forwarder` einrichten oder `Cloudflared` ebenfalls `als Forwarder` aber nicht mit Cloudflare DNS-Servern, `sonder mit den Quad9 DNS-Resolvern` einrichten.  
- Ablauf der DNS-Anfragen (vereinfachtes Beispiel):
	- Client → Pi-hole → beliebiger-Forwarder → Quad9-DNS-Server → beliebiger-Forwarder → Pi-hole → Client

### Anwendungsfälle, Vor- & Nachteile von Quad9 (mit Unbound oder Cloudflared als Forwarder)
- Öffentlicher DNS-Resolver, betrieben von einer Non-Profit-Organisation aus der Schweiz
- Bestmögliche Sicherheit vor Dritten-Akteuren durch verfügbare Verschlüsselungsoptionen DoT/DoH
- Schutz vor [Man-in-the-Middle-Angriffen](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) bei Verwendung von DoH/DoT
- Schutz vor ISP (Internetanbieter), denn durch Verschlüsselung kann dieser nicht mitlesen
- Hoher Datenschutzstandard
- Bei Quad9 werden bereits DNS-Filterlisten angewendet
- Für Quad9 benötigt man einen Forwarder, wie z.B. Unbound (nicht rekursiv) oder Cloudflared
- Nachteile sind z.B. die eingeschränkte Flexibilität und die Filterlisten beim Anbieter könnten ungewollt zu Problemen führen



----------------------------------------------------------------------------------------------------------------


- Eigenschaften und Anwendungsfälle der DNS-Verschlüsselungs- und Signaturverfahren (DoT/DoH/DNSSEC) können in der [Pi-hole-Anleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/raspberry-pi) nachgeschaut werden.


----------------------------------------------------------------------------------------------------------------


# 3. Installation & Einrichtung von [Unbound](https://de.wikipedia.org/wiki/Unbound) / [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) / [Quad9](https://www.quad9.net/de/)


# 3.1 Unbound als rekursiven DNS-Resolver einrichten
- System aktualisieren
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- Unbound installieren
```
$ sudo apt install unbound
```

- Version prüfen
```
$ unbound -V
```


## Unbound konfigurieren
- Neue Konfigurationsdatei erstellen oder Standarddatei bearbeiten:
```
$ sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

- Folgendes einfügen:
```
server:
    verbosity: 1
    interface: 127.0.0.1
    port: 5353
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    
    # Root-Server für rekursive Abfragen
    root-hints: "/var/lib/unbound/root.hints"
    # Pfad für Trust Anchor (DNSSEC)
    auto-trust-anchor-file: "/var/lib/unbound/root.key"
    
    hide-identity: yes
    hide-version: yes
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: yes
    edns-buffer-size: 1472
    prefetch: yes
    prefetch-key: yes
    
    cache-min-ttl: 360
    cache-max-ttl: 86400
    
    so-rcvbuf: 4m	# Socket Receive Buffer Size
    so-sndbuf: 4m	# Socket Send Buffer Size
    num-threads: 1
```
- Mit `STRG+X` speichern
- Die Konfigurationsparameter erklärt:
	- `port: 5353`: Gibt den Port an, auf welchem Unbound Anfragen entgegennimmt
	- `root-hints: "/var/lib/unbound/root.hints"`: Pfad zur Datei mit den Adressen der Root-Nameserver
	- `auto-trust-anchor-file: "/var/lib/unbound/root.key"`: Aktiviert DNSSEC
	- `hide-identity`: Unterdrückt die Information, welche Software als Resolver antwortet
	- `prefetch`: Aktiviert Caching von DNS-Einsträgen

- `num-threads`-Zeile in der konfiguration (Verarbeitungsthreads)
	- Die Anzahl der num-threads bestimmt die Anzahl der 
	- Jeder Thread kann eine DNS-Anfrage bearbeiten
	- Durch höhere Werte können parallele Verarbeitungen ermöglicht werden.
	- Ob die Erhöhung des Wertes möglich ist, hängt von der Leistung ([CPU](https://de.wikipedia.org/wiki/Prozessor) und [RAM](https://de.wikipedia.org/wiki/Random-Access_Memory)) ab.
	- Dabei sollte man die Anzahl von den verfügbaren CPU Kernen machen.
	- Die Anzahl der CPU-Kerne kann man mit `$ nproc` überprüfen.
	- In `kleineren Netzwerken` reicht `num-threads: 1` vollkommen aus (Erhöhung bringt dann meist keinen Vorteil).
	- In `größeren Netzwerken` kann die Anzahl `je nach verfügbaren CPU-Kernen` auf `2, 4 oder 8` erhöht werden.
	- Man sollte zudem auch immer ein paar Ressourcen für Pi hole übrig lassen.


## Root-Hints Datei herunterladen
- Unbound benötigt die Root-Server-Liste, um die Anfragen rekursiv auflösen zu können.
```
$ sudo wget -O /var/lib/unbound/root.hints https://www.internic.net/domain/named.cache
```

## Unbound neu starten & Autostart aktivieren
```
$ sudo systemctl restart unbound
$ sudo systemctl enable unbound
```


## Unbound in Kombination mit Pi hole
- Pi hole konfigurieren um Unbound als Upstream-DNS zu nutzen:
	- Pi hole Webinterface öffnen
	- Unter `Settings` > `DNS`
	- alle bestehenden Upstream-DNS-Server entfernen
	- folgendes bei `Custom 1 (IPv4)` einfügen: `127.0.0.1#5353`
	- Speichern

- Pi-hole Dienst neu starten (optional)
```
$ sudo systemctl restart pihole-FTL
```


## Testen
- Prüfen, ob Unbound als DNS-Resolver funktioniert:
	- Wenn eine Antwort erscheint, ist Unbound korrekt eingerichtet.
```
$ dig pi-hole.net @127.0.0.1 -p 5353
```

## DNSSEC validieren
- KEINE IP-Adresse + SERVFAIL-Antwort = erfolgreich validiert
```
$ dig +dnssec +multi dnssec-failed.org @127.0.0.1 -p 5353
```


## Ergänzung für [Raspberry Pi OS](https://www.raspberrypi.com/software/)
- Damit DNSSEC ordnungsgemäß funktioniert, muss die Systemzeit kortrekt gesetzt sein.
```
$ timedatectl status
```
- Falls keine aktuelle Zeit gesetzt ist, folgende Befehle ausführen:
```
$ sudo apt install ntp
$ systemd-timesyncd
```


----------------------------------------------------------------------------------------------------------------


# 3.2 Cloudflared als Forwarder mit Cloudflare als DNS-Resolver und DoH-Verschlüsselung


## Installation
- System aktualisieren
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- Pi hole aktualisieren
```
$ sudo pihole -up
```


----------------------------------------------------------------------------------------------------------------


### für Debian basierte Distributionen mit x86-[CPU](https://de.wikipedia.org/wiki/Prozessor)
- Debian-Installer-Paket für [x86-Architektur](https://de.wikipedia.org/wiki/X86-Prozessor) (amd64) herunterladen und Cloudflared installieren.
```
$ cd /tmp
$ wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
$ sudo apt install ./cloudflared-linux-amd64.deb
```

### für [Raspberry Pi OS](https://www.raspberrypi.com/software/) mit ARM-[CPU](https://de.wikipedia.org/wiki/Prozessor)
- Debian-Installer-Paket für [ARM-Architektur](https://de.wikipedia.org/wiki/ARM-Architektur) (arm64) herunterladen und Cloudflared installieren.
```
$ cd /tmp
$ wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb
$ sudo apt install ./cloudflared-linux-arm64.deb
```


----------------------------------------------------------------------------------------------------------------


- Version überprüfen:
```
$ cloudflared -v
```

- Benutzer für Cloudflared anlegen
	- Erstellt einen Systembenutzer, der aber keine Login-Shell hat und auch kein Home-Verzeichnis für den sicheren Betrieb.
	- Dieser wird benötigt, um den Cloudflared-Prozess auszuführen.
	- Das wird üblicherweise so gemacht, um Programme mit so wenig Rechten wie möglich und so vielen wie nötig zu betreiben.
```
$ sudo useradd -s /usr/sbin/nologin -r -M cloudflared
```

## systemd-Service-Datei erstellen:
```
$ sudo nano /etc/systemd/system/cloudflared.service
```

```
[Unit]
Description=cloudflared DNS over HTTPS proxy
After=syslog.target network-online.target

[Service]
Type=simple
User=cloudflared
ExecStart=/usr/local/bin/cloudflared proxy-dns --address 127.0.0.1 --port 5053 --upstream https://cloudflare-dns.com/dns-query --upstream https://1.1.1.1/dns-query --upstream https://1.0.0.1/dns-query --bootstrap=1.1.1.1
Restart=on-failure
RestartSec=10
KillMode=process

[Install]
WantedBy=multi-user.target
```
- Mit `STRG+X` speichern
- Anfragen über DoH werden an https://cloudflare-dns.com/dns-query weitergeleitet
- `--port 5053`: Port 5053 wird festgelegt
- `--upstream`: Zusätzliche redundante DoH-Server
- `--bootstrap`: Vermeidet DNS-Loop beim Start durch direkte IP-Nutzung zum Bootstrap


## Optional Fallback Upstream-Server einrichten
- Hier ist zu beachten, dass auch der Fallback-Upstram-DNS-Server DoH unterstützt.
- Dann muss die DoH-URL eingetragen werden.
- Cloudflared nutzt die Server in angegebener Reihenfolge von oben nach unten, also wenn der oberste Upstream nicht funktioniert, dann wird der nächstuntere verwendet.
- Folgende DoH-URL in der `/etc/systemd/system/cloudflared.service`-Konfig ergänzen, um Quad9 als Fallback einzurichten: `--upstream https://dns.quad9.net/dns-query`
- Das würde dann z.B. so aussehen: 
```ExecStart=/usr/local/bin/cloudflared proxy-dns --address 127.0.0.1 --port 5053 --upstream https://cloudflare-dns.com/dns-query --upstream https://1.1.1.1/dns-query --upstream https://1.0.0.1/dns-query --upstream https://dns.quad9.net/dns-query --bootstrap=1.1.1.1
```


## Dienst aktivieren, starten und Autostart aktivieren
```
$ sudo systemctl daemon-reexec
$ sudo systemctl daemon-reload
$ sudo systemctl enable cloudflared
$ sudo systemctl start cloudflared
```

- Status überprüfen
```
$ sudo systemctl status cloudflared
```

## Automatisches Update einricheten - standardmäßig keine automatischen Updatemöglichkeiten
- Da Cloudflared `NICHT über den Paketmanager apt aktualisiert werden` kann, empfiehlt es sich ein Cronjob einzurichten, damit Cloudflared automatisch regelmäßig aktualisiert wird.

1. Cronjob anlegen
```
$ sudo nano /etc/cron.d/cloudflared-update
```

2. Einfügen:
```
0 2 */28 * * root wget -q -O /tmp/cloudflared.deb https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && dpkg -i /tmp/cloudflared.deb && systemctl restart cloudflared
```
- Alle `28-Tage` um `2 Uhr Nachts` wird die aktuelle .deb-Datei heruntergeladen, installiert und Cloudflard neu gestartet.


## Cloudflared in Kombination mit Pi hole
- Pi hole konfigurieren um Unbound als Upstream-DNS zu nutzen
	- Pi hole Webinterface öffnen
	- Unter `Settings` > `DNS`
	- alle bestehenden Upstream-DNS-Server entfernen
	- folgendes bei `Custom 1 (IPv4)` einfügen: `127.0.0.1#5053`
	- Speichern

- Pi-hole Dienst neu starten:
```
$ sudo systemctl restart pihole-FTL
```


## Testen
- Prüfen, ob Cloudflared als DNS-Resolver funktioniert:
```
$ dig pi-hole.net @127.0.0.1 -p 5053
```

## DoH-Verschlüsselung prüfen
- Port prüfen:
	- Wenn Cloudflared auf dem gesetzten Port horcht, ist der Dienst aktiv.
```
$ sudo lsof -i :5053
```

- DNS-Anfragen analysieren:
	- Hier nun auf externe Anfragen achten.
	- Wenn Pi hole mit Cloudflared genutzt wird, wird hier dieser Traffic über Port 53 angezeigt. Jedoch sollte der externe Traffic, also das, was Cloudflared nach außen leitet, über Port 5053 laufen.
	- GGf. auch von einem anderen System mit `$ dig example.com @IP-Adresse-von-DNS-Server` DNS-Anfrage stellen, um Traffic beobachten zu können:
```
$ sudo tcpdump -i any port 53a
```

- Verschlüsselter Traffic von Cloudflared an die Upstream Server einsehen:
```
$ sudo tcpdump -i any port 5053
```

## Logs
```
$ journalctl -u cloudflared -f
```
```
$ journalctl -u cloudflared -b
```

## Optional: Systemseitige Optimierung - Limit für offene Dateideskriptoren
- Limit für offene Dateideskriptoren prüfen:
	- Wenn die `Ausgabe: 4096` ist, dann ist `alles in ordnung`.
	- Sollte die `Ausgabe: 1024` sein, ist das `für hohes Aufkommen zu gering`.
	- Für ganz viele Anfragen ist `8192` optimal.
	- Das bringt jedoch nur in Lastszenarien etwas, bei wenigen Clients ist die Anpassung nicht notwendig.
```
$ ulimit -n
```

- Limit erhöhen:
```
$ sudo nano /etc/security/limits.conf
```

- Am Ende ergänzen:
```
*         soft     nofile      8192
*         hard     nofile      8192
```

- Danach auch Systemd anpassen, um die Änderung dauerhaft zu setzen:
```
$ sudo mkdir -p /etc/systemd/system/unbound.service.d/
```
```
$ sudo nano /etc/systemd/system/unbound.service.d/limits.conf
```

- erwarteter Inhalt:
```
[Service]
LimitNOFILE=8192
```

- Neu laden:
```
$ sudo systemctl daemon-reexec
$ sudo systemctl daemon-reload
$ sudo systemctl restart unbound
```

- Damit alles vollständig und sauber geladen wird, noch ein Neustart:
```
$ sudo reboot
```

- Überprüfen:
```
$ ulimit -n
```


----------------------------------------------------------------------------------------------------------------


# 3.3 Unbound als Forwarder mit Cloudflare & Quad9 (inkl. DoT)
- Unbound nicht rekursiv sondern ähnlich wie Cloudflared als Forwarder nutzen mit Upstream-Servern von Quad9 oder Cloudflare.
- Dabei ist auch DoT-Verschlüsselung möglich.
- Leider sind bei dieser Methode die kalten Anfragen, also die Anfragen, die von Unbound an einen externen Upstream-Server gestellt werden müssen, bevor sie im Cache abgelegt werden (warme Anfragen), mit teils relativ hohen Latenzen verbunden.
- Wenn man also die bestmögliche Performance haben möchte und trotzdem nicht auf Verschlüsselung verzichten möchte, ist Cloudflared mit DoH besser geeignet.
- Für private Anwender sollte jedoch die Performance mit Unbound + DoT in der Regel trotzdem ausreichen.


## Installtion & Konfiguration
- System aktualisieren:
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- Unbound installieren:
```
$ sudo apt install unbound
```

## Konfiguration erstellen
```
$ sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

- Konfiguration:
```
server:
    verbosity: 1
    interface: 127.0.0.1
    port: 5353
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    tcp-upstream: yes    # aktiviert TCP für Anfragen an Upstream-Server, für DoT erforderlich
    so-rcvbuf: 4m        # Socket Receive Buffer Size
    so-sndbuf: 4m        # Socket Send Buffer Size
    num-threads: 1
    hide-identity: yes
    hide-version: yes
    harden-glue: yes
    harden-dnssec-stripped: yes
    harden-referral-path: yes             # zusätzliche Prüfung bei Referrals
    qname-minimisation: yes
    use-caps-for-id: yes
    edns-buffer-size: 1232

    cache-min-ttl: 3600
    cache-max-ttl: 86400

    # *** Cache-Größen festlegen ***
    msg-cache-size: 50m                   # Größe des Nachrichtencaches im RAM (50 MB)
    rrset-cache-size: 50m                 # Größe des RRset-Caches im RAM (50 MB)

    # Wichtig: Root-Hints & DNSSEC abschalten, wenn Unbound nur weiterleiten soll
    root-hints: ""
    auto-trust-anchor-file: ""
    # do-not-query-localhost: no
    # recursion: yes

forward-zone:
    name: "."
    forward-tls-upstream: yes

    # Cloudflare mit DNS-over-TLS – Primär
    forward-addr: 1.1.1.1@853#cloudflare-dns.com
    forward-addr: 1.0.0.1@853#cloudflare-dns.com

    # Quad9 mit DNS-over-TLS – Sekundär (Fallback)
    forward-addr: 9.9.9.9@853#dns.quad9.net
    forward-addr: 149.112.112.112@853#dns.quad9.net
```
- Mit `STRG+X` speichern
- `forward-zone`: Definiert, wo DNS-Anfragen weitergeleitet werden
- `forward-addr` : Ziel DNS-Server mit Port
- `root-hints` & `auto-trust-anchor-file` leeren / entfernen oder mit 
`- recursion: yes` muss bleiben, damit Unbound korrekt funktioniert
- Je nach Wunschkonfiguration das `#` (für Kommentare) so anpassen, dass die korrekten Konfigurationen aus dem `forward-zone`-Abschnitt genutzt wird.


----------------------------------------------------------------------------------------------------------------


## Optional: Cloudflare primär und Quad9 als Backup einrichten (Ausfallsicherheit)
- Die bereits oben gezeigte Konfiguration von Unbound unter dem Punkt `forward-zone` kann man nach Wunsch anpassen.
- Unbound `verwendet die Resolver` in der `angegebenen Reihenfolge`.
- Die Reihenfolge sowie die Anbieter können natürlich auch angepasst werden.
- Dabei sind für die Ausfallsicherheit 2-3 DNS-Anbieter sinnvoll. Mehr als 4 verschiedene DNS-Anbieter sollten NICHT in der Konfiguration enthalten sein, da es ansonsten zu Verzögerungen kommen kann.


## Ergänzung für interne Netze mit eigenen internen Hostnamen (meist Unternehmen)
- `z.B. firma.local`
- Der Abschnitt ist nicht zwingend erforderlich, kann aber noch mit in die Unbound-Konfiguration eingefügt werden.
- Das bewirkt, dass interne Domains nicht weitergeleitet werden, was für mehr Datenschutz und Sicherheit sorgt.
- Die IP-Adressbereiche müssen evtl. angepasst werden.
- Außerdem auch die Zeile `private-domain` anpassen.

```
...
    # Lokale Netze / interne Domains nicht weiterleiten
    # IP-Adressbereiche müssen evtl. angepasst werden
    private-address: 10.0.0.0/8
    private-address: 10.10.10.0/24
    private-address: 192.168.0.0/16
    private-address: 192.168.1.0/24
    private-address: 192.168.12.0/24
    private-domain: "firma.local"
...
```

## Ergänzung: Erhöhung paralleler TLS-Verbindungen für DoT-Verschlüsselung (für größere Netze sinnvoll)
- Standardmäßig werden wenige TLS-Verbindungen offen gehalten.
- Das kann man jedoch erhöhen.
- `Je nach Systemleistung` kann man auch auf bis zu 50-100 erhöhen.
- Für Heimnetzwerke reicht `10-20` meist aus, für größere Netze kann man auch auf `30-60` gehen.
- Sollte man die Werte deutlich erhöhen wollen, sollte man unbedingt den nächsten Punkt `Limit für offene Dateideskriptoren` beachten.
- Dabei sollte man jedoch auch die Ressourcen (RAM und CPU) berücksichtigen. 
- Konfiguration mit `outgoing-num-tcp` und `incoming-num-tcp` Zeilen ergänzen - Beispiel:
```
...
    # Parallelität für DoT-Verbindungen erhöhen (Optional)
    outgoing-num-tcp: 40    # Anzahl gleichzeitiger TCP-Verbindungen (DoT nutzt TCP)
    incoming-num-tcp: 40    # Falls Clients direkt per TCP anfragen (selten nötig)
...
```

## Ergänzung: Epersistente TLS-Verbindungen für DoT-Verschlüsselung
- Einstellen, dass TLS-Verbindungen bestehen bleiben, damit diese nicht für jede neue Anfrage neu aufgebaut werden müssen.
- Die persistente Verbindung soll bei kalten Anfragen etwas die Latenz verringern.
```
...
# TLS-Optimierungen für persistente Verbindungen
outgoing-port-permit: 853  # TLS-Port erlauben für Outbound
outgoing-port-avoid: 0     # Vermeide keinen Port (vorsichtshalber setzen)
...
```

## Ergänzung: CA-Zertifikats-Bundle festlegen, welches zur überprüfung der DoT-Verbindung genutzt werden soll
- Auf den meisten Betriebssysteme (inkl. [Pi OS](https://www.raspberrypi.com/software/) und [Ubuntu-Server](https://ubuntu.com/download/server)) müsste Unbound eigentlich das CA-Zertifikats-Bundle standardmäßig nutzen, aber um sicherzustellen, dass Unbound das gewünschte Zertifikatsbundle nutzt, kann die Zeile hinzugefügt werden.
- Weitere Konfigurationen sind nicht notwendig, die Root-CA-Zertifikate, also das Zertifikats-Bundle ist normalerweise auf den gänigen Betriebssystemen vorinstalliert, um Zertifikate zu überprüfen.
```
...
    # System-CA-Bundle für TLS-Verifizierung (Optional)
    tls-cert-bundle: "/etc/ssl/certs/ca-certificates.crt"
...
```


## Ergänzung: Zugriff auf Unbound nur über localhost, wie z.B. Pi hole
```
...
    # Zugriff auf Unbound nur von localhost erlauben (Optional)
    access-control: 127.0.0.1/32 allow
...
```

## Ergänzung: Performance von Unbound optimieren
- Wenn viel RAM verfügbar ist, dann kann der Cache von den vorgeschlagenen 50MB z.B. auf 100MB erhöht werden.
- Das ist nicht zwingend notwendig, aber so kann Unbound mehr Einträge im Cache halten.
- Dafür die Zeilen `msg-cache-size` und `rrset-cache-size` anpassen.


## Ergänzung: Multi-Core-Performance verbessern
- Die Zeile `num-threads` bestimmt die Verarbeitungsthreads, für Unbound.
- Jeder Thread kann eine DNS-Anfrage bearbeiten.
- Durch höhere Werte können parallele Verarbeitungen ermöglicht werden, wobei Unbound trotzdem nur mäßig davon profitiert.
- Standardmäßig wird nur 1 Thread verwendet.
- Wie viele Threads man nutzen kann/sollte hängt von der Anzahl an CPU-kernen ab.
- Die Anzahl der CPU-Kerne kann man mit `$ nproc` überprüfen.
- Das Erhöhen bringt jedoch nicht immer einen direkten Vorteil.
- Für Systeme mit 2 Kernen ist `num-threads: 1` ausreichend, wenn mindestens 4 Kernen vorhanden sind kann auf `num-threads: 2` erhöht werden.
- In `kleineren Netzwerken` reicht aber auch `num-threads: 1` vollkommen aus (Erhöhung bringt dann meist keinen Vorteil).
- In `größeren Netzwerken` kann die Anzahl `je nach verfügbaren CPU-Kernen` auf `2, 4 oder 8` erhöht werden.
- Man sollte zudem auch immer ein paar Ressourcen für Pi hole übrig lassen.


## Ergänzung: Puffer für eingehende und ausgehende Pakete anpassen (Optionen bereits in Konfig enthalten)
- Bei hoher Last kann ein zu kleiner Empfangspuffer zu Paketverlust führen.
- Daher kann man optional noch den Puffer festlegen.
- Normalerweise sollten 4MB oder 8MB ausreichen, man kann der Wert aber auch erhöhen, wobei das nur in speziellen Fällen sinvoll ist.
- Dafür `so-rcvbuf` und `so-sndbuf` anpassen - Beispiel:
```
server:
    verbosity: 1
    interface: 127.0.0.1
    port: 5353
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    so-rcvbuf: 8m        # Socket Receive Buffer Size
    so-sndbuf: 8m        # Socket Send Buffer Size
    num-threads: 1
    hide-identity: yes
    ...
```


## Ergänzung: Limit für offene Dateideskriptoren - `ulimit -n` prüfen und ggf. für hohes TCP-Aufkommen erhöhen
- Limit für offene Dateideskriptoren prüfen
	- Wenn die `Ausgabe: 4096` ist, dann ist `alles in ordnung`.
	- Sollte die `Ausgabe: 1024` sein, ist das `für hohe TCP-Aufkommen zu gering`.
	- Für ganz viele Anfragen ist `8192` optimal.
	- Besonders wenn die parallelen TCP-Verbindungen erhöht wurden, sollte das geprüft werden.
```
$ ulimit -n
```

- Limit erhöhen:
```
$ sudo nano /etc/security/limits.conf
```

- Am Ende ergänzen:
```
*         soft     nofile      8192
*         hard     nofile      8192
```

- Danach auch Systemd anpassen, um die Änderung dauerhaft zu setzen:
```
$ sudo mkdir -p /etc/systemd/system/unbound.service.d/
```
```
$ sudo nano /etc/systemd/system/unbound.service.d/limits.conf
```
- erwarteter Inhalt:
```
[Service]
LimitNOFILE=8192
```

- Neu laden:
```
$ sudo systemctl daemon-reexec
$ sudo systemctl daemon-reload
$ sudo systemctl restart unbound
```
- Damit alles vollständig und sauber geladen wird, noch ein Neustart:
```
$ sudo reboot
```

- Überprüfen:
```
$ ulimit -n
```


----------------------------------------------------------------------------------------------------------------


## Syntax der Konfiguration überprüfen
```
$ sudo unbound-checkconf
```


## Unbound neu starten & Autostart aktivieren
```
$ sudo systemctl restart unbound
$ sudo systemctl enable unbound
```
```
$ sudo systemctl status unbound
```


## Unbound in Kombination mit Pi hole
- Pi hole konfigurieren um Unbound als Upstream-DNS zu nutzen
	- Pi hole Webinterface öffnen
	- Unter `Settings` > `DNS`
	- alle bestehenden Upstream-DNS-Server entfernen
	- folgendes bei `Custom 1 (IPv4)` einfügen: `127.0.0.1#5353`
	- Speichern

- Pi-hole Dienst neu starten (optional)
```
$ sudo systemctl restart pihole-FTL
```


## Testen
- Prüfen, ob Unbound als DNS-Resolver funktioniert:
```
$ dig pi-hole.net @127.0.0.1 -p 5353
```

- Unbound Logs:
```
$ sudo journalctl -u unbound
```


## Geschwindigkeit überprüfen und ggf. optimieren 
- Prüfen, ob Unbound als DNS-Resolver funktioniert:
	- Die Angabe in `msec` ist die Latenz in Sekunden.
	- kalt, also wenn die Anfrage das erste Mal gestellt wird, sind mit DoT zwischen 50-120 ms ein relativ guter Wert, 120-200 ms sind akzeptabel, aber nicht unbedingt besonders gut.
	- warm, also wenn die Anfrage im Cache liegt, sind Latenzen von 0-5 ms üblich.
	- Inwieweit die Latenzen akzeptabel sind, hängt u.a. auch von Anwendungsfällen und den persönlichen Präferenzen ab.
```
$ dig pi-hole.net @127.0.0.1 -p 5353
```

1. Latenz zu den Upstream-DNS-Servern prüfen
- Unbound verbindet sich über TCP+TLS mit den Upstream-Servern und mit openssl-Verbindungszeit grob messen:
```
$ time openssl s_client -connect 1.1.1.1:853 -servername cloudflare-dns.com </dev/null
```
```
$ time openssl s_client -connect 9.9.9.9:853 -servername dns.quad9.net </dev/null
```
alternativ (nur TCP-Handshake)
```
$ time nc -vz 1.1.1.1 853
```
```
$ time nc -vz 9.9.9.9 853
```
- Zielwerte
	- `<50 ms = sehr gut`
	- `>100 ms = suboptimal → anderer DNS-Upstream-Anbieter mit DoT testen`

2. TLS-Zertifikatsprüfung beschleunigen
- Überprüfen, ob die folgende Zeile in der Konfig enthalten ist: `tls-cert-bundle: "/etc/ssl/certs/ca-certificates.crt"`
- Danach Logs einsehen:
```
$ journalctl -u unbound.service
```


3. CPU / Last überprüfen
- TLS-Handshakes benötigen Ressourcen:
```
$ htop
```
htop kann mit `F10` verlassen werden
```
$ top
```
top kann mit `STRG+C` gestoppt werden

- Wenn die Auslastung während einer/mehreren kalten Anfragen hoch ist, kann evtl. `num-threads: 2` und wenn genügend Ressourcen zur Verfügung stehen auch auf `num-threads: 4` erhöht werden.


4. Unbound Performance messen:
```
$ unbound-control stats_noreset
```


----------------------------------------------------------------------------------------------------------------

