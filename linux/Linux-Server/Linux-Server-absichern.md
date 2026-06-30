# Linux-Server absichern

`Anleitung für Ubuntu-Server & Debian geeignet`

## Inhaltsverzeichnis
1. physische Absicherung
2. Ubuntu/Debian-Server-automatisierte-Wartung (Updates/Neustarts)
3. Firewall


---------------------------------------------------------------------------


# 1. physische Absicherung

`Anleitung verfasst am 14.4.2026, zuletzt bearbeitet am 16.6.2026`


### physischer Schutz
- Der Server sollte an einem geeigneten Platz stehen, der kühl und trocken ist und an dem die Luftzufuhr gewährleistet werden kann.
- Außerdem sollte der Server in einer erhöhten Position befestigt werden, sodass der Kontakt mit Wasser vermieden wird, was besonders in Kellern ein wichtiger Punkt ist.
- Optional können physische Sicherungen vorgenommen werden, um den Server vor unbefugten Zugriffen/Manipulationen zu schützen. Dafür eignen sich z.B. Server- oder Netzwerkschränke.
- Zusätzlich kann man Hardware mit Sicherheitssiegel versehen, um diese vor Manipulationen zu schützen, sodass die Hardware nicht geöffnet werden kann, ohne das Siegel zu beschädigen.


