# [Zammad](https://zammad.com/de) über [Docker](https://www.docker.com/) auf einem [Ubuntu-Server](https://ubuntu.com/download/server) installieren

`Anleitung zuletzt bearbeitet am 24.8.2026`

- Link zur [Zammad System Dokumentation](https://docs.zammad.org/en/latest/index.html)

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


# 6. Docker-Konfiguration herunterladen - [docs.zammad.org - docker-compose](https://docs.zammad.org/en/latest/install/docker-compose.html)
- Github-Repository klonen, um Konfig zu erhalten
	- Beim Ausführen des Befehls wird der Ordner `zammad-docker-compose` erstellt.
```
$ git clone https://github.com/zammad/zammad-docker-compose.git
```


-------------------------------------------------------------------------------------------------------------


# 7. Container starten
- Container starten
    - Um den Container zu starten, muss man in dem entsprechenden Ordner sein.
```
$ cd zammad-docker-compose
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


### Docker Port mapping anpassen, da trotz Firewall Docker Port exponiert ist !
- Standardmäßig ist der Docker-Port 8080 aufrufbar, auch wenn in der Firewall vom Ubuntu-Server keine Freigabe eingestellt wirde.
- Das leigt daran, dass Docker selbst die Firewall-Regeln verwaltet.
    - problematisch: `0.0.0.0:8080:8080` > kritische Konfiguration !
    - richtig: `127.0.0.1:8080:8080`

- Wenn bei `$ sudo ufw status` keine Regel für Port 8080 erscheint, aber im Browser http://IP-Adresse:8080 aufrufbar ist (Sofern Zammad online ist), dann ist die Konfiguration kritisch und NICHT empfehlenswert.

- Da die docker-compose.yml vom Github-Repository heruntergeladen und bei Updates geändert werden kann, bitte KEINE Änderungen an der Datei vornehmen !
- Der vorgesehene Weg ist Werte über die .env-Datei bereitzustellen !

#### Docker-Port-8080 nur für localhost erreichbar machen
- .env anlegen/bearbeiten:
```
$ cd zammad-docker-compose
$ ls -a
$ sudo nano .env
```
- Enfügen:
```
NGINX_EXPOSE_PORT=127.0.0.1:8080
```

- Container neu starten:
```
$ sudo docker compose up -d
```

- Container überprüfen:
    - Ausgabe sollte nun 127.0.0.1:8080->8080 enthalten.
```
$ sudo docker ps
```

- Nun sollte auch Port 8080 nicht mehr erreichbar sein und der Zugriff nur noch über den Reverse-Proxy laufen !


-------------------------------------------------------------------------------------------------------------


### 9. [Firewall](https://de.wikipedia.org/wiki/Firewall) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- Status
```
$ sudo ufw status
```

- Port 8080 erlauben
```
$ sudo ufw allow 8080
```

- Firewall ggf. aktivieren
```
$ sudo ufw enable
```

- Sofern ein Reverseproxy genutzt wird/werden soll, um HTTPS zu verwenden kann der Port 8080 geschlossen werden.
- Wenn benötigt kann dieser jederzeit erneut geöffnet werden.


-------------------------------------------------------------------------------------------------------------


# 10. Ersteinrichtung von [Zammad](https://zammad.com/de)
- An diesem Punkt sollte die Ersteinrichtung im Browser durchgeführt werden, bevor diese Anleitung weiter ausgeführt wird.
- Die Einrichtung ist auch in diesem Ordner unter [zammad-konfigurieren-&-einrichten](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/zammad/Zammad-konfigurieren-&-einrichten.md) erklärt.


-------------------------------------------------------------------------------------------------------------


# 11. [Zammad](https://zammad.com/de) Container-Autostart
- In der `docker-compose.yml` sollte "`restart: ${RESTART:-always}`" enthalten sein, wenn das der Fall ist, dann sollten die Container starten, wenn das Betriebssytem gestartet wird, sofern auch Docker im Autostart ist.


-------------------------------------------------------------------------------------------------------------


# 12. Logs überprüfen & Container neu starten
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
$ docker compose restart
```


-------------------------------------------------------------------------------------------------------------


# 13. Updates

### Für Updates vom Betriebssystem, folgende Befehle ausführen:
- manuelle Betriebssystem Updates:
```
$ sudo apt update
$ sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt autoremove && sudo apt autoclean
```

- Um Betriebssystem Updates zu automatisieren & einen Linux-Server abzusichern, der verlinkten Anleitung folgen:
    - [Linux-Server absichern](https://github.com/replay45/Linux-RaspberryPI-NextCloud/blob/main/linux/Linux-Server/Linux-Server-absichern.md)


### [Zammad aktualisieren](https://docs.zammad.org/en/latest/install/update.html)
- Hinweise
    - Die [offizielle Dokumentation](https://docs.zammad.org/en/latest/install/update.html) empfiehlt vor dem Updaten die Versionshinweise zu prüfen.
    - Außerdem ist es wichtig, dass beim Aktualisieren `KEINE Versionen übersprungen werden`, sondern zunächst das Update auf die nächste stable-Version, vorgenommen wird, bevor auf die aktuellste Version installiert wird.
    - Vor der Durchführung der Aktualisierung kann der Wartungs-Modus in Zammad genutzt werden.

- Leider ist der Update-Prozess bei Zammad über Docker relativ kompliziert, daher muss abgewägt werden, ob und wie oft Updates vorgenommen werden.
- Dabei muss auch berücksichtigt werden, dass `öffentlich erreichbare Instanzen auf jeden Fall geupdatet werden müssen`, bei Instanzen, die nur lokal erreichbar sind, kann dies ggf. auch abgewogen werden, ob Updates nötig sind.
- Außerdem ist der Update-Prozess für Zammad mit Docker in den Dokumentation nicht besonders gut beschrieben (Stand August 2026).
- Allerdings sollte man beachten, dass wenn Updates für eine lange Zeit ausgelassen werden, alte Zammad-Instantzen Sicherheits-, Stabilitäts und Abhängigkeitsprobleme verursachen kann !


-------------------------------------------------------------------------------------------------------------
