# [Wiki.js](https://js.wiki/) über [Docker](https://www.docker.com/) auf einem [Ubuntu-Server](https://ubuntu.com/download/server) installieren

`Anleitung erstellt am 7.3.2025, zuletzt bearbeitet am 9.9.2026`

- Link zur [Dokumentation](https://docs.requarks.io/)


# 1. [Ubuntu-Server](https://ubuntu.com/download/server) installieren (inkl. [Docker](https://www.docker.com/))
- Ubuntu-Server installieren.
- Installation beenden.
- Auf Ubuntu-Server einloggen / [SSH](https://de.wikipedia.org/wiki/Secure_Shell)-Verbindung herstellen.
- `$ ssh user@IP-Adresse`


# 2. Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove --purge && sudo apt autoclean
$ sudo snap refresh
```


# 3. Auf Server einloggen & [SSH](https://de.wikipedia.org/wiki/Secure_Shell) aktivieren, um remote auf den Server zugreifen zu können
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


-------------------------------------------------------------------------------------------------------------


# 4. [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy) [Nginx](https://nginx.org/) & selbstsigniertes Zertifikat


### Ein selbstsigniertes Zertifikat erstellen für [Wiki.js](https://js.wiki/)
- Ordner erstellen
```
$ mkdir wiki-zertifikate
$ cd wiki-zertifikate
```

- San Konfig erstellen
```
$ sudo nano san.cnf
```
- einfügen & DNS-Namen, IP-Adresse und Informationen anpassen
	- Die Punkte `stateOrProvinceName, localityName, organizationName` sind `optional` und können vollständig `aus der Konfig gelöscht werden`.
	- Optional kann unter `organizationName` der Name der Firma/Organisation etc. angegeben werden.
	- Die Platzhalter `DNS.1 = wiki.lokal.com` und `IP.1 = IP-ADRESSE` mit entsprechenden Werten anpassen.
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
DNS.1   = wiki.lokal.com
IP.1    = IP-ADRESSE

[ v3_req ]
subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + wiki-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit

-  wikiCA erstellen (Client-Zertifikat):
    - Den Platzhalter "MEINEORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `wikiCA.crt` und `wikiCA.key` erstellt wurden.
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout wikiCA.key -out wikiCA.crt -subj "/C=DE/CN=MEINEORGANISATION-Wikijs-CA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `wikiCA.srl, wiki.crt, wiki.csr, wikiCA.key` erstellt wurden.
```
$ openssl req -new -nodes -keyout wiki.key -out wiki.csr -config san.cnf
```
```
$ openssl x509 -req -in wiki.csr -CA wikiCA.crt -CAkey wikiCA.key -CAcreateserial -out wiki.crt -days 3650 -extensions v3_req -extfile san.cnf
```


### Nginx konfigurieren
- [Nginx](https://nginx.org/) - [Web-Server](https://de.wikipedia.org/wiki/Nginx) installieren
```
$ sudo apt update
$ sudo apt install nginx
```

- [Nginx](https://nginx.org/)-Konfiguration erstellen
```
$ sudo nano /etc/nginx/sites-available/wiki
```

- folgendes einfügen:
    - Platzhalter anpassen:
    - `IP-ADRESSE`: IP-Adresse des Servers und ggf. auch wiki.lokal.com anpassen
    - `/PATH/TO/wiki-zertifikate/` Pfad ggf. anpassen
```
server {
    listen 80;
    server_name IP-ADRESSE wiki.lokal.com;
    return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name IP-ADRESSE wiki.lokal.com;

  ssl_certificate     /PATH/TO/wiki-zertifikate/wiki.crt;
  ssl_certificate_key /PATH/TO/wiki-zertifikate/wiki.key;

  proxy_redirect    off;

  location / {
    proxy_set_header Host $host;

    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Port 443;
    proxy_set_header X-Forwarded-Ssl on;

    proxy_pass http://127.0.0.1:3000;
  }
}
```
- Mit `STRG+X` speichern und schließen.



### Aktivieren der Konfiguration, falls noch nicht geschehen
- Das nur, wenn Nginx zuvor noch nicht mit dieser Konfig verwendet wurde.
```
$ sudo ln -s /etc/nginx/sites-available/wiki /etc/nginx/conf.d/wiki.conf
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


### [Firewall](https://ubuntu.com/server/docs/firewalls) vom [Ubuntu-Server](https://ubuntu.com/download/server) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- HTTPS-Anfragen erlauben und Status der Firewall abrufen
    - HTTPS Anfragen erlauben um Weiterleitung auf HTTPS (HSTS) zu ermöglichen
```
$ sudo ufw allow https
$ sudo ufw allow http
$ sudo ufw enable
$ sudo ufw status
```


### Optional prüfen, ob Zertifikat gültig und aktiv ist
- im Browser:
	- Auch wenn der Browser das Zertifikat nicht als vertrauenswürdig einstuft, besteht trotzdem eine HTTPS-Verbindung.
	- Im Browser die Seiteninformationen aufrufen (Vorhängeschloss-Symbol)
	- Zertifikat-Details aufrufen
	- Angaben prüfen

- Zertifikat des Servers prüfen
```
$ openssl s_client -connect IP-Adresse:443 -showcerts
```


-------------------------------------------------------------------------------------------------------------


### CA-Zertifikat in Clients importieren


### Windows:
- Für Windows gibt es das Tool [WinSCP](https://winscp.net/eng/index.php).

### Linux:
    - Zertifikat über [SCP](https://de.wikipedia.org/wiki/Secure_Copy) kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/PATH/TO/wikiCA.crt /lokales/verzeichnis/zielordner
```

- Wenn ein Berechtigungsfehler erscheint (Befehle auf dem Server ausführen):
```
$ ssh USER@IP-Adresse
```
```
$ cd /PATH/TO/wiki-zertifikate
$ ls
```
```
$ sudo chmod 644 /PATH/TO/wiki-zertifikate/wikiCA.crt
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


# 5. [Docker](https://www.docker.com/) 
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


# 6. Docker-Compose Konfiguration erstellen
- Ordner für Wiki.js erstellen
```
$ mkdir wikijs
$ cd wikijs
```

- `docker-compose.yml` - Konfigurationsdatei erstellen
```
$ sudo nano docker-compose.yml
```

- Es gibt eine [Beispielkonfiguration in der offiziellen Dokumentation](https://docs.requarks.io/install/docker).

- Alternativ ist folgt hier eine etwas `optimierte und sicherere Version der Docker-compose Konfiguration`:
    - Es müssen 2 Platzhalter für Datenbank-Passwörter gegen starke Passwörter ersetzt werden `HIER_PLATZHALTER_PASSWORT_ERSETZEN`
```
services:

  db:
    image: postgres:15-alpine
    container_name: wikijs-db
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: HIER_PLATZHALTER_PASSWORT_ERSETZEN
      POSTGRES_USER: wikijs
    logging:
      driver: none
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    container_name: wikijs
    depends_on:
      - db
    init: true
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: HIER_PLATZHALTER_PASSWORT_ERSETZEN
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "127.0.0.1:3000:3000"

volumes:
  db-data:
```
- Mit `STRG + X` speichern & verlassen


### Hinweise:
- Platzhalter Passwörter
    - Es ist wichtig, dass die beiden Platzhalter für die Datenbank-Passwörter durch starke Passwörter ersetzt werden, diese könnten dann z.B. in einem Passwortmanager gespeichert werden.
- Ports/Erreichbarkeit
    - Außerdem ist die Anpassung von `- "127.0.0.1:3000:3000"` ebenfalls sehr wichtig, da somit Wikijs an die Loopback-Adresse (127.0.0.1) gebindet wird und man somit nicht von außen auf das Setup Zugreifen kann, denn Docker umgeht die lokale Linux-Firewall !
    - Dabei muss natürlich ein Reverse Proxy, wie Nginx eingesetzt werden, um HTTPS über Port 443 zu ermöglichen, das ist das sauberste und sicherste Setup !
- Autostart von den Containern
    - Wenn in der `docker-compose.yml` "`restart: unless-stopped`" enthalten ist, dann sollte das in der Regel ausreichen, um den Container automatisch zu starten.
    - Das geht natürlich nur, wenn auch Docker im Autostart ist - `Punkt 5. Autostart von Docker`


-------------------------------------------------------------------------------------------------------------


# 7. Container erstellen & starten

- Container starten
    - Um den Container zu starten, muss man in dem entsprechenden Ordner sein.
```
$ cd wikijs
$ ls
```
```
$ sudo docker compose up -d
```

- Um den Container herunterfahren zu können
```
$ sudo docker compose down
```

- Um den Container neu starten zu können
```
$ docker compose restart
```


-------------------------------------------------------------------------------------------------------------


# 8. Netzwerkeinstellungen
- [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) von [Ubuntu-Server](https://ubuntu.com/download/server) herausfinden
```
$ ip a
```

- Optional mit `ifconfig` (muss aber zusätzlich installiert werden).
```
$ sudo apt install net-tools
$ ifconfig
```


-------------------------------------------------------------------------------------------------------------


# 10. Ersteinrichtung von [Wiki.js](https://js.wiki/)
- An diesem Punkt sollte die Ersteinrichtung im Browser durchgeführt werden, bevor diese Anleitung weiter ausgeführt wird.
- Die Einrichtung ist auch in diesem Ordner unter [Wikijs-konfigurieren-&-einrichten](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/wikijs/Wikijs-konfigurieren-&-einrichten.md) erklärt.


-------------------------------------------------------------------------------------------------------------


# 11. Passwortänderung an der Datenbank und PostgreSQL vornehmen (nur bei Bedarf)
- Container herunterfahren:
```
$ cd wiki
```
```
$ sudo docker compose down
```

- In den PostgreSQL-Container einloggen:
```
$ sudo docker exec -it wikijs-db psql -U wikijs -d wiki
```

- Passwort ändern:
```
$ ALTER USER wikijs WITH PASSWORD 'neues_passwort';
```

- speichern:
```
\q
```

- Nun die `docker-compose.yml` bearbeiten:
```
$ sudo nano docker-compose.yml
```
BEI `DB_PASS` und `POSTGRES_PASSWORD` das Passwort entsprechend ändern.


- Container starten:
```
$ sudo docker compose up -d
```


-------------------------------------------------------------------------------------------------------------


# 13. Logs überprüfen & Container neu starten

- Containername herausfinden:
```
$ docker ps
```

- Logs auf Fehler prüfen:
```
$ docker logs -f wikijs
```

- Container neu starten:
```
$ docker compose restart
```


-------------------------------------------------------------------------------------------------------------

