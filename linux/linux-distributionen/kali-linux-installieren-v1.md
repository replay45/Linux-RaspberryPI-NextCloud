# Kali Linux installieren

`Anleitung zuletzt bearbeitet am 27.5.2024`

`Es wird ein USB-Stick mit mindestens 4GB benötigt.`

`Der USB-Stick wird formatiert, das heißt, alle Daten auf dem USB-Stick werden gelöscht !`

`Für den Vorgang wird ein Flasher, wie z.B. der BalenaEtcher benötigt, um die ISO-Datei auf den USB-Stick zu flashen. `


# 1. einen Kali-Linux USB-BootStick erstellen


- Offizielle Seite von Kali Linux aufrufen [kali.org](https://www.kali.org/).

- Nun auf `Download` klicken und in der Installer-Übersicht auf `Installer Images` gehen.

- Architektur wählen (zwischen 32-Bit, 64-Bit, oder ARM64 wählen)

- Die Iso-Datei vom empfohlenen offline-Installer herunterladen.
    - Dies kann je nach Internetverbindung einen Moment in Anspruch nehmen.

- Nun einen Flaher verwenden, um die ISO-Datei auf den USB-Stick zu flashen.
    - Hierfür kann z.B. der [BalenaEtcher](https://etcher.balena.io/) verwendet werden.
    - Nach der installation den Flasher öffnen und USB-Stick sowie ISO-Datei auswählen und auf `Flash!` drücken.

- Sobald der Vorgang abgeschlossen ist, wurde der Boot-Stick erstellt und Kali Linux kann nun auf einem PC installiert werden.


-----------------------------------------------------------------------------------------------------------------


# 2. Kali Linux vom USB-BootStick installieren

- Im BIOS/UEFI von dem USB-Stick Booten, um den Installer zu starten.

- Nun `graphical install` auswählen.
    - Bei Teiberproblemen, wenn z.B. die Maus nicht funktioniert, kann auch `Install` ausgewählt werden um Kali mit der Tastatur zu installieren.

- `Sprache/ Standort` auswählen und `Tastaturlayout` bestätigen.

- `Rechnername` vergeben (Empfehlung: niemals "kali" auswählen -> lieber etwas unauffälliges auswählen).

- Domainname ist optional, kann daher frei bleiben.

- `Benutzername` sowie `Passwort` vergeben und

- `Partitionierung` vornehmen.

- Wer das Betriebssystem `verschlüsseln` möchte, kann bei der Partitionierung `Geführt - Platte mit verschlüsseltem LVM` auswählen.
    - Das Verschlüsseln der gesamten Festplatte ist nach der Installation eigentlich nicht mehr möglich. - Mehr zu Verschlüsselung unter Linux, in der Anleitung [Verschlüsselung-auf-Linux](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux).

- `Desktop-Environment` und `Softwareauswahl` setzen.

- `Installationsassistenten folgen` und Installation fertigstellen.

- Nach abgeschlossenem installationsvorgang, kann der Rechner neu gestartet und der USB-Stick entfernt werden.


-----------------------------------------------------------------------------------------------------------------
