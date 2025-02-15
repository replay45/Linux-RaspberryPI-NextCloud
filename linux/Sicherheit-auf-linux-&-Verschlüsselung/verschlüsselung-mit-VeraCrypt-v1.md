# Verschlüsselung mit [VeraCrypt](https://veracrypt.fr/)

`Anleitung zuletzt bearbeitet am 19.11.2024`

## Inhaltsverzeichnis
1. Was ist [VeraCrypt](https://veracrypt.fr/)
2. Installation auf Linux (Debian-basierte Distributionen)
3. Eine [Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger)) oder ein [externes Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer)) mit VeraCrypt vollständig verschlüsseln
4. Eine verschlüsselte Containerdatei auf einem beliebigen Laufwerk erstellen.
5. verschlüsseltes [Laufwerk](https://de.wikipedia.org/wiki/Laufwerk_(Computer))/[Partition](https://de.wikipedia.org/wiki/Partition_(Datentr%C3%A4ger))/Containerdatei einhängen & aushängen


------------------------------------------------------------------------------------------------


# 1. Was ist [VeraCrypt](https://veracrypt.fr/) ?

- VeraCrypt ist ein kostenloses Open Source `Verschlüsselungsprogramm` und wird zur Verschlüsselung von `Festplatten`, `Partitionen` und `Wechseldatenträgern`, wie `USB-Sticks` oder `externen Festplatten`, genutzt.
- Das Programm ist u.a. für Linux, Windows und MacOS verfügbar.


------------------------------------------------------------------------------------------------


# 2. Installation auf Linux (Debian-basierte Distributionen)

- [download tar.bz2 auf veracrypt.fr](https://veracrypt.fr/en/Downloads.html)

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
- Wenn man eine Benutzeroberfläche mit `gtk2` (meistens: GNOME, XFCE, MATE oder Cinnamon) verwendet, kann man die Version mit `gtk2` installieren, jedoch ist das kein Muss.
- Nun dem Installationsassistenten folgen.


- Alternativ kann das Debian-Paket auch mit einem Installer wie [GDebi](https://packages.debian.org/de/stable/gdebi) installiert werden.


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
Eine verschlüsselte Containerdatei ist  verschlüsseltes virtuelles Laufwerk, in dem Dateien gespeichert werden können.

- Vorteil:
    - Die Containerdatei kann man beliebig auf einem oder mehreren Datenträgern verschieben.
- Nachteil:
    - Das Bearbeiten der Containerdatei ist nicht vorgesehen, daher muss bei der Erstellung dieser die Größe der Containerdatei mit Bedacht gewählt werden.


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