### Schutz vor Überspannung/Stromausfall
- Zunächst kann eine Mehrfachsteckdose mit `Überspannungs- und Blitzschutz (Feinschutz)` verwendet werden, um besonders schützenswerte Hardware vor Überspannungen zu schützen. Ein Feinschutz in einer Mehrfachsteckdose ersetzt jedoch keinen fachgerecht installierten Blitzschutz im Sicherungskasten - [Mehr zu Mehrfachsteckdosen & Überspannungsschutz](https://github.com/replay45/Projekt-Sammlung-und-Fun-Projekte/blob/main/Sammlung/Strom.md).
- Einen zusätzlichen Schutz kann auch eine [USV](https://de.wikipedia.org/wiki/Unterbrechungsfreie_Stromversorgung) bieten, sowohl bei `Spannungsschwankungen`, als auch vor `Spannungspitzen`.
- Eine [USV](https://de.wikipedia.org/wiki/Unterbrechungsfreie_Stromversorgung) kann zudem auch bei kompatibler Hardware sicherstellen, dass der Server sich bei einem Stromausfall `korrekt herunterfahren` kann.


---------------------------------------------------------------------------


# 2. Ubuntu/Debian-Server-automatisierte-Wartung (Updates/Neustarts)

## 2.1 Ubuntu-Server automatische Neustarts

`Anleitung verfasst am 14.4.2026`

`Anleitung für Ubuntu-Server & Debian geeignet, getestet mit Ubuntu 24.04 LTS`

### automatisch nach Zeitplan mit Cron
```
$ crontab -e
```

- Cronjob um Server alle 3 Monate um 5 Uhr Nachts neuzustarten.
    - `0 5 * 1-12/3 *` = Minute 0, Stunde 5, *1-12/3 = alle 3 Monate (beginnend mit Jannuar, also Neustart im Monat 1,4,7,10)
```
0 5 * 1-12/3 * /bin/systemctl reboot
```

- Cronjob um Server alle 6 Monate um 5 Uhr Nachts neuzustarten.
```
0 5 *1-12/6 * * /bin/systemctl reboot
```

---------------------------------------------------------------------------

## 2.2. automatische Update-Strategie für balance zwischen Sicherheit und Stabilität/Business Continuity

`Anleitung verfasst am 14.4.2026`

`Anleitung für Ubuntu-Server & Debian geeignet, getestet mit Ubuntu 24.04 LTS`

- Ziel ist es den Linux Server durch automatische Updates abzusichern.
- Um das Beste Ergenbnis aus Sicherheit und Business Continuity zu erhalten, werden nur Sicherheitsupdates automatisiert.
- Dadurch sollte Sicherheit und Stabilität gegeben sein.


### automatische Update-Strategie mit unattended-upgrades
- Installieren von unattended-upgrades
```
$ sudo apt update
$ sudo apt install unattended-upgrades
```

- Konfiguration:
```
$ sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

- Konfig für Ubuntu:
    - Die Konfiguration enthält den foglenden Abschnitt bereits, dieser muss NICHT hinzugefügt werden!
    - Durch die `//` werden die Zeilen auskommentiert.
    - Durch das Entfernen der beiden `//` wird die Zeile aktiv.
    - Updates für Pakete (Programme): `"${distro_id}:${distro_codename}-updates";`
    - Sicherheitsupdates: `"${distro_id}:${distro_codename}-security";`
```
Unattended-Upgrade::Allowed-Origins {
        "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        // Extended Security Maintenance; doesn't necessarily exist for
        // every release and this system may not have it installed, but if
        // available, the policy for updates is such that unattended-upgrades
        // should also install from here by default.
        "${distro_id}ESMApps:${distro_codename}-apps-security";
        "${distro_id}ESM:${distro_codename}-infra-security";
//      "${distro_id}:${distro_codename}-updates";
//      "${distro_id}:${distro_codename}-proposed";
//      "${distro_id}:${distro_codename}-backports";
};
```


- Konfig für Debian & Pi OS (Raspberry Pi):
    - Die Konfiguration enthält den foglenden Abschnitt bereits, dieser muss NICHT hinzugefügt werden!
    - Durch die `//` werden die Zeilen auskommentiert.
    - Durch das Entfernen der beiden `//` wird die Zeile aktiv.
    - Stable-Updates: `"origin=Debian,codename=${distro_codename},label=Debian";`
    - Sicherheitsupdates: `"origin=Debian,codename=${distro_codename}-security,label=Debian-Security";`
```
Unattended-Upgrade::Origins-Pattern {
        // Codename based matching:
        // This will follow the migration of a release through different
        // archives (e.g. from testing to stable and later oldstable).
        // Software will be the latest available for the named release,
        // but the Debian release itself will not be automatically upgraded.
//      "origin=Debian,codename=${distro_codename}-updates";
//      "origin=Debian,codename=${distro_codename}-proposed-updates";
//      "origin=Debian,codename=${distro_codename},label=Debian";
//      "origin=Debian,codename=${distro_codename},label=Debian-Security";
        "origin=Debian,codename=${distro_codename}-security,label=Debian-Security";

        // Archive or Suite based matching:
        // Note that this will silently match a different release after
        // migration to the specified archive (e.g. testing becomes the
        // new stable).
//      "o=Debian,a=stable";
//      "o=Debian,a=stable-updates";
//      "o=Debian,a=proposed-updates";
//      "o=Debian Backports,a=${distro_codename}-backports,l=Debian Backports";
};
```

### automatische Neustarts nach Updates mit unattended-upgrades (Optional)
Wenn Updates ausgeführt werden, die einen neustart verlangen, kann folgende Zeile verwendet werden, um dieses Feature zu aktivieren.

- Konfiguration öffnen
```
$ sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

- Folgende Zeile suchen mit `STRG+W`
```
// Unattended-Upgrade::Automatic-Reboot "false";
```

- Duch entfernen der "//" und setzten auf "true", werden automatische Neustarts aktiviert.
```
Unattended-Upgrade::Automatic-Reboot "true";
```

- Zeit für automatische Neustarts einstellen
    - per Default ist 02:00 AM, also um 2:00Uhr nachts 
```
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```


### Intervall einstellen & aktivieren
- Konfig-Datei 20auto-upgrades öffnen, um automatische Updates zu aktivieren
```
$ sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

- Folgenden Zeilen müssen enthalten sein, wenn diese nicht enthalten sind, hinzufügen:
    - Das Interfvall von `7` (steht für 7 Tage) kann natürlich angepasst werden.
```
APT::Periodic::Update-Package-Lists "7";
APT::Periodic::Unattended-Upgrade "7";
APT::Periodic::AutocleanInterval "7";
```

- Dienst Starten und den autostart aktivieren
```
$ sudo systemctl start unattended-upgrades
$ sudo systemctl enable unattended-upgrades
$ sudo systemctl status unattended-upgrades
```

- Logs prüfen
```
$ cat /var/log/unattended-upgrades/unattended-upgrades.log
```

- Nachdem Nachträgliche Änderungen vorgenommen wurden
```
$ sudo systemctl restart unattended-upgrades
$ sudo systemctl status unattended-upgrades
```

- Manuellen Test durchführen (Testen der Konfig)
```
$ sudo unattended-upgrades --dry-run --debug
```

---------------------------------------------------------------------------


# 3. Firewall

- Firewall Punkt auch in der Anleitung unter Sicherheit unter Linux überarbeiten

### Was ist eine Firewall ?


### Wie funktioniert die Firewall unter Linux ?


### [Firewall Manager - ufw](https://wiki.ubuntuusers.de/ufw/) (Debian-basierte Distributionen)
- Installation & Status
```
$ sudo apt install ufw
$ sudo ufw status
$ sudo ufw status verbose
$ sudo ufw reload
```

- Firewall aktivieren/ deaktivieren/ zurücksetzen
```
$ sudo ufw enable
$ sudo ufw disable
$ sudo ufw reset
```

- Portfreigabe - App-list
```
$ sudo ufw app list
$ sudo ufw allow PAKETNAME
$ sudo ufw allow ssh
```

- klassische Portfreigabe 
```
$ sudo ufw allow <port> <optional: protocol>

$ sudo ufw allow 53
$ sudo ufw allow 53/tcp
$ sudo ufw allow 53/udp
```

- Portfreigabe für eine bestimmte IP-Adresse
```
$ sudo ufw allow from IP-ADRESSE
$ sudo ufw allow from 192.168.2.10

$ sudo ufw allow from IP-ADRESSE to any port PORT
$ sudo ufw allow from 192.168.2.10 to any port 22
```

- Regeln entfernen
```
$ sudo ufw status numbered
$ sudo ufw delete NUMBER
$ sudo ufw delete 2

$ sudo ufw delete allow PORTFREIGABE
$ sudo ufw delete allow 22
$ sudo ufw delete allow http

$ sudo ufw delete deny PORTFREIGABE
$ sudo ufw delete deny 22
$ sudo ufw delete deny http
```

- Anfragen verbieten
```
$ sudo ufw deny <port> <optional: protocol>

$ sudo ufw deny 53
$ sudo ufw deny 53/tcp
$ sudo ufw deny 53/udp
```

- Logging
```
$ sudo ufw logging on 
$ sudo ufw logging STUFE 
$ sudo ufw logging off 
```

---------------------------------------------------------------------------

