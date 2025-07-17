# [Zammad](https://zammad.com/de) über [Docker](https://www.docker.com/) auf einem [Ubuntu-Server](https://ubuntu.com/download/server) installieren



# 1. [Ubuntu-Server](https://ubuntu.com/download/server) installieren (inkl. [Docker](https://www.docker.com/))
- Ubuntu-Server installieren.
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


# 5. [Docker](https://www.docker.com/) manuell oder über [Snap](https://snapcraft.io/) installieren

### [Docker](https://www.docker.com/) manuell installieren

- Notwendige Pakete installieren:
```
$ sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

- Docker GPG-Schlüssel hinzufügen und überprüfen
	- Wenn es ein Download-Problem geben sollte, zu `Punkt 16. DNS-resolve Problem lösen` scrollen. 
```
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ sudo apt update
```

- Docker Repository hinzufügen für Ubuntu 22.04
```
$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

-  Docker installieren
```
$ sudo apt update
$ sudo apt install docker-ce docker-ce-cli containerd.io
```

- Docker-Version prüfen
```
$ docker --version
```

- Installation von Docker Compose
```
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
```


### [Docker](https://www.docker.com/) über Snap installieren
- Docker über den Paketmanager [Snap](https://snapcraft.io/) installieren
```
$ sudo snap install docker
```

- Version prüfen
```
$ docker --version
$ docker-compose --version
```


# 6. Autostart von [Docker](https://www.docker.com/)
- Docker manuell installiert
```
$ sudo systemctl start docker
$ sudo systemctl enable docker
$ sudo systemctl status docker
```

- Docker über [Snap](https://snapcraft.io/) installiert
```
$ sudo snap services docker
$ sudo snap enable docker
```


-------------------------------------------------------------------------------------------------------------


# 7. Docker-Container erstellen

- Github-Repository klonen, um Konfig zu erhalten
	- Beim Ausführen des Befehls wird der Ordner `zammad-docker-compose` erstellt.
```
$ git clone https://github.com/zammad/zammad-docker-compose.git
```

- Die `docker-compose.yml` - Konfigurationsdatei kann bei Bedarf natürlich noch manuell angepasst werden.
- Außerdem werden ggf. beim klonen des Repositorys, standard-Passwörter in der Konfig verwendet, die `vor dem ersten Start` geändert werden sollten.
- Wenn die Datenbanken bereits in verwendung sind und die Passwörter geändert werden sollen, dann zu `Punkt 14.` scrollen.
- Hinweis: die `rancher-compose.yml` kann erstmal ignorierd werden.

```
$ cd zammad-docker-compose
$ sudo nano docker-compose.yml
```


- Passwörter vor dem ersten Start in der Konfig docker-compose.yml ändern !
	- Hinweis: Das `-` (Bindestrich) gehört zur Syntax, einfach nicht beachten und an ort und Stelle behalten !
```
$ sudo nano docker-compose.yml
```
- `STRG+W` zum Suchen drücken
- nach `password` und/oder `PASS` suchen
- Es müssten `etwa 4 Stellen` sein, an denen das Passwort angepasst werden sollte (2 Datenbank Passwörter und 2 elacstic Search Passwöter) !
- Außerdem sollten es `4 verschieden Passwörter sein`.
- Die Konfiguration kann z.B. in einem Passwortmanager abgespeichert werden.
- Mit `STRG+X` speichern


- "Version" (Zeile) in Konfig entfernen (Optional)
	- Die Zeile mit der Version, z.B. `Version 3.8` kann zu Warnmeldungen führen, stellt aber kein Problem dar.
	- Um die Warnmeldungen nicht mehr zu erhalten, einfach die Zeile mit der Version entfernen.


-------------------------------------------------------------------------------------------------------------


# 8. Container starten

- Container starten
    - Um den Container zu starten, muss man in dem entsprechenden Ordner sein.
```
$ cd zammad-docker-compose
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

### Wenn ein DNS-Error beim Starten des Containers erscheint, zu `Punkt 17. Wenn ein DNS-Error beim Starten des Containers erscheint` scrollen.


-------------------------------------------------------------------------------------------------------------


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


-------------------------------------------------------------------------------------------------------------


### 10. [Firewall](https://de.wikipedia.org/wiki/Firewall) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- Status
```
$ sudo ufw status
```

- Port 8080 erlauben (für zammad über Docker)
```
$ sudo ufw allow 8080
```

- Firewall ggf. aktivieren
```
$ sudo ufw enable
```


-------------------------------------------------------------------------------------------------------------


# 11. Ersteinrichtung von [Zammad](https://zammad.com/de)
- An diesem Punkt sollte die Ersteinrichtung im Browser durchgeführt werden, bevor diese Anleitung weiter ausgeführt wird.
- Die Einrichtung ist auch in diesem Ordner unter [zammad-konfigurieren-&-einrichten]() erklärt.


-------------------------------------------------------------------------------------------------------------


# 12. [Zammad](https://zammad.com/de) (Container) in Autostart

- Wenn in der `docker-compose.yml` "`restart: ${RESTART:-always}`" enthalten ist, dann sollte das in der Regel ausreichen, um den Container automatisch beim Systemstart zu starten.
- Das geht natürlich nur, wenn auch Docker im Autostart ist - `Punkt 6. Autostart von Docker`
- Sollte das trozdem nicht ausreichen, müssen evtl. manuell noch Konfigurationen vorgenommen werden.


-------------------------------------------------------------------------------------------------------------


# 13. Änderungen an der Konfig-Datei vornehmen (nur bei Bedarf)
- Container herunterfahren:
```
$ cd zammad-docker-compose
```
```
$ sudo docker-compose down
```
- Änderungen vornehmen und mit der `docker-compose.yml` über dem Befehl `$ sudo nano docker-compose.yml` abstimmen.
- Container starten:
```
$ sudo docker-compose up -d
```

-------------------------------------------------------------------------------------------------------------


# 14. Passwortänderung an der Datenbank und PostgreSQL vornehmen (nur bei Bedarf) - Datenbanken die bereits in Verwendung sind
- Container herunterfahren:
```
$ cd zammad-docker-compose
```
```
$ sudo docker-compose down
```

- In den PostgreSQL-Container einloggen:
	- Der Containtername kann abweichen, diesen ggf. anpassen. Containername prüfen: `$ sudo docker ps`
```
$ sudo docker exec -it zammad-postgresql-1 psql -U zammad -d zammad_production
```

- Passwort ändern:
```
$ ALTER USER zammad WITH PASSWORD 'neues_passwort';
```

- speichern:
```
\q
```

- Nun die `docker-compose.yml` bearbeiten:
	- BEI `DB_PASS` und `POSTGRES_PASSWORD` das Passwort entsprechend ändern.
```
$ sudo nano docker-compose.yml
```

- Container starten:
```
$ sudo docker-compose up -d
```


-------------------------------------------------------------------------------------------------------------


# 15. Logs überprüfen & Container neu starten

- Containername herausfinden:
```
$ docker ps
```

- Logs auf Fehler prüfen:
```
$ docker logs -f zammad-docker-compose_NAME_DES_CONTAINERS
```

- Container neu starten:
```
$ docker-compose restart
```


-------------------------------------------------------------------------------------------------------------


# 16. DNS-resolve Problem lösen

- Wenn das Auflösen der download.docker.com Domain gibt, DNS-Server ändern:
```
$ ping -c 4 download.docker.com
```
```
$ sudo nano /etc/resolv.conf
```
- Den Namn server zu 1.1.1.1 ändern:
`nameserver 1.1.1.1`

```
$ ping -c 4 download.docker.com
```

- Wenn das noch nicht hilft:
```
$ sudo systemctl restart systemd-resolved
```
```
$ ping -c 4 download.docker.com
```

## Nach dem erfolgreichen Zugriff auf die Domain, empfiehlt es sich die Einstellungen rückgängig zu machen, da es ansonsten zu Problemen bei starten des Containers kommen könnte - mehr dazu unter Punkt 17!


-------------------------------------------------------------------------------------------------------------


# 17. Wenn ein DNS-Error beim Starten des Containers erscheint

Zunächst die Änderungen aus "Punkt 16" rückgängig machen:

- Löschen der manuellen Datei (falls sie überschrieben wurde):
```
$ sudo rm /etc/resolv.conf
```

- symbolischen Link zurücksetzen:
```
$ sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
```

- überprüfen:
```
$ ls -l /etc/resolv.conf
```
- Ausgabe sollte etwa so aussehen: `lrwxrwxrwx 1 root root Datum Uhrzeit /etc/resolv.conf -> /run/systemd/resolve/resolv.conf`



- Systemd-resolved mit festen DNS-Servern konfigurieren:
```
$ sudo nano /etc/systemd/resolved.conf
```
- Zeile `[Resolve]` ändern
```
DNS=1.1.1.1
FallbackDNS=1.0.0.1
```
Speichern mit `STRG+X`


- Dienst neu starten:
```
$ sudo systemctl restart systemd-resolved
```

- Änderungen überprüfen:
```
$ resolvectl status
```

- Container starten:
```
$ sudo docker-compose up -d
```


-------------------------------------------------------------------------------------------------------------
