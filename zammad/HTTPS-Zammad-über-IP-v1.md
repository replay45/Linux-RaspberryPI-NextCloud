# [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) für [Zammad](https://zammad.com/de) - über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) (ohne öffentliche Domain)

`Anleitung erstellt am 1.7.2025`


# 1. Einschränkungen (bzw. Nachteile)
- Wenn man KEINE öffentliche Domain für Zammad nutzt, sondern diesen nur über die IP-Adresse betreibt, dann nutzt man am besten ein `selbst-signiertes Zertifikat`.
- Nachteil dabei ist, dass Browser die HTTPS-Verbindung, auch wenn die Verschlüsselung aktiv ist, als unsicher kennzeichnen, da das Zertifikat selbst signiert ist.
- Hier kann man allerdings auch mit einem Kompromiss etwas nachhelfen, indem man das entsprechende Zertifikat in den betroffenen Browsern als vertrauenswürdig einstuft.


# 2. Wichtige Informationen zum Zertifikat für HTTPS
- Um eine HTTPS-Verschlüsselung nutzen zu können, benötigt man ein SSL-Zertifikat.
- Da das `Zertifikat "selbst signiert" ist` wird es `nicht als vertrauenswürdig eingestuft`. Das ist allerdings `KEIN Problem`, da die `HTTPS-Verbindung trotzdem gegeben` ist.


# 3.1. Ein selbstsigniertes Zertifikat erstellen ([Zammad](https://zammad.com/de) nur über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse)
- Ordner erstellen
```
$ mkdir zammad-zertifikate
$ cd zammad-zertifikate
```
- Zertifikat erstellen
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout zammad.key -out zammad.crt
```
`x509` Erzeugt ein selbst signiertes Zertifikat.
`-nodes` Speichert den privaten Schlüssel unverschlüsselt.
`-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre).
`-newkey rsa:2048` Erstellt einen neuen RSA-Schlüssel mit 2048 Bit.

- Nun werden noch einige Informationen abgerufen, die meisten können übersprungen werden.
- `WICHTIG` ist der `Common Name`, denn hier muss unbedingt die IP-Adresse des Zammad-Servers angegeben werden.


### oder


# 3.2. Ein selbstsigniertes Zertifikat erstellen ([Zammad](https://zammad.com/de) über [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) & interne Domain - [DNS](https://de.wikipedia.org/wiki/Domain_Name_System)-Weiterleitung)
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
	- Die Platzhalter `DNS.1   = zammad.lokal.com` und `IP.1    = 192.168.1.10` mit entsprechenden Werten anpassen.
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
commonName             = zammad.local.com

[ req_ext ]
subjectAltName = @alt_names

[ alt_names ]
DNS.1   = zammad.lokal.com
IP.1    = 192.168.1.10

