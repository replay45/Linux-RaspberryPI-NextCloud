# Zammad konfigurieren & einrichten

`Anleitung erstellt am 25.3.2025, zuletzt bearbeitet am 9.9.2026`

- Link zur [Zammad Admin Dokumentation](https://admin-docs.zammad.org/de/latest/#)

## Inhaltsverzeichnis
1. Ersteinrichtung von Zammad (Admin Account anlegen)
2. Ersteinrichtung & Einstellungen
3. LDAP-Integration (Benutzerkonten über Active Directory)
4. E-Mail-Integrationen


-------------------------------------------------------------------------------------------------------------


# 1. Ersteinrichtung von Zammad (Admin Account anlegen)
- `http://server-ip:8080` (falls reverse-Proxy schon eingerichtet wurde: https://server-ip)
- Einrichtungsassistenten folgen
    - Admin-Konto erstellen
    - Nach der Erstellung des Admin-Kontos im Browser wird man zum Dashboard weitergeleitet.

### Im Browser angemeldet bleiben (Hinweis)
- Wenn man im Browser angemeldet bleiben möchte, vor der Eingabe der Zugangsdaten im Login-Fenster `An mich erinnern` anwählen !


-------------------------------------------------------------------------------------------------------------


# 2. Ersteinrichtung & Einstellungen

### Verwalten > Benutzer
- Unter diesem Reiter können lokale Benutzerkonten angelgt werden.
- Wenn zukünftig oder bereits Integrationen für Benutzeranmeldungen, wie LDAP etc. genutzt werden/ werden sollen, dass keine konflikte mit doppelten Nutzernamen und E-Mail-Adressen entstehen.
- Standardmäßig nutzt Zammad bei lokalen Bneutzern die E-Mail als Bneutzername für die Anmeldung.


### Verwalten > Gruppen
- Gruppen dienen dazu Tickets zu sortieren, sodass festgelegt werden kann, zu welcher "Gruppe" ein Ticket gehört, damit die richte Rolle von Support-Mitarbeitern daran arbeiten kann, die Tickets entsprechend sortiert werden und andere Bedingungen eingestellt werden können.


### Verwalten > Rollen
- Unter Rollen können Rollen erstellt werden, die dann Benutzern automatisch oder manuell zugewiesen werden können, um Berechtigungen zu steuern.
- Somit kann man einfach Rollen für Admins, Rollen für Support-Mitarbeiter und Rollen für Kunden etc. anlegen.
- Dabei kann man auch unterschiedliche Rollen für Support-Mitarbeiter anlegen, um festzulegen welche Gruppe von Support-Mitarbeitern an welchen Ticket-Gruppen arbeiten darf.


### Verwalten > Übersichten
- Hier können die Kategorien festgelegt werden, in denen die Ticketes für die Ticket-Moderatoren/ den Support-Mitarbeitern angezeigt werden.
- z.B. geöffnete Tickets, geschlossene Tickets, Tickets sortiert nach Gruppen etc.


### Einstellungen > Branding
- Bei "Produktname" kann ein beliebiger Name angegeben werden, dieser erscheint dann z.B. im Bowser Tab, als Benennenung des Tabs.
- Außerdem können Name der Organisation und Logo angepasst werden.


### Einstellungen > System 
- [Speicherung von Anhängen](https://admin-docs.zammad.org/de/latest/settings/system/storage.html)
    - Unter diesem Punkt kann man einstellen, wie Anhänge gespeichert werden sollen.
    - Standardmäßig werden Anhänge in der SQL-Datenbank gespeichert, allerdings kann das dazu führen, dass die Datenbank schnell anwächst, was zu deutlichen Performance Problemen führen kann.
    - Empfohlen ist die Speichermethode `Dateisystem`. Dabei werden die Anhänge unter `/opt/zammad/storage/` gespeichert.
    - Bei der Erstellung von Backups muss dann entsprechend beachtet werden die Datenbank und die Anhänge zu backupen.
    - Es ist nachträglich möglich den Speicherort für existierende Anhänge zu ändern, die entsprechenden Befehle dazu finden sich in der offiziellen Dokumentation.


### Einstellungen > System
- Basis
    - Unter "Vollqualifizierter Domainname" (FQDN) sollte entweder die öffentliche Domain oder alternativ ein lokaler DNS Eintrag rein, der verwendet wird, um die WebUI aufzurufen.
    - Wird eine `öffentliche Domain` verwendet, dann unter HTTP-Typ `https` anwählen und wird ein lokaler `reverseproxy`, wie NGINX genutzt und/oder Zammad über eine private IP-Adresse genutzt, dann muss hier unbedingt `http` ausgewählt bleiben !
    - Die SystemID ist die vorangestellte Nummer bei der Ticketnummer, es wird automatisch eine zugewiesen, bei der Ersteinrichtung kann diese noch angepasst werden, bei Produktivsystemen sollte diese jedoch dann nicht mehr geändert werden !


### Einstellungen > Sicherheit
- Basis
    - Hier kann eingestellt werden, ob Nutzer auf der Anmeldeseite sich neu registrierenkönnen. Je NAch Anwendungsfall von Zammad deaktivieren !
    - Ebenso kann eingestellt werden, ob Benutzer die Passwort vergessen-Option verwenden können, wenn später Integrationen, wie z.B. LDAP genutzt werden soll, könnte man die Option deaktivieren.
    - Außerdem kann noch das Sitzungstimeout eingesetllt werden.

- Passwort
    - Hier können Policys für die Anforderungen an Passwörter angepasst werden


### Einstellungen > Ticket
- Nummer
    - Hier kann die Länge der Ticketnummer, die Position der Nummer und weitere Eigenschaften zur Nummer eingestellt werden.


### System > Wartungsmodus / Wartungsnachricht
- Im Admin Dashboard in den Einstellungen unter `System > Wartung` kann der Wartungsmodus -Modus aktivert und eine Wartungsnachricht `@Login` eingestellt, die separat eingestellt werden kann eingerichtet werden.
- Die Wartungsnachricht `@Login` kann auch unabhänig vom Wartungsmodus genutzt werden.


### System > Sitzungen
- Im Admin Dashboard in den Einstellungen unter `System > Sitzungen` können die Sitzungen (angemeldete Nutzer) eingesehen werden. 


-------------------------------------------------------------------------------------------------------------


# 3. LDAP-Integration (Benutzerkonten über Active Directory)

### Was ist [LDAP / LDAPS](https://de.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol) ?
- LDAP ist ein offenes Protokoll, das für die Authentifizierung in Verzeichnisdiensten verwendet wird. LDAP wird häufig in Active Directory-Umgebungen verwendet, um Benutzer zu authentifizieren.
- LDAPS ist LDAP over SSL/TLS und somit die verschlüsselte Variante von LDAP über Port 636.
- Außerhalb von Testumgebungen sollte man daher immer LDAPS nutzen.


### Prüfen ob auf Active Directory-Server (AD) LDAP oder LDAPS aktiv ist
- Standard-Ports: 
	- LDAP verwendet normalerweise Port 389 
	- LDAPS verwendet Port 636

- Testen mit telnet
	- Wenn die Verbindung erfolgreich ist, bedeutet dies, dass der AD-Server LDAPS unterstützt.
```
$ telnet <AD-Server-IP> 636
```

- Verbindung über PowerShell prüfen:
	- Wenn der Test erfolgreich ist, zeigt dies, dass LDAPS aktiviert und verfügbar ist.
```
$ Test-NetConnection -ComputerName <AD-Server-IP> -Port 636
```

- Auf Linux prüfen
```
$ openssl s_client -connect SRV-DC:636 -showcerts
```


### Integrations-Benutzer im Active Directory erstellen
- Auf LDAP/Active-Directory Server anmelden
- In der Benutzerverwaltung des Active Directory/ der Domäne einen neuen Benutzer anlegen, der nur für die LDAP-Integration in Zammad genutzt wird.

- Hinweis
    - Da man über LDAP mindestens einen Benutzer importieren muss, der in Zammad Admin-Berechtiungen erhält, empfiehlt es sich einen Zammad-Admin-Benutzer in der Domäne anzulegen, diesem `in der Domäne selber möglichst keine Berechtigungen` zu geben und eine Sicherheitsgruppe erstellen und diesem Benutzer zuweisen, die dann später in Zammd gemappt wird, als Admin-Gruppe, sodass der LDAP-Benutzer in Zammad Admin ist, `ohne` Admin-Berechtigungen in der Domäne.

### Aktivieren der LDAP-Integration in Zammad
- Zammad Admin-Bereich öffnen
- LDAP-Integration aktivieren
	- System
	- Integration
	- LDAP

- LDAP-Konfiguration hinzufügen
	- Aktivieren
	- `Neue Quelle`
	- Hostname des LDAP-Servers
	- Name der Verbindung einfügen
	- Base DN: `DC=domain,DC=local` - "mehr unten unter Base DN" (sollte automatisch ausgefüllt werden)
	- Bind User: Nutzername des LDAP-Benutzers der für die Anmeldung im LDAP erstellt wurde
	- Bind Password: Passwort des gleichen LDAP-Benutzers der für die Anmeldung beim LDAP-Server verwendet wird
	- SSL-Verifizierung zunächst bei lokalen, nicht öffentlich erreichbaren Domänen (temporär) auf `NEIN`

- Base DN
    - Hier legt man die Attribute der Domäne fest
    - Wenn die Domäne domäne.local heißt, dann müssen die Attribute entsprechend angepasst werden `DC=domäne,DC=local`
    - man kann auch zusätzlich OUs (Organisationseinheiten) hinzufügen: `OU=Users,DC=domäne,DC=local` dabei muss der Name der OU entsprechend der OU angepasst werden, in der die zu importierenden User sind.
    - Es ist auch möglich mehrere OUs anzugeben.


### LDAP Zuordnung - Benutzer
- Hier kann festgelegt werden welche Attribute vom LDAP Server auf die entsprechenden Zammad-Attribute gemappt werden, z.B. givenname (LDAP) für Vorname (Zammad) oder samaccountname (LDAP) für Login (Zammad) etc.


### Sicherheitsgruppen (LDAP) / Rollen (Zammad)
- Unter Rollen könne Sicherheitsgruppen gemappt werden, also Sicherheitsgruppen aus dem LDAP/Active Directory in Rollen in Zammad übertragen werden, sodass Benutzer die eine Rolle im Active Directory haben, dann in Zammad die entsprechend die dazugehöhrige Rolle erhalten, mit der sie dann die nötigen Berechtigungen in Zammad selber bekommen.
- So kann das Rollenmanagement von Benutzern im Active Directory bleiben.
- Dafür muss man unter dem Punkt LDAP-Rollen die Sicherheitsgruppe aus dem Active Directory angeben und die entsprechende Rolle in Zammad zuweisen.
    - Die LDAP-Sicherheitsgruppen sollte im Dropdown-Menü auswählbar sein und müssen dann auf eine Zammad-Rolle zugewiesen werden.

- Experte (Benutzer ohne angegebene Gruppe (nicht)-synchronierieren)
    - Unter `Experte` kann man nun noch einstellen, was mit Nutzern ohne eine zugeweisene Rolle (über die Sicherheitsgruppe) geschehen soll, z.B. dass diese nicht synchronisiert werden.


### Wenn ein selbst-signiertes Zertifikat verwendet wird, muss dieses als nächstes importiert werden
- auf AD-Server anmelden
- `certlm.msc` öffnen
- `Eigene Zertifikate`
- das CA-Zertifikat des Domincontrollers wählen
- Rechtsklick, Exportieren
- privaten Schlüssel NICHT exportieren
- `Base-64-codiert X.509 (.CER)`
- Namen vergeben
- Das Zertifikat sollte die Endung `crt` haben (Datei kann einfach umbenannt werden)
- Nun unter `Einstellungen > Sicherheit > SSL-Zertifikate` das entsprechende CA-Zertifikat des LDAP-Servers hinzufügen
- Abschließend erneut unter `System > Integration > LDAP` in der LDAP-Integration die SSL-Verifizierung auf `JA` stellen.


### LDAP Benutzer
- Die Benutzer über die LDAP-Integration erscheinen dann unter `Verwalten > Benutzer`.


-------------------------------------------------------------------------------------------------------------


# 4. E-Mail-Integrationen
- Zunächst muss die E-Mail-Domain einsgestellt werden
    - Dafür unter `Kanäle > E-Mail`, `Einstellungen` muss das Feld unter `Benachrichtigungs-Absender` angepasst werden.
    - `#{config.product_name} <noreply@#{config.fqdn}>` muss durch die support-E-Mail Adresse ersetzt werden, z.B. support@firma.de ...
    - `Übermitteln`

### E-Mail - SMTP-Konfiguration
- SMTP-Konfiguration
    - Dafür unter `Kanäle > E-Mail`, `E-Mail-Benachrichtigung` auf `Bearbeiten` und `SMTP - eigene ausgehende SMTP-Einstellungen konfigurieren...` anwählen.
    - SMTP: vom E-Mail Anbieter vorgegebene E-Mail-Adresse
    - Unter Benutzername und Passwort die entsprechenden Zugangsdaten der Support-E-Mail-Adresse eingeben
    - Entsprechenden Port angeben, z.B. 587 für STARTTLS oder 465 für SSL/TLS
    - SSL-Verifizierung sollte auf `JA` stehen.


### E-Mail-Integration mit Microsoft 365 - [Dokumentation](https://admin-docs.zammad.org/en/latest/channels/microsoft365-graph/index.html)
- Zammad empfiehlt für MS365 die Integration names `Microsoft 365 Graph API E-Mail Kanal`.
    - Dabei ist der Kanal sowie für das Versenden als auch von Empfangen von E-Mails verantwortlich.
    - Zunächst sollte sichergestellt sein, dass eine E-Mail z.B. support@firma.de im Exchange Admin-Center erstellt wurde.
    - Im Exchange Admin-Center sollte man auch alle Zugriffs Optionen für die support-Adresse deaktivieren die nicht benötigt werden, z.B. SMTP, IMAP, POP, Outlook etc. deaktivieren.


- Backup
    - An dieser Stelle sollte man sicherstellen, dass ein aktuelles Backup der Datenbank vorhanden ist, oder eines anfertigen.
    - Dokumentationen dazu hier: [Backups-von-zammad-erstellen](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/zammad/Backups-von-zammad-erstellen.md)


- Nun muss man die E-Mail-Domain einstellen
    - Dafür unter `Kanäle > Microsoft 365 Graph API E-Mail Kanal`, `Einstellungen` muss das Feld unter `Benachrichtigungs-Absender` angepasst werden.
    - `#{config.product_name} <noreply@#{config.fqdn}>` muss durch die support-E-Mail Adresse ersetzt werden, z.B. support@firma.de ...
    - `Übermitteln`


- FQDN - interner DNS-Eintrag
    - Wenn Zammad über eine private IP-Adresse läuft und keine öffentliche Domain hat, müssen noch ein paar Anpassungen vorgenommen werden.
    - Zunächst muss der `vollqualifizierte Domainname` unter `Einstellungen > System > Basis` eingestellt werden.
    - Hier empfiehlt sich einen lokalen DNS-Eintrag zu verwenden, z.B. support.intern.de und diesen dann im `DNS Server hinterlegen`, sodass dieser auf die lokale IP auflöst. Dabei sollte leider auf dedizierte Endungen zurückgegriffen werden (wie .de), da es ansonsten zu HTTPS-Problemen kommen kann.
    - GGf. müssen die selbstsignierten Zertifikate neu erstellt werden, sodass diese auch für den DNS-Eintrag gelten, falls dies noch nicht geschehen ist.


- Callback URL (mit nginx-Reverse Proxy):
    - Da Microsoft nur Callback URLs mit HTTPS akzeptiert müssen zunächst noch ein paar Anpassungen vorgenommen werden.
    - Zunächst per SSH auf den Ubuntu-Server einloggen und in das Zammad-Docker Verzeichnis wechseln und eine neue Datei erstellen/ falls vorhanden bearbeiten:
```
$ cd zammad-docker-compose
$ ls -a
$ sudo nano .env
```

- Die .env Datei muss folgendes Enthalten:
    - Sicherstellen, dass bei `ZAMMAD_HTTP_TYPE=https` steht, ansonsten anpassen !
```
ZAMMAD_FQDN=LOKALER-DNS-EINTRAG
ZAMMAD_HTTP_TYPE=https
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
ZAMMAD_HTTP_TYPE: https
```

- Wenn die Werte korrekt gesetzt sind, dann folgenden Befehl nutzen, um Container neu zu starten:
```
$ docker compose up -d
```


- Erneut im Zammad-Admin-Dashboard unter `Einstellungen > System > Basis` muss jetzt noch unter `HTTP-Typ` `HTTPS` ausgewählt werden. Zum speichern noch `Übermitteln` drücken.
    - Dadurch, dass in der .env-Datei `ZAMMAD_HTTP_TYPE: https` gesetzt wurde, sollte KEIN CSRF Token Problem auftreten.

- Nun dann unter `Kanäle > Microsoft 365 Graph E-Mail` und auf `Connect Microsoft 365 App` klicken, um die URL zu kopieren, diese dann zwischenspeichern. 
    - Die Callback URL sollte mit `https` beginnen und einen internen DNS Namen/FQDN verwenden.
    - Die Callback URL könnte z.B. so beginnen: `https://zammad.intern.de/api...`.


- Einrichtung (EntraID):
    - Als nächstes muss man im Entra ID Admin-Center eine Application registrieren.
    - Dafür im Entra ID Admin Center anmelden.
    - In der linken Menüleiste unter `Applications > App registrations` `New registration` auswählen.
    - Nun einen passenden Namen eingeben, z.B. `Zammad-Helpdesk` etc.
    - Unter Supported account types sollte man am besten `Accounts in this organizational directory only (Single tenant)` oder auf Deutsch `nur ein Mandat ...` wählen.
    - Unter `Redict URL` dann `Web` auswählen und die zwischengespeicherte Callback URL einfügen.
    - Registrieren auswählen
    - Nun die Anwendungs-ID (Client), die Objekt ID und die Verzeichnis-ID (Mandat) kopieren, z.B. in einen Passwortmanager
    - Jetzt muss noch zusätzlich ein neues "Geheimnis" hinzugefügt werden.
    - Dafür unter `Clientanmeldeinformationen` auf `Ein Zertifikat oder Geheimnis hinzufügen` klicken.
    - Nun auf `neuer geheimer Schlüssel` gehen eine Beschreibung hinzufügen, Gültigkeitsdatum auswählen und "Hinzufügen".
    - Zuletzt dann den `Wert des geheimen Schlüssels` kopieren und ebenfalls in einem Passwortmanager abspeichern.

- Einrichtung in Zammmad (Konto Hinzufügen)
    - Zammad im Browser über den DNS-Eintrag öffnen, damit die Weiterleitung mit der Callback-URL später funktioniert.
    - Erneut unter `Kanäle > Microsoft 365 Graph E-Mail` und auf `Connect Microsoft 365 App` und die `Anwendungs-ID (Client)` sowie die `Verzeichnis-ID (Mandat)` in die vorgesehenden Felder einfügen.
    - Schließlich noch den `Wert` des geheimen-Schlüssels hinzufügen.

- Administrator Zustimmung
    - Jetzt auf `Administrator-Zustimmung beantragen` klicken, um sich mit den Zugangsdaten eines Admin-Kontos bei Microsoft anzumelden.
    - authentifizieren
    - Wenn die Weiterleitung zu Zammad über die Callback URL erfolgreich ist, dann sollte die Authentifizierung geklappt haben.
    - Das importieren und Einrichten der E-Mail des Admin-Kontos ist nicht erforderlich und kann übersprungen werden.

- Support-E-Mail-Konto hinzufügen 
    - Jetzt sollte mit `Konto hinzufügen` das eigentliche Postfach des Support-E-Mail-Kontos hinzugefügt werden.

- Nun sollte die Integration der Support-Mail abgeschlossen sein und es können innerhlb von Tickets E-Mails versendet werden.
    - Dabei sollte man beachten, dass auch das E-Mail Symbol ausgewählt wird, wenn ein Nachricht in das Ticket geschickt wird, sofern gewünscht.


-------------------------------------------------------------------------------------------------------------

