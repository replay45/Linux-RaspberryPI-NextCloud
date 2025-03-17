# [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für Wikijs - über IP-Adresse (ohne öffentliche Domain)

`Anleitung erstellt am 7.3.2025`


# 1. Einschränkungen (bzw. Nachteile)
- Wenn man KEINE öffentliche Domain für Wikijs nutzt, sondern diesen nur über die IP-Adresse betreibt, dann nutzt man am besten ein `selbst-signiertes Zertifikat`.
- Nachteil dabei ist, dass Browser die HTTPS-Verbindung, auch wenn die Verschlüsselung aktiv ist, als unsicher kennzeichnen, da das Zertifikat selbst signiert ist.
- Hier kann man allerdings auch mit einem Kompromiss etwas nachhelfen, indem man das entsprechende Zertifikat in den betroffenen Browsern als vertrauenswürdig einstuft.


# 2. Wichtige Informationen zum Zertifikat für HTTPS
- Um eine HTTPS-Verschlüsselung nutzen zu können, benötigt man ein SSL-Zertifikat.
- Da das `Zertifikat "selbst signiert" ist` wird es `nicht als vertrauenswürdig eingestuft`. Das ist allerdings `KEIN Problem`, da die `HTTPS-Verbindung trotzdem gegeben` ist.


# 3.1. Ein selbstsigniertes Zertifikat erstellen (Wikijs nur IP-Adresse)
- Ordner erstellen
```
$ mkdir wiki-zertifikate
$ cd wiki-zertifikate
```
- Zertifikat erstellen
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout wiki.key -out wiki.crt
```
`x509` Erzeugt ein selbst signiertes Zertifikat.
`-nodes` Speichert den privaten Schlüssel unverschlüsselt.
`-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre).
`-newkey rsa:2048` Erstellt einen neuen RSA-Schlüssel mit 2048 Bit.

- Nun werden noch einige Informationen abgerufen, die meisten können übersprungen werden.
- `WICHTIG` ist der `Common Name`, denn hier muss unbedingt die IP-Adresse des Wikijs-Servers angegeben werden.



# 3.2. Ein selbstsigniertes Zertifikat erstellen (Wikijs über IP-Adresse & interne Domain - DNS-Weiterleitung)
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
```
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
x509_extensions    = v3_req
prompt             = no

[ req_distinguished_name ]
countryName            = DE
stateOrProvinceName    = DeinBundesland
localityName           = DeineStadt
organizationName       = DeineFirma
commonName             = wiki.lokal.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1   = wiki.lokal.com
IP.1    = 192.168.1.10

[ v3_req ]
subjectAltName = @alt_names
```

- Zertifikat erstellen
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout wiki.key -out wiki.crt -config san.cnf
```
`x509` Erzeugt ein selbst signiertes Zertifikat.
`-nodes` Speichert den privaten Schlüssel unverschlüsselt.
`-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre).
`-newkey rsa:2048` Erstellt einen neuen RSA-Schlüssel mit 2048 Bit.


# 4. Zertifikat in [Nginx](https://nginx.org/) Webserver einfügen
- Um die HTTPS-Verschlüsselung nutzen zu können, muss man einen Webserver wie Nginx als Reverse Proxy vor das Wiki schalten.

