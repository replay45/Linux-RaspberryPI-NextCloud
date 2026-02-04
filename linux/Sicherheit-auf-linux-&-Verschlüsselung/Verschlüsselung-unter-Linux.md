# Verschlüsselung auf Linux

`Anleitung erstellt am 19.11.2024, zuletzt bearbeitet am 27.1.2026`


## Inhaltsverzeichnis
1. verschlüsseln auf Ordner-Ebene mit [CryFS](https://www.cryfs.org)
    - Installation von CryFS auf Linux
    - Ein virtuelles, verschlüsseltes Laufwerk erstellen (auf Ordner-Ebene)
    - optional ein Skript erstellen, um das Laufwerk einhängen zu können
    - Skript über eine .Desktop-Datei verknüpfen und ausführbar machen
2. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) unter Linux (Debian-basierte Distributionen)
    - Wieso sollte man die Festplattenverschlüsselung nutzen ?
    - Festplattenverschlüsselung aktivieren
    - Einrichtung von Festplattenverschlüsselung mit [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)
3. weitere Sicherheitsmaßnahmen
    - [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort
    - [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot)
    - Mehr zu Verschlüsselung von externen Speichermedien
    - Mehr zu Sicherheit auf Linux-Systemen


------------------------------------------------------------------------------------------------


# 1. verschlüsseln auf Ordner-Ebene mit [CryFS](https://www.cryfs.org)
CryFS ist ein [Open Source](https://de.wikipedia.org/wiki/Open_Source) `Verschlüsselungsprogramm`, mit dem man ein `verschlüsseltes virtuelles Laufwerk` erstellen kann.
Man kann das virtuelle, verschlüsselte Laufwerk sowohl ohne [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) als auch mit aktivierter Festplattenverschlüsselung des Betriebssystems nutzen.

Sofern man eine Festplattenverschlüsselung des Betriebssystems nutzt und zusätzlich mit CryFS ein `virtuelles verschlüsseltes Laufwerk erstellt`, spricht man von einer `2-Ebenen-Verschlüsselung`.
Das `erhöht erheblich die Sicherheit für die Daten, die durch die zweite Verschlüsselungsebene geschützt sind`.

Es ist auch möglich, `externe Speichermedien` über CryFS zu verschlüsseln, allerdings eignet sich dafür das Tool [VeraCrypt](https://veracrypt.fr/) - (mehr dazu in diesem Ordner unter [Verschlüsselung mit VeraCrypt](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux/Sicherheit-auf-linux-%26-Verschl%C3%BCsselung)), welches sich besonders für die Verschlüsselung von externen Speichermedien eignet und auch von allen gängigen Desktop-Betriebssystemen unterstützt wird.


------------------------------------------------------------------------------------------------


### A. Installation von CryFS auf Linux
```
$ sudo apt install cryfs
```


------------------------------------------------------------------------------------------------


### B. Ein virtuelles, verschlüsseltes Laufwerk erstellen (auf Ordner-Ebene)
- 2 Ordner in einem gewünschten Pfad erstellen, z.B. unter `/home/USER`.
- Dabei könnten die Ordner wie folgt heißen: `entschlüsselt` und `verschlüsselt`.
- WICHTIG: Die neu erstellten Ordner müssen leer sein !
	

- Befehl zum Erstellen eines virtuellen verschlüsselten Laufwerkes:
```
$ cryfs ~/HIER/PFAD/VERSCHLÜSSELTER/ORDNER ~/HIER/PFAD/ENTSCHLÜSSELTER/ORDNER
```

- Dann erscheint die Frage, ob die Standardeinstellungen genutzt werden sollen.
- Hier kann einfach mit `y` bestätigt werden.

- Nun muss ein starkes Passwort gewählt werden.

Um das Laufwerk auszuhängen, erscheint ein "unmount-Befehl" im Terminal.
Optional kann aber auch die Funktion des Dateimanagers genutzt werden, um das System auszuhängen.


### Hinweis zum Speicherort der Daten
- Die Daten liegen ausschließlich in dem verschlüsselten Ordner !
- Beim Entschlüsseln, werden diese zwar im Ordner "entschlüsselt" als Laufwerk angezeigt, jedoch sind die Daten niemals wirklich in dem unverschlüsselten Teil gespeichert !


- Um die Daten wieder zu entschlüsseln und das erstellte Laufwerk wieder einzuhängen, den Befehl anpassen und ausführen:
```
$ cryfs ~/HIER/PFAD/VERSCHLÜSSELTER/ORDNER ~/HIER/PFAD/ENTSCHLÜSSELTER/ORDNER
```


------------------------------------------------------------------------------------------------


### C. optional ein Skript erstellen, um das Laufwerk einhängen zu können
- Nun kann optional ein `Sh-Skript` erstellt werden, um nicht jedes Mal den Befehl ausführen zu müssen, sondern nur das Skript im Terminal zu starten, welches den Befehl automatisch ausführt.

- Dieses Skript muss lediglich den Entschlüsselungsbefehl enthalten und als `Datei.sh` gespeichert werden.

- Mehr zu sh-Skripten in der Datei [bash-sh-skripte](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux) im Linux Repository.


------------------------------------------------------------------------------------------------


### D. Skript über eine `.Desktop`-Datei verknüpfen und ausführbar machen
- Das Skript kann man nun mit einer `.Desktop`-Datei` verknüpfen:
    - Dabei erscheint die `.Desktop`-Datei im App-Menü und führt beim Öffnen das erstellte sh-Skript aus und wenn alles korrekt eingerichtet ist, sollte sich ein Terminal mit der Passwortabfrage öffnen.

- Dabei muss jedoch beachtet werden, dass in der `.Desktop`-Datei die Konfiguration richtig vorgenommen wurde und der Wert `Terminal=true` gesetzt wurde.

- Wie man ein `sh-Skript mit einer ".Desktop"-Datei verknüpft`, wird ebenfalls in diesem Github-Repository unter [bash-sh-skripte](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux) gezeigt.


------------------------------------------------------------------------------------------------


# 2. [LUKS](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS)-[Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) unter Linux (Debian-basierte Distributionen)
- `Wieso sollte man die Festplattenverschlüsselung nutzen ?`
	- Die Festplattenverschlüsselung ist eine sinnvolle Maßnahme zum Schutz aller Daten auf der Festplatte.
	- Bei der Festplattenverschlüsselung wird die gesamte Partition auf der Festplatte verschlüsselt und alle Daten sind bei physischem Diebstahl des Servers trotzdem sicher.
	- Damit der Schutz gewährleistet werden kann, muss eine `starke Passphrase` gewählt werden !
	- Da das Eingeben der Passphrase normalerweise bei jedem Boot-Vorgang notwendig ist, empfiehlt es sich, das [TPM-Modul](https://de.wikipedia.org/wiki/Trusted_Platform_Module) auf dem Mainboard zu verwenden, damit die Passphrase dort verschlüsselt gespeichert werden kann und beim Boot-Vorgang das System automatisch nach der Integritätsprüfung entschlüsselt werden kann.
	- Die Festplattenverschlüsselung zum Schutz der Daten, des Betriebssystems, offline Angriffen oder physischem Diebstahl, ist nicht nur bei Linux notwendig, sondern ebenfalls bei Windows oder MacOS !
	- Allerdings schützt die Festplattenverschlüsselung nicht direkt im laufenden Betrieb, jedoch kann für den zusätzlichen Schutz im laufenden Betrieb mit [CryFS](https://www.cryfs.org) ein `verschlüsseltes virtuelles Laufwerk auf Ordner-Ebene` erstellt werden. Dabei spricht man auch von einer zwei-Ebenen-Verschlüsselung, sofern die Festplattenverschlüsselung aktiv ist - mehr dazu unter Punkt `1. Verschlüsseln auf Ordner-Ebene`.

- `Vorteile der Festplattenverschlüsselung`
	- Alle Daten auf dem Massenspeicher werden verschlüsselt,
	- Schutz bei physischem Diebstahl der Hardware,
	- Schutz vor unbefugtem Zugriff
	- Schutz auch nach Entsorgung des Massenspeichers
	- Schutz vor unbefugtem Zugriff auf das Recovery-Menü, wo das Admin-Passwort zurückgesetzt werden kann
	- Schutz vor weiteren offline-Angriffen auf das System

- `Nachteile`
	- Die Festplattenverschlüsselung schützt nicht direkt im laufenden Betrieb
	- Bei alter Hardware möglicher Performanceverlust


## [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) aktivieren
- Das Aktivieren der Festplattenverschlüsselung ist nach der Installation von Linux nur sehr schwer umzusetzen, daher sollte man bei der `Installation der Linux-Distribution` die Option `Festplatte mit LUKS verschlüsseln` auswählen.
- Die Festplattenverschlüsselung ist bei allen gängigen Distributionen während der Installation möglich, meist unter dem Punkt `Partitionierung`, kann jedoch bei sehr alter Hardware zu Performanceverlust führen.
- Die Einrichtung von TPM kann auch zu einem späteren Zeitpunkt erfolgen.


## Einrichtung von [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) mit [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module)
- `Voraussetzungen für TPM`
	- [LUKS2](https://de.wikipedia.org/wiki/Dm-crypt#Erweiterung_mit_LUKS) und [TPM 2.0](https://de.wikipedia.org/wiki/Trusted_Platform_Module) auf dem Mainboard,
	- systemd-cryptenroll (ab Ubuntu 20.04+ verfügbar)

- `Vorteile von TPM`
	- Automatische Entsperrung beim Boot durch TPM (nur auf dem installierten System),
	- Veränderungen am [Bootloader](https://de.wikipedia.org/wiki/Bootloader) oder Kernel verhindern automatischen Boot-Vorgang (Passphrase muss manuell bestätigt werden)

- `Nachteile`
	- TPM muss ggf. vorhanden und korrekt konfiguriert sein,
	- etwas komplexere Einrichtung,
	- keine Migration des Speichers in anderen Server ohne Re-Enrollment

- `Funktion von TPM`
	- Das TPM Modul auf dem Mainboard speichert die Passphrase verschlüsselt ab.
	- Beim Systemstart prüft das TPM die Integrität des Systems, denn das System wird nur bei unverändertem Systemzustand entschlüsselt.
	- Sollten Änderungen am System z.B. am UEFI oder Bootloader vorgenommen werden, muss die Passphrase manuell eingegeben werden.


## Einrichtung von [TPM](https://de.wikipedia.org/wiki/Trusted_Platform_Module):
- Linux starten, entschlüsseln und anmelden.
- notwendige Pakete installieren
```
$ sudo apt update
$ sudo apt install clevis clevis-luks clevis-tpm2
$ sudo apt install clevis-initramfs
```
- Überprüfen, ob TPM vorhanden und funktionsfähig ist
	- Ausgabe sollten 8 zufällige Bytes sein. Falls nicht, überprüfen, ob TPM-Modul vorhanden ist
```
$ sudo tpm2_getrandom 8
$ reset
```

### Bind LUKS-Volume an TPM:
- Algemeine Erklärung:
    - Ersetzen von `/dev/sdaX` durch eigenen Pfad (z.B. /dev/sda3).
    - Der `FSTYPE` der richtigen Partition ist `crypto_LUKS`
	- Nach dem Ausführen des Befehls, für das Speichern der Passphrase in das TPM-Modul, die entsprechende Passphrase eingeben.

- Partitionsnamen herausfinden:
```
$ lsblk
```

### LUKS-Passphrase in TPM eingeben:
- Was ist PCR ?
    - PCR = Platform Configuration Register
    - PCR umfasst z.B.: [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface), [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)), [Bootloader](https://de.wikipedia.org/wiki/Bootloader)-Konfigurationen, TPM-Konfigurationen oder Initramfs usw.
    - Nur wenn alle "PCRs" unverändert sind, wird entschlüsselt

- Empfohlen: PCR "7" - "gute" Sicherheit
    - Empfohlen den ersten Befehl mit dem Wert PCR "7".
    - PCR "7" misst [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) und [Bootloader](https://de.wikipedia.org/wiki/Bootloader), ist jedoch relativ stabil gegen "kleine Änderungen" oder Betriebssystem-Updates, dadurch weniger restriktiv, aber besser in produktiven/zuverlässigkeitsorientierten Umgebunden.
```
$ sudo clevis luks bind -d /dev/sdaX tpm2 '{"pcr_ids":"7"}'
```


- ALTERNATIV: PCR "0,1,7" - "hohe" Sicherheit (Fehleranfälliger)
    - ALTERNATIV für mehr Sicherheit PCR "0,1,7", schützt besser gegen Manipulationen, ist jedoch fehleranfälliger, selbst bei Kernel-Updates vom Betriebssystem !
    - PCR "0,1,7" prüft [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface), TPM-Konfiguration, [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) und [Bootloader](https://de.wikipedia.org/wiki/Bootloader)-Konfiguration.
    - Eher `NICHT` für produktive/ zuverlässigkeitsorientiere Umgebungen geeignet.
```
$ sudo clevis luks bind -d /dev/sdaX tpm2 '{"pcr_bank":"sha256","pcr_ids":"0,1,7"}'
```


### Überprüfen:
- Clevis testen:
```
$ sudo clevis luks list -d /dev/sdaX
```

- Den Initramfs aktualisieren, damit `Clevis` beim Booten geladen wird:
```
$ sudo update-initramfs -u -k all
```

### Testen
- Server neu starten
- Wenn der TPM-Unlock funktioniert, wird keine Passphrase abgefragt.
- TPM-Unlocking hängt von der Plattformkonfiguration (PCRs) ab. Änderungen an [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface), [Kernel](https://de.wikipedia.org/wiki/Kernel_(Betriebssystem)), [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot) oder Initramfs können TPM-Unlocking verhindern.


------------------------------------------------------------------------------------------------


# 3. weitere Sicherheitsmaßnahmen


### [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort
- Das UEFI-Passwort schützt vor unbefugten Änderungen am UEFI, da man zunächst das Passwort eingeben muss, um in das UEFI zu gelangen.
- Es ist durchaus ratsam, die Option zu aktivieren, da sie einen zusätzlichen Schutz vor Manipulationen bietet.
- Jedoch vorsicht: Wenn man die Zugangsdaten für das UEFI verliert, kann das Gerät schnell unbrauchbar werden. Daher unbedingt die Zugangsdaten notieren und sicher aufbewahren. Dafür eignet sich z.B. ein Passwortmanager.
- Die Option kann man meistens unter dem Reiter `Security` aktivieren und heißt häufig `Administrator-Passwort`, das kann jedoch von Hersteller zu Hersteller unterschiedlich sein.


### [Secure Boot](https://en.wikipedia.org/wiki/UEFI#Secure_Boot)
- Secure Boot ist eine Sicherheitsfunktion (Integritätsschutz), die sicherstellt, dass beim Boot-Vorgang vom Betriebssystem nur vertrauenswürdige Software geladen wird.
- Das Ziel ist, einen Schutz vor Manipulationen am Betriebssystem zu implementieren, um das Laden von Schadcode oder veränderten Bootloadern zu verhindern.
- Technisch funktioniert das über eine digitale Signatur, wodurch nicht signierte oder veränderte Software blockiert wird.
- Die `Einrichtung` von Secure-Boot wird in der Anleitug [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux) gezeigt.


------------------------------------------------------------------------------------------------


- Mehr zu `Verschlüsselung von externen Speichermedien` in diesem Ordner unter [Verschlüsselung mit VeraCrypt](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux)

- Mehr zu `Sicherheit auf Linux-Systemen` in diesem Ordner unter [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux)


------------------------------------------------------------------------------------------------
