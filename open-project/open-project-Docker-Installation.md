# [OpenProject](https://www.openproject.org/) über Docker installieren (inkl. [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy))

`Anleitung erstellt am 26.2.2026`

`OpenProject erfolgreich auf Ubuntu 24.04 installiert`


## Inhaltsverzeichnis
1. Was ist [OpenProject](https://www.openproject.org/) ?
2. interne Domain (DNS-Eintrag) / IP-Adresse
3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
4. [Docker](https://www.docker.com/) installieren 
5. Zertifikate
6. [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy) [Nginx](https://nginx.org/)
7. OpenProject installieren
8. OpenProject aufrufen
9. Ersteinrichtung
10. Einrichtung von OpenProject
11. CA-Zertifikat in Clients importieren


-----------------------------------------------------------------------------------------------


# 1. Was ist [OpenProject](https://www.openproject.org/) ?
- [OpenProject](https://www.openproject.org/) ist eine [Open Source](https://de.wikipedia.org/wiki/Open_Source) Projektmanagement-Software mit Fokus auf `Datenschutzfreundlichkeit`.


- Vorteile von OpenProject
    - [Open Source](https://de.wikipedia.org/wiki/Open_Source)
    - datenschutzfreundlich
    - Webbasierte Lösung (Browser ist der Client)
    - effiziente Teamorganisation
    - Erstellung und Visualisierung von Projektplänen


-----------------------------------------------------------------------------------------------


# 2. interne Domain (DNS-Eintrag) / IP-Adresse
- Diese Anleitung beschränkt sich auf die Verwendung der IP-Adresse des Servers bzw. eines internen DNS-Eintrags.


### interner DNS-Eintrag
- Wenn man einen internen DNS-Eintrag verwendet, dann sollte man unbedingt Folgendes beachten:
    - Typischerweise interpretieren Browser Einträge wie ".lan", ".home", ".intern" als Suchbegriff. Das kann man ausgleichen, indem man das Protokoll, z.B. `https://` davor schreibt, jedoch ist das eine unbequeme Lösung.
    - Außerdem sollte man auf `KEINEN FALL` ".local" verwenden, da hier Probleme auftreten können!
    - Stattdessen sollte man auf `offiziell reservierte Test-TLDs` setzen, das wären z.B. `.example` oder `.test`.


# 3. [Ubuntu-Server](https://ubuntu.com/download/server) installieren
- Dem Server eine statische [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) vergeben oder im DHCP-Server eine [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) "reservieren".
- Ubuntu-Server installieren.
- Installation beenden.
- Auf Ubuntu-Server einloggen / [SSH](https://de.wikipedia.org/wiki/Secure_Shell)-Verbindung herstellen.
- `$ ssh user@IP-Adresse`


### Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt full-upgrade
$ sudo apt autoremove --purge && sudo apt autoclean
```
- [Snap](https://snapcraft.io/)-Paketmanager:
```
$ sudo snap refresh
```


### Auf Server einloggen & [SSH](https://de.wikipedia.org/wiki/Secure_Shell) aktivieren, um remote auf den Server zugreifen zu können
- SSH aktivieren (und in Autostart)
```
$ sudo systemctl start ssh
$ sudo systemctl enable ssh
$ sudo systemctl status ssh
```

- in der [Firewall](https://de.wikipedia.org/wiki/Firewall) SSH erlauben - [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
```
$ sudo ufw allow ssh
$ sudo ufw status
```

- Firewall ggf. aktivieren
```
$ sudo ufw enable
```


### Netzwerkeinstellungen/ Dateimanagement
- [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) von [Ubuntu-Server](https://ubuntu.com/download/server) herausfinden
```
$ ip a
```

- Optional mit `ifconfig` (muss aber zusätzlich installiert werden):
```
$ sudo apt install net-tools
$ ifconfig
```

- Das beste Dateimanagement im Terminal mit dem [Midnight Commander](https://midnight-commander.org/)
```
$ sudo apt install mc
$ sudo mc
```


-------------------------------------------------------------------------------------------------------------


# 4. [Docker](https://www.docker.com/) installieren 
- Empfohlene Version von Docker: [Docker Engine](https://docs.docker.com/engine/install/)
- Falls eine andere Version von Docker installiert wurde, sollte diese entfernt werden und die Docker Engine installiert werden.
- Falls bereits weitere Server über eine inoffizielle/ alte Version laufen, müssen diese ggf. neu aufgesetzt werden.
- Möglicherweise sind vom Betriebssystem bereits inoffizielle Pakete installiert, die mit den offiziellen in Konflikt geraten könnten.


### ggf. deinstallieren der alten Version, um Konflikte zu mit Docker-Engine verhindern
- Pakete die deinstalliert werden müssen:
```
docker.io
docker-compose
docker-compose-v2
docker-doc

podman-docker
containerd
runc
```

- Deinstallationsbefehl:
   - Der Paketmanager apt könnte fälschlicherweise melden, dass die Pakete nicht installiert sind.
```
$ sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)

```

- Docker-ce, docker-ce-cli und containerd.io entfernen
```
$ sudo apt purge docker-ce docker-ce-cli containerd.io
```

- Alte Images, Container und Volumes aus `/var/lib/docker/` und `/var/lib/containerd` löschen!
```
$ sudo rm -rf /var/lib/docker
$ sudo rm -rf /var/lib/containerd
```

- ggf. Docker-Repository und GPG-Schlüssel entfernen
```
$ sudo rm /etc/apt/sources.list.d/docker.list
$ sudo rm /etc/apt/keyrings/docker.gpg
$ sudo apt update
```

- alte Pakete entfernen und aktualisieren
```
$ sudo apt autoremove --purge && sudo apt autoclean
$ sudo apt update
```


### Docker-Engine über den Paketmanager apt
- apt-repository hinzufügen
    - Dockers offizielle GPG-Key hinzufügen
```
$ sudo apt update
$ sudo apt install ca-certificates curl
$ sudo install -m 0755 -d /etc/apt/keyrings
$ sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
$ sudo chmod a+r /etc/apt/keyrings/docker.asc
```

- apt-repository hinzufügen (das ist ein Befehl)
    - Repository zu apt Source hinzufügen
```
$ sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```
```
$ sudo apt update
```

- Docker-Pakete installieren:
```
$ sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Docker-Version prüfen
```
$ docker --version
```

### Autostart von [Docker](https://www.docker.com/)
- Docker manuell installiert
```
$ sudo systemctl start docker
$ sudo systemctl enable docker
$ sudo systemctl status docker
```


-------------------------------------------------------------------------------------------------------------


# 5. Zertifikate

### selbstsigniertes Zertifikat erstellen
- Ordner erstellen
```
$ mkdir OpenProject-Zertifikate
$ cd OpenProject-Zertifikate
```
- San-Konfig erstellen
```
$ sudo nano san.cnf
```
- einfügen & DNS-Namen, IP-Adresse und Informationen anpassen
    - Die Punkte `stateOrProvinceName, localityName, organizationName` sind `optional` und können vollständig aus der Konfig gelöscht werden.

```
[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name
req_extensions     = req_ext
x509_extensions    = v3_req
prompt             = no

[ req_distinguished_name ]
countryName            = DE
stateOrProvinceName    = DeinBundesland
localityName           = DeineStadt
organizationName       = DeineFirma
commonName             = IP-ADRESSE

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1   = OpenProject.local.com
IP.1    = IP-ADRESSE

[ v3_req ]
subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + OpenProject-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit


- OpenProjectCA erstellen (Client-Zertifikat):
    - Den Platzhalter "MEINEORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `OpenProjectCA.crt` und `OpenProjectCA.key` erstellt wurden.
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout OpenProjectCA.key -out OpenProjectCA.crt -subj "/C=DE/CN=MEINEORGANISATION-CA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `OpenProjectCA.srl, OpenProject.crt, OpenProject.csr, OpenProjectCA.key` erstellt wurden.
```
$ openssl req -new -nodes -keyout OpenProject.key -out OpenProject.csr -config san.cnf
```
```
$ openssl x509 -req -in OpenProject.csr -CA OpenProjectCA.crt -CAkey OpenProjectCA.key -CAcreateserial -out OpenProject.crt -days 3650 -extensions v3_req -extfile san.cnf
```


-------------------------------------------------------------------------------------------------------------


# 6. [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy) [Nginx](https://nginx.org/)


### [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy) [Nginx](https://nginx.org/) installieren
```
$ sudo apt update
$ sudo apt install nginx
```

### [Nginx](https://nginx.org/) konfigurieren
- neue Konfig-Datei erstellen
```
$ sudo nano /etc/nginx/sites-available/OpenProject
```

- folgendes einfügen:
    - Platzhalter anpassen:
    - `IP-ADRESSE`: IP-Adresse des Servers und ggf. auch OpenProject.example anpassen
    - `/home/USERNAME/OpenProject-zertifikate/` Pfad ggf. anpassen
```
server {
    listen 80;
    server_name IP-ADRESSE;
    return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name IP-ADRESSE;

  ssl_certificate     /home/USERNAME/OpenProject-Zertifikate/OpenProject.crt;
  ssl_certificate_key /home/USERNAME/OpenProject-Zertifikate/OpenProject.key;

  proxy_redirect    off;

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;

    proxy_pass http://127.0.0.1:8080;
  }
}
```
- Mit `STRG+X` speichern und schließen.


### Aktivieren der Konfiguration, falls noch nicht geschehen
- Das nur, wenn Nginx zuvor noch nicht mit dieser Konfig verwendet wurde.
```
$ sudo ln -s /etc/nginx/sites-available/OpenProject /etc/nginx/conf.d/openproject.conf
```

- Konfig testen
	 - Wenn die Meldung `syntax is ok` erscheint, ist alles in ordnung.
```
$ sudo nginx -t
```

- [Nginx](https://nginx.org/) neu starten
```
$ sudo systemctl restart nginx
```


-------------------------------------------------------------------------------------------------------------


# 7. OpenProject installieren


### zufällige Ziffernfolge erstellen
- zufällige Ziffernfolge erstellen (64-Ziffern):
    - Dies dient der Sicherheit bei der Verschlüsselung der Sitzungen, Cookies etc.
    - Siehe die offizielle Dokumentation: [offiziellen Dokumentation](https://www.openproject.org/de/docs/installation-and-operations/installation/docker/)
```
$ head /dev/urandom | tr -dc A-Za-z0-9 | head -c 64 ; echo ''
```
- Die Ausgabe in einem Passwortmanager notieren und in den Docker-Befehl aus dem nächsten Schritt eingeben.
- Falls mehrere Instanzen genutzt werden, `niemals` denselben Schlüssel verwenden !


### Erstellung von Ordern für Datenpersistenz
- In dem folgenden Ordner, der erstellt wird, werden Daten wie Anhänge oder Dokumente etc. gespeichert.
    - Die PostgreSQL-Datenbank bleibt jedoch im Docker-Container.
```
$ sudo mkdir -p /var/lib/openproject/{pgdata,assets}
```
- Berechtigungen setzen:
```
$ sudo chown -R 1000:1000 /var/lib/openproject/
```


### OpenProject installieren (Konfig für Nutzung mit [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy))
- Folgendes als ein Befehl ausführen:
    - Das ist eine angepasste Konfig für die Nutzung mit Reverse-Proxy und HTTPS aus der [offiziellen Dokumentation](https://www.openproject.org/de/docs/installation-and-operations/installation/docker/)
    - Folgendes unbedingt anpassen: `IP-ADRESSE-DES-SERVERS` und `PLATZHALTER-XXXXXXX` durch die generierten 64-Zahlen ersetzen !
```
$ sudo docker run -d --restart unless-stopped -p 8080:80 --name openproject \
  -e OPENPROJECT_HOST__NAME=IP-ADRESSE-DES-SERVERS \    # IP-Anpassen !
  -e SECRET_KEY_BASE=PLATZHALTER-XXXXXXX \              # PLATZHALTER-XXXXXXX mit den generierten Zahlen ersetzen
  -e OPENPROJECT_HTTPS=true \
  -e OPENPROJECT_DEFAULT__LANGUAGE=de \
  -v /var/lib/openproject/pgdata:/var/openproject/pgdata \
  -v /var/lib/openproject/assets:/var/openproject/assets \
  openproject/openproject:17
```

### Autostart des Containers überprüfen:
- Überprüfen, ob der Container OpenProject im Autostart ist:
```
$ sudo docker inspect -f '{{ .HostConfig.RestartPolicy.Name }}' openproject
```

- Falls der Autostart nicht aktiviert ist, dann aktivieren:
```
$ sudo docker update --restart unless-stopped openproject
```


### Container Befehle (Optional/ wenn benötigt)
- Docker Container stoppen
```
$ docker stop openproject
```

- Docker Container starten
```
$ docker start openproject
```

- Status überprüfen
```
$ sudo docker ps
```


-------------------------------------------------------------------------------------------------------------


# 8. OpenProject aufrufen
- Nun kann OpenProject über die IP-Adresse aufgerufen werden.
- Dafür im Browser `https://IP-Adresse` eingeben.


- Es muss ggf. noch der Port 443 für HTTPS mit dem `Firewall Manager` [ufw](https://wiki.ubuntuusers.de/ufw/) geöffnet werden.
```
$ sudo ufw status
$ sudo ufw allow 443
$ sudo ufw enable
```

-------------------------------------------------------------------------------------------------------------


# 9. Ersteinrichtung
- Browser öffnen und `https://IP-ADRESSE` öffnen.

### Admin-Benutzer
- Die default Zugangsdaten sind: `admin`, `admin`
- Nach der ersten Anmeldung muss man das Passwort ändern.

### Backups
- Das Einrichten und Wiederherstellen von Backups wird in der Anleitung [Backups-von-Open-Project-erstellen](xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx) erläutert.

> folgt in Kürze


-------------------------------------------------------------------------------------------------------------


# 10. Einrichtung von OpenProject

### Konto Einstellungen
- Einstellungen für den Admin-Benutzer finden sich, indem man auf das Benutzersymbol (OA) oben rechts klickt und dann auf `Kontoeinstellungen` gehen.
- Hier können nun die E-Mail-Adresse ergänzt, das Passwort geändert und die 2-Faktor-Authentifizierung für den Admin-Benutzer aktiviert werden.
- Unter dem Reiter `Sitzungsverwaltung` kann man die aktiven Sitzungen einsehen und verwalten.
- Zusätzlich finden sich Einstellungen zu den `Benachrichtigungen` und dem `Profilfoto`.


### Administration
- Hier können nun administrative Einstellungen gesetzt werden, es folgen einige Erläuterungen.


#### Benutzer und Berechtigungen
- `Benutzer`
    - Benutzer anlegen, verwalten und löschen
    - Beim Anlegen von Konten ist besonders nützlich, dass man die Benutzerkonten direkt mit einem (temporären) Passwort anlegen kann und dann die Option `Erzwinge Passwortwechsel beim nächsten Login` aktivieren kann.
    - Außerdem gibt es auch die Möglichkeit, einzelne Benutzerkonten zu sperren, ohne diese löschen zu müssen.
- `Rollen und Rechte`
    - Hier können Rollen und Rechte verwaltet werden. Diese Rollen können dann den Benutzerkonten zugewiesen werden.
    - Mit dem Klick auf eine Rolle können auch die Berechtigungen für die entsprechende Rolle angepasst werden.
- `Berechtigungsübersicht`
    - Hier können die Berechtigungen für alle Gruppen in einer Übersicht eingesehen und bearbeitet werden.
    - Bei den Rollen wird zwischen `"Projekt-Rollen"` und `"Globalen-Rollen"` unterschieden.
    - Dabei sind die Projekt-Rollen nur für Projekte und die globalen Rollen können an Benutzer vergeben werden.
    - Benutzerkonten für die normale Nutzung sollten `niemals` volle Admin-Berechtigungen haben. Wenn einzelne Admin-Berechtigungen benötigt werden, dann sollten eingeschränkte Rollen mit nur den nötigen Berechtigungen an den entsprechenden User vergeben werden. z.B. kann eine "globale Rolle" angelegt werden, mit der Benutzer verwaltet werden können, sodass autorisierte Mitarbeiter/ Nutzer andere Benutzerkonten verwalten können.


#### Authentifizierung
- `Anmeldung und Registrierung > Anmeldung`
    - Einstellen von `Session läuft ab`, um einzustellen nach wie viel Zeit "Inaktivität" Benutzer ausgeloggt werden.
- `Anmeldung und Registrierung > Registrierung`
    - Festlegen, ob Benutzer sich selbständig registrieren können.
    - Wenn `Deaktiviert` ausgewählt ist, können nur Admins Benutzer erstellen und verwalten.
- `Anmeldung und Registrierung > Passwörter`
    - Hier kann die Richtlinie für Passwörter und Anzahl von Fehlversuchen, sowie die Sperrzeit festgelegt werden.
- `Anmeldung und Registrierung > Zwei-Faktor-Authentifizierung`
    - Einstellung, um Zwei-Faktor-Authentifizierung zu erzwingen.


-------------------------------------------------------------------------------------------------------------


# 11. CA-Zertifikat in Clients importieren


## Zertifikat von Server mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) herunterladen

### Windows:
- Für Windows gibt es das Tool [WinSCP](https://winscp.net/eng/index.php).

### Linux:
- Zertifikat über SCP kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/home/username/.../cert.crt /dein/lokales/verzeichnis/zielordner
```

- Wenn ein Berechtigungsfehler erscheint (Befehle auf dem Server ausführen):
```
$ ssh user@IP-Adresse
```
```
$ cd /home/username/OpenProject-Zertifikate
$ ls
```
```
$ sudo chmod 644 home/username/OpenProject-Zertifikate/OpenProjectCA.crt
```
- Nun nochmal mit SCP versuchen, die Datei zu kopieren.


## CA-Zertifikat in Browser als vertrauenswürdig einstufen
- Die `Chrome basierten Browser` nutzen den `Zertifikatsspeicher des Betriebssystems`.
- Der `Firefox` hingegen nutzt per default nur den `eigenen Zertifikatsspeicher`.

### Zertifikat manuell in [Firefox](https://www.firefox.com/de/) als vertrauenswürdig einstufen
- Firefox öffnen
- `Einstellungen`
- `Sicherheit und Datenschutz`
- zum Punkt `Zertifikate` und auf `Zertifikate anzeigen`
- `Zertifizierungsstellen`
- `Importieren`
- Setzen des Häkchens bei `Dieser CA vertrauen, um Websites zu identifizieren`
- `OK`
- Firefox neu starten.


## In einer [Active Directory](https://de.wikipedia.org/wiki/Active_Directory) Umgebung, mit GPO-Richtlinie Zertifikate an Clients automatisch verteilen
- Wenn eine Active Directory (Domäne) genutzt wird, kann eine Gruppenrichtlinie (GPO) erstellt werden, um das CA-Zertifikat automatisch an alle Clients zu verteilen.
- Dabei muss ggf. das Zertifikat in den Zertifikatsspeicher des Betriebssystems für die Chrome-basierten Browser und ggf. separat in den Firefox-Zertifikatsspeicher verteilt werden, wobei es auch möglich ist, Firefox über die GPO so zu konfigurieren, dass dieser den Zertifikatsspeicher des Betriebssystems verwendet.


-------------------------------------------------------------------------------------------------------------


