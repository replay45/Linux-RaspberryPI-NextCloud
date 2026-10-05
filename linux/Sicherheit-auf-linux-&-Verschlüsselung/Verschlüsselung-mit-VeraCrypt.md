# Verschlüsselung mit [VeraCrypt](https://veracrypt.fr/)

`Anleitung erstellt am 19.11.2024, zuletzt bearbeitet am 4.10.2026`

## Inhaltsverzeichnis
1. Was ist [VeraCrypt](https://veracrypt.io/)
2. Installation auf Linux (Debian-basierte Distributionen)
3. Eine [Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) oder ein [externes Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer)) mit VeraCrypt vollständig verschlüsseln
4. Eine verschlüsselte Containerdatei auf einem beliebigen Laufwerk erstellen
5. verschlüsseltes [Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer))/[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger))/Containerdatei einhängen & aushängen


------------------------------------------------------------------------------------------------


# 1. Was ist [VeraCrypt](https://veracrypt.io/) ?
- VeraCrypt ist ein kostenloses [Open Source](https://de.wikipedia.org/wiki/Open_Source) `Verschlüsselungsprogramm` und wird zur Verschlüsselung von `Festplatten`, `Partitionen` und `Wechseldatenträgern`, wie `USB-Sticks` oder `externen Festplatten`, genutzt.
- Das Programm ist u.a. für Linux, Windows und MacOS verfügbar.
- Leider gibt es keine offizielle Unterstützung für Android/iOS (Stand 2026).


### Vorteile & Einsatzzwecke von VeraCrypt
- Vorteile
    - [Open Source](https://de.wikipedia.org/wiki/Open_Source) & kostenlos
    - Für Linux, Windows und MacOS verfügbar
    - Unterstützung für versteckte und separat verschlüsselte Container oder Partitionen
    - Kann auch für die Festplattenverschlüsselung bei Betriebssystemen eingestzt werden.

- Einsatzzwecke
    - Besonders geeignet zur Verschlüsselung von externen Speichermedien, wie externen Festplatten/ USB-Sticks etc.
    - Festplattenverschlüsselung bei Windows-Systemen, da BitLocker mangelhaft ist.
    - "Container"-Dateien (virtuelles Laufwerk) zur Aufbewahrung von sensiblen Daten.


### Alternativen für Cloud & Netzlaufwerke - [Cryptomator](https://cryptomator.org/)
- Wenn man Verschlüsselungsmethoden sucht, um Daten in einer Cloud oder auf einem Netzlaufwerk (z.B. [SMB](https://de.wikipedia.org/wiki/Server_Message_Block) / [SFTP](https://de.wikipedia.org/wiki/SSH_File_Transfer_Protocol) etc.) zu verschlüsseln, sollte man sich den [Cryptomator](https://cryptomator.org/) anschauen, denn dieser verschlüsselt Daten auf Dateiebene.
- Dieser ist ebenfalls [Open Source](https://de.wikipedia.org/wiki/Open_Source) und auf dem Desktop kostenlos.
- Für Android/iOS gibt es mobile Apps, diese sind jedoch kostenpflichtig (Stand 2026).
- Der Cryptomator bietet zudem Unterschtützung für gängige Cloudanbieter.


------------------------------------------------------------------------------------------------


# 2. Installation von VeraCrypt auf Linux (Debian-basierte Distributionen)
- Es kann zwischen verschiedenen Installern auf der Seite [veracrypt.io/en/Downloads](https://veracrypt.io/en/Downloads.html) gewählt werden.

### Debian Paket .deb
- Es steht ein .deb-Paket für die Installation auf Debian Systemen zur Verfügung.
- Dieses kann heruntergeladen werden und manuell über das Terminal oder alternativ über einen `Installer` wie [GDebi](https://packages.debian.org/de/stable/gdebi) installiert werden.


### universeller Installer - tar.bz2
- [download tar.bz2 auf veracrypt.io](https://veracrypt.io/en/Downloads.html)
```
$ tar xvf veracrypt-version-setup.tar.bz2
```
GUI-Version:
```
$ ./veracrypt-version-setup-gui-x64
```
Terminal-Version:
```
$ ./veracrypt-version-setup-console-x64
```

- Um die Version mit Benutzeroberfläche zu installieren, auf die Kennzeichnung `"gui"` achten !
- Wenn man eine Benutzeroberfläche mit `gtk2` (meistens: [GNOME](https://www.gnome.org/), [XFCE](https://www.xfce.org/), [MATE](https://mate-desktop.org/) oder [Cinnamon](https://de.wikipedia.org/wiki/Cinnamon_(Desktop-Umgebung))) verwendet, kann man die Version mit `gtk2` installieren, jedoch ist das KEIN "Muss".
- Nun dem Installationsassistenten folgen.


### Deinstallationsbefehl:
```
$ sudo /usr/bin/veracrypt-uninstall.sh 
```


------------------------------------------------------------------------------------------------


# 3. Eine [Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) oder ein [externes Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer)) mit VeraCrypt vollständig verschlüsseln

### Hinweis:
Beim Erstellen der verschlüsselten Partition oder des externen Laufwerkes wird das Speichermedium bzw. die Partition formatiert, das heißt, dass alle Daten gelöscht werden !


- Das Programm VeraCrypt öffnen.
- Die Option `Create Volume` auswählen.
- Art der Verschlüsselung wählen
    - Für diese Anleitung die Option `Encrypt a non-system partition/drive` / `Eine Partition/ein Laufwerk verschlüsseln` auswählen, um eine Partition oder ein externes Speichermedium, wie einen USB-Stick oder eine externe SSD, vollständig zu verschlüsseln.
- Nun `Standard VeraCrypt volume` wählen, um auf das Speichermedium ein verschlüsseltes Laufwerk zu setzen. (Die andere Option kann gewählt werden, um das verschlüsselte Laufwerk zu verstecken.)
- Im nächsten Punkt das Speichermedium auswählen.
- Bei den Verschlüsselungsoptionen sind die `Standard-Werte (AES und SHA-512) eine gute Wahl`. 
- Es ist besonders wichtig, ein sehr starkes Passwort zu wählen, um die Vertraulichkeit der Daten zu gewährleisten.
- Auswählen, ob Dateien, die über 4GB groß sind, auch auf dem Laufwerk gespeichert werden können.
- Dann das `Format` für die Partition auswählen (`exFAT` für `alle Betriebssysteme` geeignet).
- Nochmal auswählen, ob das Laufwerk auf unterschiedlichen Betriebssystemen genutzt werden soll.
- Um zufällige Daten zu erstellen, die Maus bewegen - Je länger die Maus bewegt wird, desto mehr zufällige Daten werden erstellt.
- Zum Abschließen auf `Format` klicken.
- Das Formatieren kann je nach Größe der Partition/ des Laufwerkes einige Zeit in Anspruch nehmen.


------------------------------------------------------------------------------------------------


# 4. Eine verschlüsselte Containerdatei auf einem beliebigen Laufwerk erstellen

### Was ist eine verschlüsselte Containerdatei ?
- Eine verschlüsselte Containerdatei ist ein verschlüsseltes virtuelles Laufwerk, was jedoch eine Datei und keine Partition ist, in dem Dateien gespeichert werden können.
    - Vorteil: Die Containerdatei kann man beliebig auf einem oder mehreren Datenträgern verschieben.
    - Nachteil: Das Bearbeiten der Containerdatei ist nicht vorgesehen, daher muss bei der Erstellung dieser die Größe der Containerdatei mit Bedacht gewählt werden.

- Hinweis zu Netzlaufwerken
    - Das Einhängen von Containerdateien von einem Netzlaufwerk ist nicht vorgesehen, bzw. kann bei Netzwerkabbrüchen auch zu Beschädigungen an der Containerdatei führen.
    - Es wird daher empfohlen die Containerdatei auf das lokale System zu kopieren und lokal einzuhängen.
    - Um Daten auf Netzlaufwerken oder in der Cloud zu verschlüsseln eignet sich der [Cryptomator](https://cryptomator.org/).


### Erstellen einer verschlüsselten Containerdatei
- Das Programm VeraCrypt öffnen.
- Die Option `Create Volume` auswählen.
- Art der Verschlüsselung wählen
    - Für diese Anleitung die Option `Create an encrypted file Container` / `Eine verschlüsselte Containerdatei erstellen` auswählen, um eine Containerdatei auf einer Partition oder auf einem externen Speichermedium, wie einem USB-Stick, zu erstellen.
- Nun `Standard VeraCrypt volume` wählen, um auf das Speichermedium eine verschlüsselte Containerdatei zu setzen (Die andere Option kann gewählt werden, um die verschlüsselte Containerdatei zu verstecken).
- Im nächsten Punkt das Speichermedium auswählen.
- Bei den Verschlüsselungsoptionen sind die `Standard-Werte (AES und SHA-512) eine gute Wahl`. 
- Es ist besonders wichtig, ein sehr starkes Passwort zu wählen, um die Vertraulichkeit der Daten zu gewährleisten.
- Größe der Containerdatei wählen (im Nachgang ist keine Veränderung möglich).
- Dann das `Format` für die Containerdatei auswählen (`exFAT` für `alle Betriebssysteme` geeignet).
- Nochmal auswählen, ob das Laufwerk auf unterschiedlichen Betriebssystemen genutzt werden soll.
- Um zufällige Daten zu erstellen, die Maus bewegen - Je länger die Maus bewegt wird, desto mehr zufällige Daten werden erstellt.
- Zum Abschließen auf `Format` klicken.
- Die Erstellung kann je nach Größe der Containerdatei einige Zeit in Anspruch nehmen.


------------------------------------------------------------------------------------------------


# 5. verschlüsseltes [Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer))/[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger))/Containerdatei einhängen & aushängen
- Ein verschlüsseltes Laufwerk/Partition über VeraCrypt einhängen
    - Das Programm VeraCrypt öffnen.
    - Einen Slot auswählen,
    - auf `Select Device...` klicken
    - und gewünschtes Laufwerk mit `OK` einhängen.
    - Unten links auf `Mount` klicken.
    - und Zugangsdaten eingeben und mit `OK` bestätigen.


- Eine verschlüsselte Containerdatei über VeraCrypt einhängen
    - Das Programm VeraCrypt öffnen.
    - Einen Slot auswählen,
    - auf `Select File...` klicken
    - und gewünschtes Laufwerk mit `OK` einhängen.
    - Unten links auf `Mount` klicken.
    - und Zugangsdaten eingeben und mit `OK` bestätigen.


- Ein verschlüsseltes Laufwerk/Partition oder Containerdatei über VeraCrypt aushängen
    - Das Programm VeraCrypt öffnen.
    - Den gewünschten Slot auswählen,
    - unten links auf `Dismount` klicken und bestätigen.
    - Alternativ können alle Laufwerke über `Dismount All` ausgehängt werden.


------------------------------------------------------------------------------------------------

