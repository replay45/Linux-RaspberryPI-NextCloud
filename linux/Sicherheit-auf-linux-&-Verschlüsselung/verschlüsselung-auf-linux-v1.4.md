# Verschlüsselung auf Linux

`Anleitung zuletzt bearbeitet am 19.11.2024`

## Inhaltsverzeichnis
1. verschlüsseln auf Ordner-Ebene mit [CryFS](https://www.cryfs.org)
    - Installation von CryFS auf Linux
    - Ein virtuelles, verschlüsseltes Laufwerk erstellen (auf Ordner-Ebene)
    - optional ein Skript erstellen, um das Laufwerk einhängen zu können
    - Skript über eine .Desktop-Datei verknüpfen und ausführbar machen
2. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) unter Linux (Debian-basierte Distributionen)
    - Wieso sollte man die Festplattenverschlüsselung nutzen ?
    - Festplattenverschlüsselung aktivieren
3. weitere Sicherheitsmaßnahmen
    - [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort
    - Mehr zu Verschlüsselung von externen Speichermedien
    - Mehr zu Sicherheit auf Linux-Systemen


------------------------------------------------------------------------------------------------


# 1. verschlüsseln auf Ordner-Ebene mit [CryFS](https://www.cryfs.org)


CryFS ist ein Open Source Verschlüsselungsprogramm, mit dem man ein `verschlüsseltes virtuelles Laufwerk` erstellt.
Man kann das virtuelle, verschlüsselte Laufwerk sowohl ohne [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) als auch mit aktivierter Festplattenverschlüsselung des Betriebssystems nutzen.

Sofern man eine Festplattenverschlüsselung des Betriebssystems nutzt und zusätzlich mit CryFS ein `virtuelles verschlüsseltes Laufwerk erstellt`, spricht man von einer `2-Ebenen-Verschlüsselung`.
Das `erhöht erheblich die Sicherheit für die Daten, die durch die zweite Verschlüsselungsebene geschützt sind`.

Es ist auch möglich, `externe Speichermedien` über CryFS zu verschlüsseln, allerdings eignet sich dafür das Tool [VeraCrypt](https://veracrypt.fr/) - (mehr dazu unten unter Punkt 2), welches sich besonders für die Verschlüsselung von externen Speichermedien eignet und auch von allen gängigen Desktop-Betriebssystemen unterstützt wird.


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


# 2. [Festplattenverschlüsselung](https://de.wikipedia.org/wiki/Festplattenverschl%C3%BCsselung) unter Linux (Debian-basierte Distributionen)


### Wieso sollte man die Festplattenverschlüsselung nutzen ?

- Die (vollständige) Festplattenverschlüsselung schützt alle Daten auf der verschlüsselten Partition inkl. dem Betriebssystem und den persönlichen Daten wie Dokumenten, Fotos etc., die ebenfalls auf der verschlüsselten Partition liegen.

- Außerdem schützt die Festplattenverschlüsselung bei Diebstahl davor, dass Dritte an sensible Daten kommen, denn ohne Verschlüsselung der Festplatte könnten Dritte beim Entfernen der Festplatte auf die lokal gespeicherten Daten zugreifen (sowohl bei Linux als auch bei Windows).

- Allerdings schützt die Festplattenverschlüsselung nicht im laufenden Betrieb und ist daher z.B. für File-Sharing-Server nur bedingt sinnvoll, hingegen `bei mobilen Geräten, wie Laptops unbedingt empfohlen` und auch bei Desktop-PCs kann die Festplattenverschlüsselung durchaus sinnvoll sein.

- Um auch bei mobilen Geräten im laufenden Betrieb mehr Schutz für bestimmte Daten zu haben, kann mit [CryFS](https://www.cryfs.org) ein `verschlüsseltes virtuelles Laufwerk auf Ordner-Ebene` erstellt werden. Dabei spricht man auch von einer zwei-Ebenen-Verschlüsselung, sofern die Festplattenverschlüsselung aktiv ist - mehr dazu unter Punkt `1. Verschlüsseln auf Ordner-Ebene`.



### Festplattenverschlüsselung aktivieren

- Das Aktivieren der Festplattenverschlüsselung ist nach der Installation von Linux nur sehr schwer umzusetzen, daher sollte man bei der `Installation der Linux-Distribution` die Option `Festplatte mit LUKS verschlüsseln` auswählen.

- Die Festplattenverschlüsselung ist bei allen gängigen Distributionen während der Installation möglich, meist unter dem Punkt `Partitionierung`, kann jedoch bei sehr alter Hardware zu Performanceverlust führen.


------------------------------------------------------------------------------------------------


# 3. weitere Sicherheitsmaßnahmen


### [UEFI](https://de.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface)-Passwort

- Das UEFI-Passwort schützt vor unbefugten Änderungen am UEFI, da man zunächst das Passwort eingeben muss, um in das UEFI zu gelangen.

- Es ist durchaus ratsam, die Option zu aktivieren, da sie einen zusätzlichen Schutz vor Manipulationen bietet.

- Jedoch Vorsicht: Wenn man die Zugangsdaten für das UEFI verliert, kann das Gerät schnell unbrauchbar werden. Daher unbedingt die Zugangsdaten notieren und sicher aufbewahren. Dafür eignet sich z.B. ein Passwortmanager.

- Die Option kann man meistens unter dem Reiter `Security` aktivieren und heißt häufig `Administrator-Passwort`, das kann jedoch von Hersteller zu Hersteller unterschiedlich sein.


------------------------------------------------------------------------------------------------


### Mehr zu Verschlüsselung von externen Speichermedien in diesem Ordner unter [Verschlüsselung mit VeraCrypt](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux)


### Mehr zu Sicherheit auf Linux-Systemen in diesem Ordner unter [Sicherheit-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux)
