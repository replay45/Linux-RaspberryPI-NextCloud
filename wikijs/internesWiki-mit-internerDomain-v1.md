# internes [Firmen-Wiki](https://de.wikipedia.org/wiki/Enterprise_Wiki) über eine Domain in einem lokalen Netzwerk ([Intranet](https://de.wikipedia.org/wiki/Intranet)) aufrufen - mit [Wikijs](https://js.wiki/)


`Anleitung erstellt am 1.2.2025`

`Für diese Anleitung wurde für Wiki.js über Docker auf einem Ubuntu-Server genutzt.`

`Als DNS-Server wurde ein Active-Directory - Windows-Server verwendet.`

`Um die Domain im internen Netzwerk aufrufen zu können, benötigt man einen eigenen DNS-Server für das Netzwerk !`

`Es ist NICHT nötig Ports im Router zu öffnen ! - Lediglich im Ubuntu-Server müssen die entsprechenden Ports geöffnet werden.`



# Ziel dieser Anleitung
- Ziel ist es, eine Domain für ein internes [Firmen-Wiki](https://de.wikipedia.org/wiki/Enterprise_Wiki) im lokalen [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) einzurichten, um das Wiki im lokalen Netzwerk aufrufen zu können.
- Diese Anleitung zeigt `nicht`, wie das Wiki über das Internet erreichbar gemacht werden kann.
- Wenn man das Wiki auch über das Internet erreichbar machen möchte, sollte man unbedingt eine `verschlüsselte HTTPS-Verbindung` einrichten.



# 1. Domain in [Wiki.js](https://js.wiki/) selber festlegen
- Wiki.js Admin-Dashboard öffnen,
- zu dem Reiter `Allgemeines` wechseln
- lokale Domain festlegen (z.B. wiki.lokal.com)
- Mehr zur Domain für Wiki.js ist in der [offiziellen Dokumentation](https://docs.requarks.io/) zu finden.



# 2. [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse)
- Dem Server, auf dem Wiki.js läuft, eine feste IP-Adresse im [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) vergeben.
- Je nach Netzwerk kann der [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) z.B. ein Router oder auch der [Active Directory](https://de.wikipedia.org/wiki/Active_Directory) - [Windows-Server](https://en.wikipedia.org/wiki/Windows_Server) sein.



# 3. Im [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) des Netzwerkes - hier im Active Directory Windows-Server einen Eintrag festlegen
- Windows-Server öffnen.
- ggf. [DNS-Server](https://de.wikipedia.org/wiki/Domain_Name_System) installieren & einrichten, falls noch nicht geschehen.
- DNS-Eintrag erstellen und Weiterleitung zur entsprechenden IP-Adresse vom Server mit dem Wiki einrichten.



# 4. [Ubuntu-Server](https://ubuntu.com/download/server) interne Weiterleitung von Domain zu [Wiki.js](https://js.wiki/) auf Port 3000
- [Nginx](https://nginx.org/) - [Web-Server](https://de.wikipedia.org/wiki/Nginx) installieren
```
$ sudo apt install nginx
```

- [Nginx](https://nginx.org/)-Konfiguration erstellen
```
$ sudo nano /etc/nginx/sites-available/wiki
```

- Folgendes in die Konfiguration einfügen:
```
server {
    listen 80;
    server_name wiki.lokal.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
- `wiki.lokal.com` muss ggf. in der Konfigurationsdatei mit der `eigenen Domain ersetzt` werden.
- Mit `STRG+X` speichern und schließen.



# 5. [Firewall](https://ubuntu.com/server/docs/firewalls) vom [Ubuntu-Server](https://ubuntu.com/download/server) mit [Firewall-Manager ufw](https://wiki.ubuntuusers.de/ufw/)
- HTTP-Anfragen erlauben und Status der Firewall abrufen
```
$ sudo ufw allow http
$ sudo ufw status
```

- falls HTTPS genutzt werden soll
```
$ sudo ufw allow https
```

- ggf. SSH erlauben
```
$ sudo ufw allow ssh
```

- falls noch nicht geschehen
```
$ sudo ufw enable
$ sudo ufw status
```



# 6. Aktivieren der Konfiguration
```
$ sudo ln -s /etc/nginx/sites-available/wiki /etc/nginx/sites-enabled/
```

- Konfig testen
```
$ sudo nginx -t
```
- Wenn die Meldung `syntax is ok` erscheint, ist alles in Ordnung.



# 7. [Nginx](https://nginx.org/) neu starten
```
$ sudo systemctl restart nginx
```



# 8. im Browser testen
- Browser öffnen und Domain eingeben.
- ggf. Browser-Cache leeren oder Inkognito-Modus verwenden, um Fehler zu vermeiden.



# 9. ggf. Seite im Terminal testen
```
$ curl -I http://deine.domain.com
```
```
$ curl -I http://wiki.lokal.com
```
Mit `STRG+C` verlassen



# 10. DNS-Auflösung testen
- Mit `nslookup`
```
$ nslookup deine.domain.com
```
```
$ nslookup wiki.lokal.com
```
- Wenn die richtige IP-Adresse ausgegeben wird, läuft die DNS-Auflösung korrekt.



# 11. Bei Fehlern können einige der folgenden Überprüfungen durchgeführt werden
- ggf. Browser-Cache leeren oder Inkognito-Modus verwenden, um Fehler zu vermeiden.
- Docker überprüfen - Hier kann man auch die Ports sehen, auf dem der Docker-Container läuft.
```
$ cd wiki
$ sudo docker ps
```


- Server pingen, um Erreichbarkeit zu prüfen
```
$ ping IP-Adresse
```


- Firewall neu laden
```
$ sudo ufw reload
$ sudo ufw status
```


- Nginx-Konfiguration neu laden
```
$ sudo systemctl reload nginx
```


- Logs überprüfen

Zugriffslog
```
$ sudo tail -f /var/log/nginx/access.log
```
Fehlerlog
```
$ sudo tail -f /var/log/nginx/error.log
```

- DNS-Auflösung aus `Punkt 10` erneut überprüfen.


----------------------------------------------------------------------------------------------
