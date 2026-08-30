# [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für [Zammad](https://zammad.com/de) - über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) (ohne öffentliche Domain)

`Anleitung erstellt am 1.7.2025, zuletzt bearbeitet am 30.8.2026`


# 1. selbstsigniertes Zertifikat
- Wenn man KEINE öffentliche Domain für Zammad nutzt, sondern diesen nur über die IP-Adresse betreibt, dann nutzt man am besten ein `selbst-signiertes Zertifikat`.
- Nachteil dabei ist, dass Browser die HTTPS-Verbindung, auch wenn die Verschlüsselung aktiv ist, als unsicher kennzeichnen, da das Zertifikat selbst signiert ist.
- Man kann das erstellte CA-Zertifikat herunterladen und in die Clients mannuell oder über eine Verwaltungsumgebung importieren.


-------------------------------------------------------------------------------------------------------------


# 2. Ein selbstsigniertes Zertifikat erstellen ([Zammad](https://zammad.com/de) über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) & interne Domain - [DNS](https://de.wikipedia.org/wiki/Domain_Name_System)-Weiterleitung)
```
$ mkdir zammad-zertifikate
$ cd zammad-zertifikate
```
- San Konfig erstellen
```
$ sudo nano san.cnf
```
- einfügen & DNS-Namen, IP-Adresse und Informationen anpassen
	- Die Punkte `stateOrProvinceName, localityName, organizationName` sind `optional` und können vollständig `aus der Konfig gelöscht werden`.
	- Optional kann unter `organizationName` der Name der Firma/Organisation etc. angegeben werden.
	- Die Platzhalter `DNS.1   = zammad.lokal.com` und `IP.1    = IP-ADRESSE` mit entsprechenden Werten anpassen.
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
DNS.1   = zammad.lokal.com
IP.1    = IP-ADRESSE

[ v3_req ]
subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + Zammad-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit

-  ZammadCA erstellen (Client-Zertifikat):
    - Den Platzhalter "MEINEORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `ZammadCA.crt` und `ZammadCA.key` erstellt wurden.
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout ZammadCA.key -out ZammadCA.crt -subj "/C=DE/CN=MEINEORGANISATION-ZammadCA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `ZammadCA.srl, Zammad.crt, Zammad.csr, ZammadCA.key` erstellt wurden.
```
$ openssl req -new -nodes -keyout Zammad.key -out Zammad.csr -config san.cnf
```
```
$ openssl x509 -req -in Zammad.csr -CA ZammadCA.crt -CAkey ZammadCA.key -CAcreateserial -out Zammad.crt -days 3650 -extensions v3_req -extfile san.cnf
```


-------------------------------------------------------------------------------------------------------------


# 3. [Firewall](https://ubuntu.com/server/docs/firewalls) vom [Ubuntu-Server](https://ubuntu.com/download/server) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- HTTPS-Anfragen erlauben und Status der Firewall abrufen
    - HTTPS Anfragen erlauben um Weiterleitung auf HTTPS (HSTS) zu ermöglichen
```
$ sudo ufw allow https
$ sudo ufw allow http
$ sudo ufw enable
$ sudo ufw status
```


-------------------------------------------------------------------------------------------------------------


# 4. [Reverse-Proxy](https://de.wikipedia.org/wiki/Reverse_Proxy) [Nginx](https://nginx.org/)
- Um die HTTPS-Verschlüsselung nutzen zu können, muss man einen Webserver wie Nginx als Reverse Proxy vor Zammad schalten.

- [Nginx](https://nginx.org/) - [Web-Server](https://de.wikipedia.org/wiki/Nginx) installieren
```
$ sudo apt update
$ sudo apt install nginx
```

- [Nginx](https://nginx.org/)-Konfiguration erstellen
```
$ sudo nano /etc/nginx/sites-available/zammad
```

- folgendes einfügen:
    - Platzhalter anpassen:
    - `IP-ADRESSE`: IP-Adresse des Servers und ggf. auch Zammad.example anpassen
    - `/home/USERNAME/zammad-zertifikate/` Pfad ggf. anpassen
```
server {
    listen 80;
    server_name IP-ADRESSE zammad.lokal.com;
    return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name IP-ADRESSE zammad.lokal.com;

  ssl_certificate     /home/USERNAME/zammad-zertifikate/Zammad.crt;
  ssl_certificate_key /home/USERNAME/zammad-zertifikate/Zammad.key;

  proxy_redirect    off;

  location / {
    proxy_set_header Host $host;

    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Port 443;
    proxy_set_header X-Forwarded-Ssl on;

    proxy_pass http://127.0.0.1:8080;
  }
}
```
- Mit `STRG+X` speichern und schließen.



# 5. Aktivieren der Konfiguration, falls noch nicht geschehen
- Das nur, wenn Nginx zuvor noch nicht mit dieser Konfig verwendet wurde.
```
$ sudo ln -s /etc/nginx/sites-available/zammad /etc/nginx/conf.d/zammad.conf
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


# 6. CSRF Token Problem
- Was ist das Problem ?
    - Das Problem kann auftreten, wenn ein Reverseproxy für HTTPS verwendet wird.
    - Wenn das Problem auftritt kann sich kein Benutzer/Admin-Konto mehr bei Zammad einloggen.

- Auf Ubuntu-Server per SSH einloggen
    - In das Zammad-Docker Verzeichnis wechseln
    - eine neue Datei erstellen
```
$ cd zammad-docker-compose
$ ls -a
$ sudo nano .env
```

- nun folgendes einfügen und anpassen:
    - LOKALER-DNS-EINTRAG durch den tatsächlichen verwendeten DNS Eintrag erstetzen
```
ZAMMAD_FQDN=LOKALER-DNS-EINTRAG
ZAMMAD_HTTP_TYPE=http
NGINX_SERVER_SCHEME=https
```

- Überprüfen:
    - Befehl ausführen und nach `ZAMMAD_FQDN=`, `ZAMMAD_HTTP_TYPE=http` etc. suchen
```
$ docker compose config
```

- Überprüfen:
```
$ docker compose config | grep -E 'ZAMMAD_FQDN|ZAMMAD_HTTP_TYPE|NGINX_SERVER_SCHEME'
```
- Die Ausgabe sollte wie folgt aussehen:
```
NGINX_SERVER_SCHEME: https
ZAMMAD_FQDN: LOKALER-DNS-EINTRAG
ZAMMAD_HTTP_TYPE: http
```

- Wenn die Werte korrekt gesetzt sind, dann folgenden Befehl nutzen, um Container neu zu starten:
```
$ docker compose up -d
```


-------------------------------------------------------------------------------------------------------------


# 7. Prüfen, ob Zertifikat gültig und aktiv ist
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


# 8. CA-Zertifikat in Clients importieren


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
$ cd /home/username/Zammad-Zertifikate
$ ls
```
```
$ sudo chmod 644 home/username/Zammad-Zertifikate/ZammadCA.crt
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

