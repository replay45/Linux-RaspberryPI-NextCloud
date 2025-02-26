# [Wiki.js](https://js.wiki/) über [Docker](https://www.docker.com/) auf einem [Ubuntu-Server](https://ubuntu.com/download/server) installieren


# 1. [Ubuntu-Server](https://ubuntu.com/download/server) installieren (inkl. [Docker](https://www.docker.com/))
- Ubuntu-Server installieren.
- Bei der Abfrage für weitere Software Docker auswählen, damit Docker über [Snap](https://snapcraft.io/) installiert wird.
- Installation beenden.
- Auf Ubuntu-Server einloggen / [SSH](https://de.wikipedia.org/wiki/Secure_Shell)-Verbindung herstellen.
- `$ ssh user@IP-Adresse`


# 2. Updates
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
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


# 4. [Docker](https://www.docker.com/) Version prüfen
```
$ docker --version
$ docker-compose --version
```
- Wenn die Ausgabe die Versionsnummern sind, dann ist Docker installiert.


# 5. [Docker](https://www.docker.com/) ggf. manuell über Snap installieren
- Docker über den Paketmanager [Snap](https://snapcraft.io/) installieren
```
$ sudo snap install docker
```

- Version prüfen
```
$ docker --version
$ docker-compose --version
```

- Natürlich kann Docker auch optional z.B. über [curl](https://curl.se/) installiert werden.



# 6. Autostart von [Docker](https://www.docker.com/)
- Docker über [Snap](https://snapcraft.io/) installiert
```
$ sudo snap services docker
$ sudo snap enable docker
```

- Docker über [curl](https://curl.se/) installiert
```
$ sudo systemctl status docker
$ sudo systemctl enable docker
```


-------------------------------------------------------------------------------------------------------------


# 7. Docker-Container erstellen
- Ordner für [Wiki.js](https://js.wiki/) installieren
```
$ mkdir wikijs
$ cd wikijs
```

- `docker-compose.yml` - Konfigurationsdatei erstellen
```
$ nano docker-compose.yml
```

- Folgendes in die `docker-compose.yml`-Datei einfügen:
```
version: "3.8"

services:
  wikijs:
    image: requarks/wiki:2
    container_name: wikijs
    ports:
      - "3000:3000"
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: HIER_DEIN_PASSWORT
      DB_NAME: wiki
    depends_on:
      - db

  db:
    image: postgres:13
    container_name: wikijs-db
    environment:
      POSTGRES_USER: wikijs
      POSTGRES_PASSWORD: HIER_DEIN_PASSWORT
      POSTGRES_DB: wiki
    volumes:
      - wikijs-data:/var/lib/postgresql/data

volumes:
  wikijs-data:
```
- Mit `STRG + O` speichern
- Mit `STRG + X` verlassen


# 8. Container starten
- Container starten
    - Um den Container zu starten, muss man in dem entsprechenden Ordner sein.
```
$ cd wiki
$ ls
```
```
$ sudo docker-compose up -d
```

- Um den Container herunterfahren zu können
```
$ sudo docker-compose down
```

- Um den Container neu starten zu können
```
$ docker-compose restart
```


# 9. Netzwerkeinstellungen

- [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) von [Ubuntu-Server](https://ubuntu.com/download/server) herausfinden
```
$ ip a
```

- Optional mit `ifconfig` (muss aber zusätzlich installiert werden).
```
$ sudo apt install net-tools
$ ifconfig
```


### 10. [Firewall](https://de.wikipedia.org/wiki/Firewall) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- Status
```
$ sudo ufw status
```

- Port 3000 erlauben (für das Wiki über Docker)
```
$ sudo ufw allow 3000
```

- Firewall ggf. aktivieren
```
$ sudo ufw enable
```


-------------------------------------------------------------------------------------------------------------


# 11. Ersteinrichtung von [Wiki.js](https://js.wiki/)
- An diesem Punkt sollte die Ersteinrichtung im Browser durchgeführt werden, bevor diese Anleitung weiter ausgeführt wird.
- Die Einrichtung ist auch in diesem Ordner unter [Wikijs-konfigurieren-&-einrichten](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/wikijs) erklärt.


-------------------------------------------------------------------------------------------------------------


# 12. [Wiki.js](https://js.wiki/) in Autostart
- Wiki.js autostarten (mit Docker-compose)
```
$ nano docker-compose.yml
```

- Hier nun die folgende Zeile zwischen `container_name: wikijs` und `ports:`
```
$ restart: always
```

- Systemd-Dienstdatei erstellen:
```
$ sudo nano /etc/systemd/system/docker-compose-wikijs.service
```

- Folgendes einfügen (Docker über Snap):
```
[Unit]
Description=Docker Compose Wiki.js Service
Requires=snap.docker.dockerd.service
After=snap.docker.dockerd.service

[Service]
WorkingDirectory=/pfad/zu/deinem/docker-compose-verzeichnis
ExecStart=/usr/bin/docker-compose up -d
ExecStop=/usr/bin/docker-compose down
Restart=always
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```
- Die Zeile `/pfad/zu/deinem/docker-compose-verzeichnis` mit dem `Pfad`, zur `docker-compose.yml` ersetzen.


- Systemd-Dienst aktivieren und starten:
```
$ sudo systemctl daemon-reload
$ sudo systemctl enable docker-compose-wikijs
$ sudo systemctl start docker-compose-wikijs
```

- [Ubuntu-Server](https://ubuntu.com/download/server) neu starten & Docker-Container überprüfen
```
$ sudo reboot
$ docker-compose ps
```


-------------------------------------------------------------------------------------------------------------


# 13. Änderungen an der Konfig-Datei vornehmen (nur bei Bedarf)
- Container herunterfahren
```
$ cd wiki
```
```
$ sudo docker-compose down
```
- Änderungen vornehmen und mit der `docker-compose.yml` über dem Befehl `$ nano docker-compose.yml` abstimmen.
```
$ sudo docker-compose up -d
```


# 14. Passwortänderung an der Datenbank und PostgreSQL vornehmen (nur bei Bedarf)
- Container herunterfahren
```
$ cd wiki
```
```
$ sudo docker-compose down
```

- In den PostgreSQL-Container einloggen:
```
$ sudo docker exec -it wikijs-db psql -U wikijs -d wiki
```

- Passwort ändern
```
$ ALTER USER wikijs WITH PASSWORD 'neues_passwort';
```

- speichern
```
\q
```

- Nun die `docker-compose.yml` bearbeiten
```
$ nano docker-compose.yml
```
BEI `DB_PASS` und `POSTGRES_PASSWORD` das Passwort entsprechend ändern.


- Container starten:
```
$ sudo docker-compose up -d
```


-------------------------------------------------------------------------------------------------------------
