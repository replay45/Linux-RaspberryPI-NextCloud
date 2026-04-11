# DNS-Forwarder (DoT/DoH/DNSCrypt Verschlüsselung)

- Anleitung zu DNS-Forwarder mit Verschlüsselungsoptionen in Kombination mit [Pi hole](https://pi-hole.net/).
- Diese Anleitung ist die Fortführung der [Pi hole-Anleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/dns-server/Pi-hole.md).


`Anleitung verfasst am 2.3.2026, zuletzt bearbeitet am 11.4.2026`


## Inhaltsverzeichnis
1. Einleitung
	- Ziel dieser Anleitung
	- Was ist ein rekursiver-Resolver oder Forwarder ?
	- Eigenschaften und Anwendungsfälle von DoT/DoH/DNSCrypt/DNSSEC
2. Cloudflared - (nicht mehr supportet)
    - `Update März 2026` zu [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/)
3. Pi hole mit [dnscrypt-proxy](https://docs.pi-hole.net/guides/dns/dnscrypt-proxy/) als Forwarder
    - Was ist [dnscrypt-proxy](https://dnscrypt.info/) ?
    - Einrichtung von dnscrypt-proxy als Forwarder
    - dnscrypt-proxy deinstallieren
    - Optional Testen, ob Verschlüsselung angewendet wird
4. Optional: Systemseitige Optimierung - Limit für offene Dateideskriptoren
    - Limit für offene Dateideskriptoren für Pi hole anpassen
    - Limit für offene Dateideskriptoren für dnscrypt-proxy anpassen
5. Cloudflared deinstallieren
    - Wenn Cloudflared manuell installiert wurde
    - Wenn Cloudflared über den "service install" installiert wurde


----------------------------------------------------------------------------------------------------------------


# 1. Einleitung

## Ziel dieser Anleitung
- Diese Anleitung ist die Weiterführung der [Pi hole-Anleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/dns-server/Pi-hole.md).
- Diese Anleitung basiert darauf, dass [Pi hole](https://pi-hole.net/) bereits installiert wurde und Pi hole mit einem `Forwarder erweitert werden soll`.
- Die Zielgruppe dieser Anleitung sind Administratoren, private Anwender, aber auch kleine Unternehmen.


## Was ist ein rekursiver-Resolver oder Forwarder ?
- `rekursiver Resolver`
	- Ein rekursiver Resolver löst DNS-Anfragen selbstständig auf. Das heißt, er löst die DNS-Anfragen direkt bei den Root- und TLD-Nameservern auf, ohne einen anderen externen Resolver einzubeziehen.
	- Die Root- und TLD-Nameserver werden auch von externen DNS-Anbietern genutzt, um DNS-Anfragen aufzulösen, z.B. wenn man einen externen DNS-Anbieter wie Cloudflare oder Google nutzt, stellt man an diesen seine Anfragen und der externe DNS-Anbieter löst dann für einen die Anfrage bei den Root- und TLD-Nameservern auf.
	- Mit einem rekursiven-Resolver kann man direkt bei den Root- und TLD-Nameservern die Anfragen auflösen, wodurch der DNS-Anbieter wegfällt, was den Datenschutz erhöht.
	- Ein Beispiel für einen rekursiven-Resolver wäre Unbound.
	- [Anleitung zu Unbound als Forwarder oder Rekursiver Resolver](xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx)

- `Forwarder`
	- Ein Forwarder ist ein [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System), der Anfragen entgegennimmt, aber nicht selber auflöst, im Gegensatz zu den rekursiven, sondern diese Anfrage an einen externen DNS-Anbieter weiterleitet.
	- Ein Forwarder ist also quasi der Vermittler zwischen dem Client und dem eigentlichen rekursiven Resolver.
	- Forwarder werden dann verwendet, wenn man Verschlüsselungsoptionen nutzen möchte, die nativ (z.B. in Pi hole) nicht verfügbar sind.
	- Damit wäre ein üblicher Anwendungsfall eines Forwarders, das Entgegennehmen von den DNS-Anfragen vom Pi hole, diese (wenn konfiguriert) verschlüsselt an einen externen DNS-Anbieter weiterzuleiten, damit der externe Anbieter die Namensauflösung vornehmen kann.
	- Ein Beispiel für einen Forwarder wäre [dnscrypt-proxy](https://docs.pi-hole.net/guides/dns/dnscrypt-proxy/).
	- Client → Pi-hole → beliebiger-Forwarder → Upstream-DNS-Server → beliebiger-Forwarder → Pi-hole → Client


## Eigenschaften und Anwendungsfälle von DoT/DoH/DNSCrypt/DNSSEC
- Eigenschaften und Anwendungsfälle der DNS-Verschlüsselungs- und Signaturverfahren (DoT/DoH/DNSCrypt/DNSSEC) können ebenfalls in der [Pi hole-Anleitung](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/dns-server/Pi-hole.md) nachgeschaut werden.


----------------------------------------------------------------------------------------------------------------


# 2. Cloudflared - (nicht mehr supportet)
- `Update März 2026` zu [Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/) - [developers.cloudflare.com/changelog](https://developers.cloudflare.com/changelog/post/2025-11-11-cloudflared-proxy-dns/):
    - Leider unterstützt Cloudflared nicht mehr die "proxy-dns"-Funktion ab Version 2026.2.0.
    - Das bedeutet, dass Cloudflare ab Version 2026.2.0 nicht mehr als DoH-Forwarder (mit DNSoverHTTPS), wie es in dieser und der verlinkten Anleitung beschrieben wird, verwendet werden kann.
    - Entwender es wird ein Downgrade auf die Version 2025.x durchgeführt, um die proxy-dns-Funktion weiterhin zu verwenden, oder es müssen alternative DoH-Proxys, wie [dnscrypt-proxy](https://docs.pi-hole.net/guides/dns/dnscrypt-proxy/) oder [stubby (nur DoT)](https://github.com/getdnsapi/stubby) verwendet werden.
    - Alternativ bietet sich der Umstieg zu [AdGuard Home](https://adguard.com/de/adguard-home/overview.html) an, welcher DoH und DoT direkt unterstützt.


----------------------------------------------------------------------------------------------------------------


# 3. Pi hole mit [dnscrypt-proxy](https://docs.pi-hole.net/guides/dns/dnscrypt-proxy/) als Forwarder

## Was ist [dnscrypt-proxy](https://dnscrypt.info/) ?
- Dnscrypt-proxy ist ein flexibler [Open Source](https://de.wikipedia.org/wiki/Open_Source) DNS-Proxy, der moderne Verschlüsselungsmethoden unterstützt, darunter DNSCrypt v2 und DNSoverHTTPS (DoH).
- Mit dnscrypt-proxy lässt sich der DNS-Traffic zum Upstream-Server eines DNS-Anbieters verschlüsseln.
- Die Verschlüsselung des DNS-Traffics kann vor [Man-in-the-Middle-Angriffen](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) sowie vor Manipulationen, Tracking/Logging durch den Internetserviceprovider (ISP) oder durch Dritte schützen.
- Außerdem unterstützt dnscrypt-proxy `Load-Balanceing`, das heißt, wenn man mehrere DNS-Upstream-Server, z.B. auch von unterschiedlichen Anbietern in der Konfig angibt, dann bevorzugt dnscrypt-proxy den Server mit der geringsten Latenz.


## Einrichtung von dnscrypt-proxy als Forwarder
[Github.com/DNSCrypt/dnscrypt-proxy/wiki](https://github.com/DNSCrypt/dnscrypt-proxy/wiki/Installation-linux#installation-on-linux-overview)

- WICHTIG:
    - Sollte bei einem Setup noch Cloudflared installiert sein, dann sollte es vorher deinstalliert werden, sodass keine Portkonflikte entstehen.
    - [docs.pi-hole.net - Cloudflared](https://docs.pi-hole.net/guides/dns/cloudflared/)

- Installation von dnscrypt-proxy für Debian/Ubuntu
```
$ sudo apt update
$ sudo apt install dnscrypt-proxy
```

- dnscrypt-proxy konfigurieren
    - Dies ist notwendig, da dns-crypt-proxy ebenfalls standardmäßig Port 53 nutzt, dieser ja aber schon von Pi hole genutzt wird, daher muss der Port für dnscrypt-proxy angepasst werden.
```
$ sudo systemctl edit dnscrypt-proxy.socket
```

- Konfiguration bearbeiten, sodass sie folgendes enthält:
    - Dabei ist die Position des Abschnittes entscheident.
    - Der Abschnitt muss über der Zeile "### Lines below this comment will be discarded" stehen:
```
### Editing /etc/systemd/system/dnscrypt-proxy.socket.d/override.conf
### Anything between here and the comment below will become the contents of the drop-in file

[Socket]
ListenStream=
ListenDatagram=
ListenStream=127.0.0.1:5053
ListenDatagram=127.0.0.1:5053

### Lines below this comment will be discarded
```

- `/etc/dnscrypt-proxy/dnscrypt-proxy.toml` bearbeiten
```
$ sudo nano /etc/dnscrypt-proxy/dnscrypt-proxy.toml
```

- Konfiguration bearbeiten, sodass sie folgendes enthält:
    - Die Zeile `listen_addresses = []` muss leer bleiben !
    - In der Zeile `server_names = ['cloudflare-security']`
    - Um andere oder zusätzliche (Fallback) Upstream-Server zu nutzen die sogenannten "server-namen" aus der offiziellen Liste kopieren: [dnscrypt.info/public-servers/](https://dnscrypt.info/public-servers/)
        - `'cloudflare'`: Cloudflare DNS (anycast) - aka 1.1.1.1 / 1.0.0.1 (over DoH)
        - `'cloudflare-security'`: Cloudflare DNS with malware blocking - aka 1.1.1.2 / 1.0.0.2 (over DoH)
        - `'adguard-dns'`: Remove ads and malware (over DNSCrypt)
        - `'adguard-dns-doh'`: Remove ads and malware (over DoH)
        - `'quad9-dnscrypt-ip4-filter-pri'`: Quad9 (anycast) dnssec/no-log/filter - aka 9.9.9.9 - 149.112.112.9 - 149.112.112.112 (over DNSCrypt)
        - `'quad9-doh-ip4-port443-filter-pri'`: Quad9 (anycast) dnssec/no-log/filter - aka 9.9.9.9 - 149.112.112.9 - 149.112.112.112 (over DoH)
    - Der restliche Inhalt der Konfig bleibt unverändert.
```
# Empty listen_addresses to use systemd socket activation
listen_addresses = []

# Populate `server_names` with desired DoH/DNSCrypt upstream DNS servers listed in https://dnscrypt.info/public-servers/.
# Example for Cloudflare malware-blocking DNS:
server_names = ['cloudflare-security', 'quad9-dnscrypt-ip4-filter-pri']
```

- Dienste neu starten
```
$ sudo systemctl restart dnscrypt-proxy.socket
$ sudo systemctl restart dnscrypt-proxy.service
$ sudo systemctl restart pihole-FTL.service
```

- Status der Dienste einsehen
```
$ sudo systemctl status dnscrypt-proxy.socket
$ sudo systemctl status dnscrypt-proxy.service
$ sudo systemctl status pihole-FTL.service
```

### Forwarder in Pi hole als Upstream einstellen
- Nun im `Pi hole Dashbaord` unter `Settings > DNS > Upstram DNS-Servers` `127.0.0.1#5053` eintragen, um dnscrypt-proxy zu verwenden.
- Speichern nicht vergessen.


### Updates von dnscrypt-proxy
Wenn dnscrypt-prxy wie in dieser Anleitung beschrieben über den Paketmanager apt installiert wurde, dann wird dieser mit den System-Updates aktualisiert.

- System-Updates mit Paketmanager apt
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt autoremove --purge && sudo apt autoclean
```


----------------------------------------------------------------------------------------------------------------


## dnscrypt-proxy deinstallieren
- Wenn dnscrypt-proxy über den apt-Paketmanager installiert wurde:
```
$ sudo apt remove --purge dnscrypt-proxy
```
- Im Pi hole dann ggf. den Upstram DNS-Server anpassen.


----------------------------------------------------------------------------------------------------------------


## Optional Testen, ob Verschlüsselung angewendet wird

- Logs überprüfen
    - Wenn in den Zeilen etwas vergelichbares steht, wie `[UPSTREAM-SERVER-XYZ] OK (DNSCrypt)` oder  `[UPSTREAM-SERVER-XYZ] OK (DoH)`
```
$ sudo journalctl -u dnscrypt-proxy -f
```

- aktive Verbindungen anzeigen
```
$ sudo ss -tulnp | grep dnscrypt-proxy
```


----------------------------------------------------------------------------------------------------------------


# 4. Optional: Systemseitige Optimierung - Limit für offene Dateideskriptoren
- Limit für offene Dateideskriptoren bearbeiten:
    - Um die Verarbeitung in Lastszenarien zu beschleunigen, kann man das Limit für offene Dateideskriptoren für einzelne Dienste angepasst werden.
    - Das Erhöhen des Limits ermöglicht mehrere gleichzeitige Verbindungen, was die Performance unter Last verbassern kann.
    - Bei der Nutzung von Pi hole in einem kleinen Heimnetzwerk sollte das nicht nötig sein.


### Limit für offene Dateideskriptoren für Pi hole anpassen
- Override-Datei für Pi hole-FTL:
```
$ sudo mkdir -p /etc/systemd/system/pihole-FTL.service.d/
$ sudo nano /etc/systemd/system/pihole-FTL.service.d/override.conf
```

- Folgendes einfügen:
```
[Service]
LimitNOFILE=8192
```

- Neustart der Dienstes:
```
$ sudo systemctl daemon-reexec
$ sudo systemctl daemon-reload
$ sudo systemctl restart pihole-FTL
```

- Damit alles vollständig und sauber geladen wird, noch ein Neustart:
```
$ sudo reboot
```

- Limit überprüfen:
```
$ sudo systemctl show pihole-FTL | grep LimitNOFILE
```

- Logs optional prüfen:
```
$ sudo tail -f /var/log/pihole/FTL.log
```

### Limit für offene Dateideskriptoren für dnscrypt-proxy anpassen
- Override-Datei für dnscrypt-proxy:
```
$ sudo mkdir -p /etc/systemd/system/dnscrypt-proxy.service.d/
$ sudo nano /etc/systemd/system/dnscrypt-proxy.service.d/override.conf
```

- Folgendes einfügen:
```
[Service]
LimitNOFILE=8192
```

- Neustart der Dienstes:
```
$ sudo systemctl daemon-reexec
$ sudo systemctl daemon-reload
$ sudo systemctl restart dnscrypt-proxy.service
```

- Damit alles vollständig und sauber geladen wird, noch ein Neustart:
```
$ sudo reboot
```

- Limit überprüfen:
```
$ sudo systemctl show dnscrypt-proxy.service | grep LimitNOFILE
```


----------------------------------------------------------------------------------------------------------------


# 5. Cloudflared deinstallieren

### Wenn Cloudflared manuell installiert wurde
- systemctl-Dienst entfernen & cloudflared-User entfernen
```
$ sudo systemctl stop cloudflared
$ sudo systemctl disable cloudflared
$ sudo systemctl daemon-reload
$ sudo deluser cloudflared
```

- Dateien und Konfiguratienen löschen
```
$ sudo rm /etc/default/cloudflared
$ sudo rm /etc/systemd/system/cloudflared.service
$ sudo rm /usr/local/bin/cloudflared
$ sudo rm /etc/cron.weekly/cloudflared-updater
$ sudo apt purge cloudflared
```

- Falls ein Cronjob verwendet wurde, diesen entfernen !
    - Falls ein anderer Pfad verwendet wurde, diesen entsprechend anpassen.
```
$ sudo nano /etc/cron.d/cloudflared-update
```

### Wenn Cloudflared über den "service install" installiert wurde
```
$ sudo cloudflared service uninstall
$ sudo systemctl daemon-reload
```


----------------------------------------------------------------------------------------------------------------