[ v3_req ]
subjectAltName = @alt_names
```

- Zertifikat erstellen
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout zammad.key -out zammad.crt -config san.cnf
```
`x509` Erzeugt ein selbst signiertes Zertifikat.
`-nodes` Speichert den privaten Schlüssel unverschlüsselt.
`-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre).
`-newkey rsa:2048` Erstellt einen neuen RSA-Schlüssel mit 2048 Bit.


# 4. [Firewall](https://ubuntu.com/server/docs/firewalls) vom [Ubuntu-Server](https://ubuntu.com/download/server) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- HTTPS-Anfragen erlauben und Status der Firewall abrufen
```
$ sudo ufw allow https
$ sudo ufw enable
$ sudo ufw status
```


# 5. Zertifikat in [Nginx](https://nginx.org/) Webserver einfügen
- Um die HTTPS-Verschlüsselung nutzen zu können, muss man einen Webserver wie Nginx als Reverse Proxy vor Zammad schalten.

- [Nginx](https://nginx.org/) - [Web-Server](https://de.wikipedia.org/wiki/Nginx) installieren
```
$ sudo apt install nginx
```

- [Nginx](https://nginx.org/)-Konfiguration erstellen
```
$ sudo nano /etc/nginx/sites-available/zammad
```

- Folgendes in die Konfiguration einfügen/ ersetzen:
```
server {
    listen 443 ssl;
    server_name zammad.local.com;

    ssl_certificate     /home/username/zammad-zertifikate/zammad.crt;
    ssl_certificate_key /home/username/zammad-zertifikate/zammad.key;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

- Wenn HTTP-Anfragen auf HTTPS weitergeleitet werden sollen, folgendes ergänzen:
```
server {
    listen 80;
    server_name zammad.local.com 192.168.1.10;
    return 301 https://$host$request_uri;
}
```
- `zammad.local.com` und die `IP-Adresse` müssen ggf. in der Konfigurationsdatei mit der `eigenen Domain und IP ersetzt` werden.
- Und auch die Pfade zu dem Zertifikat müssen ggf. angepasst werden.
- Mit `STRG+X` speichern und schließen.


# 6. Aktivieren der Konfiguration, falls noch nicht geschehen
- Das nur, wenn Nginx zuvor noch nicht verwendet wurde.
```
$ sudo ln -s /etc/nginx/sites-available/zammad /etc/nginx/sites-enabled/
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
	- zammad.key → Privater Schlüssel (bleibt auf dem Server)
	- zammad.csr → CSR-Datei, die von der CA signiert wird
	- rootCA.crt -> sollte in Browser importiert werden (Dateiname kann natürtlich noch angepasst werden)

- Befehl nur für Domain
```
$ openssl req -new -nodes -keyout zammad.key -out zammad.csr -subj "/C=DE/CN=zammad.local.com"
```

- Befehl für interne Domain und IP-Adresse (vorhandene san.cnf aus Punkt 3.2. wird vorausgesetzt.)
```
$ openssl req -new -nodes -keyout zammad.key -out zammad.csr -config san.cnf
```
```
$ openssl x509 -req -in zammad.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out zammad.crt -days 3650 -extensions v3_req -extfile san.cnf
```

3. Prüfen, ob das Zertifikat die Domain & IP enthält
```
$ openssl x509 -in zammad.crt -noout -text | grep -A1 "Subject Alternative Name"
```


4. selbsterstelltes Server-Zertifikat in Nginx einbinden (falls noch nicht geschen)
	- Wie die genaue Konfig aussehen muss, `ist in Punkt 5. beschrieben.`
```
$ sudo nano /etc/nginx/sites-available/zammad
```
- Konfig falls nötig anpssen
- Wenn Änderungen vorgenommen worden sind, dann:
```
$ sudo nginx -t
$ sudo systemctl restart nginx
```


### Zertifikat von Server mit [SCP](https://de.wikipedia.org/wiki/Secure_Copy) herunterladen
- Unter `Windows` kann das Programm [WinSCP](https://winscp.net/eng/index.php) genutzt werden, um mit SCP das Zertifikat kopieren zu können.

> Anleitung für Linux-Systeme:

- Zertifikat über SCP kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/home/username/zammad-zertifikate/zammad.crt /dein/lokales/verzeichnis/zielordner
```

- Wenn ein Berechtigungsfehler erscheint (Befehle auf dem Server ausführen):
```
$ ssh user@IP-Adresse
```
```
$ cd /home/username/zammad-zertifikate
$ ls
```
```
$ sudo chmod 644 home/username/zammad-zertifikate/zammad.crt
```
- Nun nochmal mit SCP versuchen, die Datei zu kopieren.


## rootCA Zertifikat in Browser als vertrauenswürdig einstufen
- Die `Chrome basierten Browser` nutzen den `Zertifikatsspeicher des Betriebssystems`.
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


### Zugriff auf Zammad - HTTP / HTTPS
- Wenn alles korrekt konfiguriert wurde, sollte das Zammad folgendermaßen erreichbar sein:
	- Zu administrativen Zwecken über HTTP: http://IP-Adresse:8080
	- Über die IP-Adresse: HTTP-Anfragen sollten automatisch auf HTTPS umgeleitet werden.
	- GGf. über den internen DNS-Eintrag mit Umleitung auf HTTPS

- Das heißt, sofern nicht direkt der Port 8080 aufgerufen wird, sollten alle HTTP-Anfragen (über Port 80) auf HTTPS, also Port 443, umgeleitet werden (egal, ob über die IP-Adresse oder den DNS-Eintrag).

-------------------------------------------------------------------------------------------------------------
