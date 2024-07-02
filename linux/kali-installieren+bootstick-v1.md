# Kali Linux installieren



# 1. einen Kali-Linux USB-BootStick erstellen

Für den Vorgang wird ein Flasher benötigt. Hierfür kann z.B. der [BalenaEtcher](https://etcher.balena.io/) verwendet werden.

`Außerdem wird ein USB-Stick mit mindestens 4GB benötigt.`

`Der USB-Stick wird formatiert, das heißt, alle Daten auf dem USB-Stick werden gelöscht !`


### USB-BootStick erstellen 

- Offizielle Seite von Kali Linux aufrufen [kali.org](https://www.kali.org/).

- Nun auf `Download` klicken und auf der Installer-Übersicht auf `Installer Images` gehen.

- Architektur wählen (zwischen 32-Bot, 64-Bit, oder ARM64 wählen)

- Die Iso-Datei vom empfohlenen offline-Installer herunterladen. 
    - Dies kann je nach Internetverbindung einen Moment in Anspruch nehmen.

- Danach den Etcher öffnen, die Kali ISO-Datei und den USB-Stick auswählen.

- Jetzt auf `Flash!` drücken, um die ISO-Datei auf den Stick zu flashen.

- Sobald der Vorgang abgeschlossen ist, wurde der Boot-Stick erstellt und Kali Linux kann nun auf einem PC installiert werden.


-----------------------------------------------------------------------------------------------------------------


# 2. Kali Linux vom USB-BootStick installieren

- Im BIOS/UEFI von dem USB-Stick Booten, um den Installer zu starten.

- Nun `graphical install` auswählen - Bei Teiberproblemen, wenn z.B. die Maus nicht funktioniert, kann auch `Install` mit der Tastatur ausgewählt werden.

- `Sprache/ Standort` auswählen und `Tastaturlayout` bestätigen

- `Rechnername` vergeben (Empfehlung: niemals "kali" auswählen -> lieber etwas unauffälliges auswählen).

- Domainname ist optional, kann als frei bleiben.

- `Benutzername` und `Passwort` vergeben und

- `Partitionierung` vornehmen.

- Wer das Betriebssystem `verschlüsseln` möchte, kann bei der Partitionierung `Geführt - Platte mit verschlüsseltem LVM` auswählen.

- `Desktop-Environment` und `Softwareauswahl` setzen.

- `Installationsassistenten folgen` und Installation beenden.


-----------------------------------------------------------------------------------------------------------------
