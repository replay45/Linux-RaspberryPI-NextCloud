# Netzwerkkonfiguration anpassen - Ubuntu-Server

`Anleitung erstellt am 17.8.2025`


## Inhaltsverzeichnis
1. Nur [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) unter [Ubuntu-Server](https://ubuntu.com/download/server) permanent anpassen
2. Statische IP-Einstellungen bearbeiten (inkl. DNS-Server)


--------------------------------------------------------------------------------------------------------------


# 1. Nur [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) unter [Ubuntu-Server](https://ubuntu.com/download/server) permanent anpassen
Geeignet, wenn nur DNS-Server angepasst werden sollen und ansonsten [DHCP](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) genutzt wird.


- Konfigurationsdatei von systemd-resolved bearbeiten:
```
$ sudo nano /etc/systemd/resolved.conf
```

- Nun folgende Zeile hinzufügen oder ändern:
	- Beispielhaft DNS-Server von Cloudflare, es kann aber auch IP des lokalen DNS-Servers verwendet werden.
```
[Resolve]
DNS=1.1.1.1
FallbackDNS=1.0.0.1
```

- Dienst neu starten:
```
$ sudo systemctl restart systemd-resolved
```
```
$ sudo systemctl enable systemd-resolved
```

- DNS-Einstellungen prüfen:
```
$ cat /etc/resolv.conf
```

--------------------------------------------------------------------------------------------------------------


# 2. Statische IP-Einstellungen bearbeiten (inkl. DNS-Server)

- Hinweis zur Formatierung:
	- Die YAML-Formatierung und Reihenfolge kann je nach genutztem Interface auf dem eigenen Server von der Beispielkonfiguration abweichen.

- Netplan-Konfig finden:
	- Die angezeigte Konfig mit dem nächsten Befehl öffnen.
```
$ ls /etc/netplan/
```

- Netplan-Konfigurationsdatei öffnen (Dateiname anpassen):
```
$ sudo nano /etc/netplan/NETKONFIG-NAME.yaml
```

- entsprechenden Interface-Namen herausfinden:
```
$ ip a
```

- Netzwerkeinstellungen bearbeiten (Beispieleinstellungen):
	- Die bestehende Konfig (Formatierung & Reihenfolge) kann von der Beispielkonfiguration abweichen.

```
network:
  version: 2
  ethernets:
    ens8:    # Ersetzen von "ens8" mit eigenem Interface-Namen
      dhcp4: no
      addresses: [192.168.1.100/24]    # Statische IP/24, wenn andere Subnetmaske verwendet wird entsprechend anpassen
      gateway4: 192.168.1.1    # Standard-Gateway
      nameservers:
        addresses: [1.1.1.1,1.0.0.1]  # Adressen mit "," getrennt, OHNE Leerzeichen !
```

- Änderungen anwenden:
```
$ sudo netplan apply
```

- Netplan-Konfiguration Syntax prüfen:
```
$ sudo netplan --debug apply
```

- IP-Einstellungen überprüfen:
```
$ ip a
```

- Route-Einstellungen prüfen:
```
$ ip route
```

- DNS-Einstellungen prüfen:
```
$ cat /etc/resolv.conf
```

--------------------------------------------------------------------------------------------------------------