- [Nginx](https://nginx.org/) - [Web-Server](https://de.wikipedia.org/wiki/Nginx) installieren
```
$ sudo apt install nginx
```

- [Nginx](https://nginx.org/)-Konfiguration erstellen
```
$ sudo nano /etc/nginx/sites-available/wiki
```

- Folgendes in die Konfiguration einfügen/ ersetzen:
```
server {
    listen 443 ssl;
    server_name wiki.lokal.com;

    ssl_certificate     /home/username/wiki-zertifikate/wiki.crt;
    ssl_certificate_key /home/username/wiki-zertifikate/wiki.key;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

- Wenn HTTP-Anfragen auf HTTPS Weitergeleitet werden sollen, folgendes ergänzen:
```
server {
    listen 80;
    server_name wiki.local.com 192.168.1.10;
    return 301 https://$host$request_uri;
}
```
- `wiki.lokal.com` und die `IP-Adresse` müssen ggf. in der Konfigurationsdatei mit der `eigenen Domain und IP ersetzt` werden.
- Und auch die Pfade zu dem Zertifikat müssen ggf. angepasst werden.
- Mit `STRG+X` speichern und schließen.


# 5. [Firewall](https://ubuntu.com/server/docs/firewalls) vom [Ubuntu-Server](https://ubuntu.com/download/server) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- HTTPS-Anfragen erlauben und Status der Firewall abrufen
```
$ sudo ufw allow https
$ sudo ufw enable
$ sudo ufw status
```


# 6. Aktivieren der Konfiguration, falls noch nicht gesehen
- Das nur, wenn Nginx zuvor noch nicht verwendet wurde.
```
$ sudo ln -s /etc/nginx/sites-available/wiki /etc/nginx/sites-enabled/
```

- Konfig testen
	 - Wenn die Meldung `syntax is ok` erscheint, ist alles in Ordnung.
```
$ sudo nginx -t
```

- [Nginx](https://nginx.org/) neu starten
```
$ sudo systemctl restart nginx
```



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


# 8. Zertifikat als vertrauenswürdig einstufen, durch `rootCA`

- Hier wird ein `rootCA`-Zertifikat ergänzt, welches dazu dient, das bereits erstellte Zertifikat, welches auf dem Server ist, zu verifizieren.
- Dafür das rootCA-Zertifikat erstellen und danach in einen Browser importieren, damit dieser das Zertifikat, welches auf dem Server ist, als vertrauenswürdig einstuft.
- In diesem Schritt müssen bis auf die Erstellung und Signierung des Zertifikates, `KEINE Änderungen am Server` vorgenommen werden.
- Der Name `rootCA` kann natürlich `beliebig angepasst` werden, jedoch sollte beim Signieren beachtet werden, mit den korrekten Dateinamen zu arbeiten.


### rootCA erstellen & Serverzertifikat mit rootCA signieren 
1. rootCA erstellen
- `MeineFirma-CA` kann optional mit beliebigen Namen, wie dem Firmennamen angepasst werden.
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout rootCA.key -out rootCA.crt -subj "/C=DE/CN=MeineFirma-CA"
```


2. CSR (Certificate Signing Request) - selbsterstelltes Server-Zertifikat mit Root-CA signieren
	- wiki.key → Privater Schlüssel (bleibt auf dem Server)
	- wiki.csr → CSR-Datei, die von der CA signiert wird
	- rootCA.crt -> sollte in Browser importiert werden

- Befehl nur für Domain
```
$ openssl req -new -nodes -keyout wiki.key -out wiki.csr -subj "/C=DE/CN=wiki.local.com"
```

- Befehl für interne Domain und IP-Adresse (vorhandene san.cnf aus Punkt 3.2. wird vorausgesetzt.)
```
$ openssl req -new -nodes -keyout wiki.key -out wiki.csr -config san.cnf
```
```
$ openssl x509 -req -in wiki.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out wiki.crt -days 3650 -extensions v3_req -extfile san.cnf
```

3. Prüfen, ob das Zertifikat die Domain & IP enthält
```
$ openssl x509 -in wiki.crt -noout -text | grep -A1 "Subject Alternative Name"
```


4. selbsterstelltes Server-Zertifikat in Nginx einbinden (falls noch nicht geschen)
	- Wie die genaue Konfig aussehen muss, `ist in Punkt 4. beschrieben.`
```
$ sudo nano /etc/nginx/sites-available/wiki
```
- Konfig falls nötig anpssen
- Wenn Änderungen vorgenommen worden sind, dann:
```
$ sudo systemctl restart nginx
```


### Zertifikat von Server mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) herunterladen
- Zertifikat über SCP kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/home/username/wiki-zertifikate/wiki.crt /dein/lokales/verzeichnis/zielordner
```

- Wenn ein Berechtigungsfehler erscheint (Befehle auf dem Server ausführen):
```
$ ssh user@IP-Adresse
```
```
$ cd /home/username/wiki-zertifikate
$ ls
```
```
$ sudo chmod 644 home/username/wiki-zertifikate/wiki.crt
```
- Nun nochmal mit SCP versuchen, die Datei zu kopieren.


## rootCA Zertifikat in Browser als vertrauenswürdig einstufen
- Die `Chrome basierten Browser` nutzen den `Zertifikatsspeicher des Betriebsystems`.
- Der `Firefox` hingegen nutzt nur den `eigenen Zertifikatsspeicher`.

### Zertifikat manuell in Firefox als vertrauenswürdig einstufen
- Firefox öffnen
- `Einstellungen`
- `Sicherheit und Datenschutz`
- zum Punkt `Zertifikate` und auf `Zertifikate anzeigen`
- `Zertifizierungsstellen`
- `Importieren`
- Setzen des Häkchens bei `Dieser CA vertrauen, um Websites zu identifizieren`
- `OK`
- Firefox neu starten.


## Wenn viele Clients genutzt werden, wie in einem Unternehmen, dann mit GPO-Richtlinie Zertifikat an Clients automatisch verteilen
- Wenn eine Active Directory (Domäne) genutzt wird, kann eine Gruppenrichtlinie (GPO) erstellt werden, um das rootCA-Zertifikat automatisch an alle Clients zu verteilen.
- Dabei muss ggf. das Zertifikat in den Zertifikatsspeicher des Betriebssystems für die Chrome-basierten Browser und ggf. separat in den Firefox-Zertifikatsspeicher verteilt werden, wobei es auch möglich ist, Firefox über die GPO so zu konfigurieren, dass dieser den Zertifikatsspeicher des Betriebsystems nutzt.


## Zetifikatsignierung mit rootCA-Zertifikat von anderen Diensten
- Der Vorteil des rootCA-Zertifikates ist, dass man auch andere Zertifikaten von anderen internen Diensten signieren kann und man somit nur das eine rootCA-Zertifikat benötigt.
	- Dafür muss man das `rootCA-Zertifikat`, die `rootCA.key-Datei` und `rootCA.srl-Datei` auf den entsprechenden Server laden, ein selbstsigniertes-Zertifikat erstellen, in die Konfig des Webservers laden und das rootCA-Zertifikat verwenden, um das selbstsigniertes-Zertifikat auf dem Server zu signieren.


-------------------------------------------------------------------------------------------------------------


### Zugriff auf das Wiki - HTTP / HTTPS
- Wenn alles korrekt konfiguriert wurde, sollte das Wiki folgendermaßen erreichbar sein:
	- Zu administrativen Zwecken über HTTP: http://IP-Adresse:3000
	- Über die IP-Adresse: HTTP-Anfragen sollten automatisch auf HTTPS umgeleitet werden.
	- GGf. über den internen DNS-Eintrag mit Umleitung auf HTTPS

- Das heißt, sofern nicht direkt der Port 3000 aufgerufen wird, sollten alle HTTP-Anfragen (über Port 80) auf HTTPS, also Port 443, umgeleitet werden (egal, ob über die IP-Adresse oder den DNS-Eintrag).

-------------------------------------------------------------------------------------------------------------

