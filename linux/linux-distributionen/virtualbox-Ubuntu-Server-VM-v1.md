# Ubuntu-Server in VM auf Host mit Debian-basierter-Distribution mit [VirtualBox](https://www.virtualbox.org/) aufsetzen



## Ressourcen:
- Die Anforderungen hängen von den individuellen Vorhaben und den zur Verfügung stehenden Ressourcen ab.
- Folgend sind einige Anhaltspunkte:
	- [CPU](https://de.wikipedia.org/wiki/Prozessor): mind. 2 Kerne, 4 Kerne für umfangreichere Nutzung
	- [RAM](https://de.wikipedia.org/wiki/Random-Access_Memory): mind. 2GB RAM, für etwas mehr Performance **ab** 4GB RAM
	- Disk: 10GB für minimalistische Installation, **ab** 20GB für "Produktiv-Systeme"

- Bei VMs ist es im Nachhinein immer schwierig, den Speicherplatz zu verkleinern, den Speicher zu vergrößern, ist hingegen meistens relativ einfach.


## Vorbereitungen
- [Ubuntu-Server ISO](https://ubuntu.com/download/server) oder wahlweise eine andere Linux-Distribution herunterladen.



## VirtualBox auf Ubuntu/Debian-basierten-Distributionen installieren
- Vorteile von [VirtualBox](https://www.virtualbox.org/)
	- gute Performance
	- Erstellen von Snapshots möglich
	- VirtualBox ist [Open Source](https://de.wikipedia.org/wiki/Open_Source)


- Terminal auf Host-System öffnen
```
$ sudo apt update
$ sudo apt install virtualbox
```

- Nun VirtualBox öffnen
- Neue virtuelle Maschine erstellen:
	- `Neu`
	- Namen vergeben
	- Pfad der VM bestimmen
	- ISO File auswählen
	- Hostname festlegen
	- Ressourcen wählen (RAM/CPU)
	- VM anlegen

- VM starten und System installieren.


-------------------------------------------------------------------------------------------------------------


# Netzwerk-Adapter für eine VM

### NAT & Host-Only
- Wenn man über den Host auch offline auf die VM z.B. über [SSH](https://de.wikipedia.org/wiki/Secure_Shell) zugreifen möchte und wenn der Host mit dem Internet verbunden ist, die VM ebenfalls Internetzugriff haben soll, können die folgenden Einstellungen verwendet werden:
- Netzwerk-Adapter1: Host-Only
- Netzwerk-Adapter2: NAT

- [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) aktivieren (von vbox)
	- VirtualBox öffnen
	- `Tools`
	- `Host-only- Netzwerke`
	- `Create`
	- es sollte sich ein neues "Netzwerk" mit dem Namen `vboxnet0` erstellen
	- DHCP-Server: `Aktivieren`
	- `Anwenden`

- Einstellungen der VM aufrufen und Netzwerk-Adapter auswählen
	- `VM herunterfahren`
	- Rechtsklick auf entsprechende VM
	- Einstellungen öffnen
	- `Netzwerk`
	- `Adapter 1`
	- `Aktivieren`
	- `Host-only Adapter` auswählen
	- Name: `vboxnet0`
	- `Adapter 2`
	- `Aktivieren`
	- `NAT` auswählen
	- `OK`

- VM starten
- Auf Host mit `$ ifconfig` oder `$ ip a` prüfen, ob ein `vboxnet0`-Netzwerk angezeigt wird.
- IN VM ebenfalls mit `$ ifconfig` oder `$ ip a` prüfen, ob eine IP vom `vboxnet0`-Netzwerk und eine vom `NAT`-Netzwerk vergeben wurde.
- Standardmäßig wird für das Host-only-Netzwerk ein 192.168.56.0/24-Netzwerk erstellt.
- Das NAT-Netzwerk hingegen vergibt meistens folgenden IP-Bereich: IP 10.0.2.x

### Nur ein Adapter funktioniert -> Lösung
- Auf VM:
- `$ ifconfig` oder `$ ip a`
- Prüfen, ob `vboxnet0` des Host-only-Netzwerkes (Adapter1) als `enp0s3` angezeigt wird und IP-Adresse vorhanden ist.
- Danach prüfen, ob `enp0s8` (NAT-Netzwerk-Adapter - Adapter2) angezeigt wird.
- Wenn `enp0s8` nicht erscheint, dann VM herunterfahren und neu starten, erneut überprüfen.
- Folgenden Befehl ausführen, um [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) für das Interface `enp0s8` anzufordern:
```
$ sudo dhclient enp0s8
```

- Wenn `dhclient` nicht installiert ist:
	- VM herunterfahren
	- Host-only-Adapter temporär deaktivieren
	- VM starten
	- Wenn Internetzugriff besteht, `isc-dhcp-client` installieren:
```
$ sudo apt update
$ sudo apt install isc-dhcp-client
```
- Nun VM herunterfahren
- Den temporär deaktivierten Host-only-Adapter wieder aktivieren
- VM starten
- Befehl zum Anfordern der IP-Adresse ausführen:
```
$ sudo dhclient enp0s8
```

-------------------------------------------------------------------------------------------------------------


# Lösung für `„Kernel driver not installed (rc=-1908)“`

- Wenn die VM nicht startet und der Fehler `„Kernel driver not installed (rc=-1908)“` angezeigt wird.
- Befehle auf dem Host ausführen
```
$ sudo apt update
$ sudo apt install virtualbox-dkms virtualbox-ext-pack linux-headers-$(uname -r) dkms
```
- Mit `OK`
- und `YES` bestätigen

```
$ sudo modprobe vboxdrv
```

- Wenn `modprobe vboxdrv` einen Fehler anzeigt liegt es meistens an Secure Boot.


-------------------------------------------------------------------------------------------------------------
