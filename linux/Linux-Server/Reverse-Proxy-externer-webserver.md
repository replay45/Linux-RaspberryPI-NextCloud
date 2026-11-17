# Nginx Reverse Proxy so erweitern, dass ein externer Webserver eingebunden werden kann

`Beitrag erstellt am 13.11.2026`

- Ziel ist eine bestehenden Reverse Proxy Konfiguration so zu erweitern, dass nicht nur lokale Webdienste, sondern auch Webserver auf anderen Servern/VMs mit einer anderen IP-Adresse über z.B. eine subdomain einzubinden.


### Beispielkonfiguration
- Folgende Beispiel Konfiguration geht von einem lokalen Webserver auf Port 80 aus und einem Webserver auf eiener anderen Maschine.
    - Die Konfiguration geht davon aus, dass der andere Server HTTPS auf Port 443 macht, die Zertifikatsprüfung wird jedoch übersprungen
    - Falls der Webserver, der nicht physisch auf dem gleichen Host läuft, kein HTTPS macht sollte trotzdem irgendwie sichergestellt werden, dass zwischen Reverseproxy und entfernten Webserver die Netzwerkverbindung verschlüsselt wird.
```
server {
    listen 80;
    server_name DOMAIN.EXAMPLE;
    return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name DOMAIN.EXAMPLE;

  ssl_certificate     /etc/ssl/domain-zertifikate/domain.crt;
  ssl_certificate_key /etc/ssl/domain-zertifikate/domain.key;
  client_max_body_size 10M;
  proxy_redirect    off;

  location / {
    proxy_pass http://127.0.0.1:80;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP  $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;
    proxy_set_header X-Forwarded-Port 443;
    proxy_set_header X-Forwarded-Ssl on;
    }
 }
}
server {
  listen 443 ssl;
  server_name subdomain.domain.example;

  ssl_certificate     /etc/ssl/subdomain-zertifikate/subdomain.crt;
  ssl_certificate_key /etc/ssl/subdomain-zertifikate/subdomain.key;

  location / {
    proxy_pass https://IP-Adresse:443;
    proxy_ssl_verify off;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto https;

    proxy_connect_timeout 60s;
    proxy_send_timeout 3600s;
    proxy_read_timeout 3600s;
  }
}
```

- Syntax überprüfen
```
$ sudo nginx -t
```

- Nginx neu starten
```
$ sudo systemctl restart nginx
```


### Falls noch benötigt, Zertifikate ausstellen - selbstsignierte Zertifikate
- Ordner erstellen
```
$ sudo mkdir /etc/ssl/subdomain-zertifikate
$ cd /etc/ssl/subdomain-zertifikate
```
- San-Konfig erstellen
```
$ sudo nano san.cnf
```
- einfügen & (DNS-Namen), IP-Adresse und Informationen anpassen
    - Besonders der Platzhalter `IP-ADRESSE` muss angepasst werden.
    - Die Punkte `stateOrProvinceName, localityName, organizationName`, wie auch `DNS.1   = subdomain.example` sind `optional` und können vollständig aus der Konfig gelöscht werden.
    - Mit `STRG+X` speichern und verlassen
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
commonName             = Server-Hostname

[ req_ext ]
subjectAltName = @alt_names


[ alt_names ]
DNS.1   = subdomain.domain.example
IP.1    = IP-ADRESSE


[ v3_req ]
subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + subdomain-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit


- subdomainCA erstellen (Client-Zertifikat):
    - Den Platzhalter "ORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `subdomainCA.crt` und `subdomainCA.key` erstellt wurden.
```
$ sudo openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout subdomainCA.key -out subdomainCA.crt -subj "/C=DE/CN=ORGANISATION-SUBDOMAIN-CA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `subdomainCA.srl, subdomain.crt, subdomain.csr, subdomainCA.key` erstellt wurden.
```
$ sudo openssl req -new -nodes -keyout subdomain.key -out subdomain.csr -config san.cnf
```
```
$ sudo openssl x509 -req -in subdomain.csr -CA subdomainCA.crt -CAkey subdomainCA.key -CAcreateserial -out subdomain.crt -days 3650 -extensions v3_req -extfile san.cnf
```

- Zuletzt das CA-Zertifikat `subdomainCA.crt` an die Clients verteilen, z.B. über GPO.

-----------------------------------------------------------------------------------------------------
