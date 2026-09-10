# selbstsigniertes IP-SSL-Zertifikat für [Synology-NAS](https://www.synology.com/de-de) erstellen (inkl. CA-Zertifikat)

`Anleitung erstellt am 6.6.2026`

`gestestet mit Synology DSM 7.3.2`

- Ziel ist es, ein selbstsigniertes Zertifikat für eine lokale [IP-Adresse](https://de.wikipedia.org/wiki/IP-Adresse) zu erstellen und das entsprechende CA-Zertifikat in den/die Client(s) importieren zu können, um eine sichere [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure)-Verbindung ohne Zertifikatswarnungen herstellen zu können.


### selbstsigniertes Zertifikat auf einem Linux-Client erstellen
- Ordner erstellen
```
$ mkdir Zertifikate
$ cd Zertifikate
```

- San-Konfig erstellen
```
$ sudo nano san.cnf
```

- einfügen & [DNS](https://de.wikipedia.org/wiki/Domain_Name_System)-Namen, IP-Adresse und Informationen anpassen
    - Die Punkte `stateOrProvinceName, localityName, organizationName` sind `optional` und können vollständig aus der Konfig gelöscht werden.
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
DNS.1   = Synology-NAS.local.com
IP.1    = IP-ADRESSE

[ v3_req ]

subjectAltName = @alt_names
```

- Server-Zertifikat erstellen + Synology-NAS-Zertifikate für Clients
    - `x509` Erzeugt ein selbst signiertes Zertifikat, `-nodes` Speichert den privaten Schlüssel unverschlüsselt, `-days 3650` Gültigkeitsdauer des Zertifikats (hier 3650 Tage, also 10 Jahre), -newkey `rsa:4096` Erstellt einen neuen RSA-Schlüssel mit 4096 Bit


- Synology-NAS-CA erstellen (Client-Zertifikat):
    - Den Platzhalter "MEINEORGANISATION" ersetzen, z.B. durch den Firmenname etc.
    - Mit `$ ls` prüfen, ob die beiden Dateien `Synology-NAS-CA.crt` und `Synology-NAS-CA.key` erstellt wurden.
```
$ openssl req -x509 -nodes -days 3650 -newkey rsa:4096 -keyout Synology-NAS-CA.key -out Synology-NAS-CA.crt -subj "/C=DE/CN=MEINEORGANISATION-&-ZERTIFIKATSFUNKTION-CA"
```

- Server-Zertifikat erstellen & signieren:
    - Mit `$ ls` prüfen, ob die Dateien `Synology-NAS-CA.srl, Synology-NAS.crt, Synology-NAS.csr, Synology-NAS-CA.key` erstellt wurden.
```
$ openssl req -new -nodes -keyout Synology-NAS.key -out Synology-NAS.csr -config san.cnf
```
```
$ openssl x509 -req -in Synology-NAS.csr -CA Synology-NAS-CA.crt -CAkey Synology-NAS-CA.key -CAcreateserial -out Synology-NAS.crt -days 3650 -extensions v3_req -extfile san.cnf
```

- Optional überprüfen
```
$ openssl verify -CAfile Synology-NAS-CA.crt Synology-NAS.crt
```


-------------------------------------------------------------------------------------------------------------


# Zertifikat in Synology NAS importieren
- Falls die Zertifikatsdateien auf einem Linux-Server, statt auf einem Linux-Client erstellt wurden, müssen diese ggf. mit SCP oder WinSCP heruntergeladen/kopiert werden.

### Windows:
- Für Windows gibt es das Tool [WinSCP](https://winscp.net/eng/index.php).

### Linux:
- Zertifikat über SCP kopieren (Befehl auf lokalem Rechner ausführen):
```
$ sudo scp user@IP-ADRESSE:/home/username/.../cert.crt /lokales/verzeichnis/zielordner
```

## Zertifikat in Synology NAS importieren (DSM/WebUI)
- Auf synology NAS im DSM (WebUI) anmelden
- Systemsteuerung
- `Sicherheit > Zertifikat`
- `Hinzufügen` auswählen
- `Neues Zertifikat hinzufügen`
- `Zertifikat importeren` & optional Beschreibung hinzufügen
- Zertifikatsdateien importieren:
    - Privater-Schlüssel ist die .key-Datei (NICHT die CA !)
    - Zertifikat ist der öffntliche Schlüssel, also die .crt-Datei (NICHT die CA !)
- `OK`

### Zertifikat als "Standard" setzen
- Standardzertifikat
    - `Aktion`
    - `Bearbeiten`
    - `Als Standardzertifikat festlegen`
    - OK
- Standardzertifikat für Dienste
    - `Einstellungen`
    - Hier kann man nun einstellen, welches Zertifikat für welchen Dienst verwendet werden soll.


-------------------------------------------------------------------------------------------------------------


## CA in Browser /Betriebssystem importieren
- Nun muss das CA-Zertifikat in den Zertifikatsspeicher des Browser oder Betriebssystems hinzugefügt werden.
- Die `Chrome basierten Browser` nutzen den `Zertifikatsspeicher des Betriebssystems`.
- Der `Firefox` hingegen nutzt per default nur den `eigenen Zertifikatsspeicher`.
- Wenn eine Active Directory (Domäne) genutzt wird, kann eine Gruppenrichtlinie (GPO) erstellt werden, um das CA-Zertifikat automatisch an alle Clients zu verteilen.


-------------------------------------------------------------------------------------------------------------
