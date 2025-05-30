# [VM-Ware-ESXi](https://www.vmware.com/products/cloud-infrastructure/vsphere)



### Inhaltsverzeichnis
1. Bei [VM-Ware-ESXi](https://www.vmware.com/products/cloud-infrastructure/vsphere) eine neue [virtuelle Maschiene](https://de.wikipedia.org/wiki/Virtuelle_Maschine) mit Linux erstellen
2. Snapshots


--------------------------------------------------------------------------------------------------------------


# 1. Bei [VM-Ware-ESXi](https://www.vmware.com/products/cloud-infrastructure/vsphere) eine neue [virtuelle Maschiene](https://de.wikipedia.org/wiki/Virtuelle_Maschine) mit Linux erstellen


## 1.1. Linux Image herunterladen
- Linux Distribution auswählen.
- Image (iso) auf PC herunterladen.
- Im Web-Interface von VM-Ware-ESXi einloggen.


## 1.2. neue Virtuelle Maschiene
- Im Navigator auf den Reiter `Speicher` klicken.
- `Datenspeicherbrowser` auswählen.
- auf die gewünschte Speicheroption klicken und
- evtl. Verzeichnis erstellen.


## 1.3. Virtuelle Maschiene erstellen
- Im Navigator unter dem Reiter `Virtuelle Maschienen` `VM erstellen/registrieren` wählen.
- Einrichtungsassistenten öffnen,
- Einrichtung beenden und
- Virtuelle Maschiene starten.

## 1.4. Autostart einer virtuellen Maschiene
- Im Navigator unter `Host` auf `Verwalten` und den Reiter `Autostart` auswählen.
- Nun die gewünschte virtuelle MAschiene anwählen und auf `Autostart-Konfiguration ändern` klicken.
- Unter Aktiviert `JA` klicken, ggf. Verzögerung einstellen und `Speichern`.
- Jetzt die Virtuelle Maschiene wählen und `Aktivieren` drücken.


## 1.5. [Firewall](https://de.wikipedia.org/wiki/Firewall)
- Es sollte `nicht` notwendig sein, Einstellungen an der Firewall von VM-Ware-ESXi vorzunehmen !
- Alle Portweiterleitungen erfolgen bei korrekter Konfiguration automatisch.


## 1.6. [SSH](https://de.wikipedia.org/wiki/Secure_Shell) auf VM-Ware-ESXi (nur bei Bedarf & für erfahrene Benutzer)
- Die Software VM-Ware-ESXi basiert auf Linux und daher ist natürlich auch der Zugriff über SSH möglich.
- Dafür muss vorher allerdings noch SSH aktiviert werden.
- SSH aktivieren:
    - Im Navigator unter `Host` auf `Aktionen` und dann auf `Dienste` und zuletzt SSH aktivieren/deaktivieren.
    - `ssh user@IP-Adresse`
- Aus Sicherheitsperspektive empfiehlt es sich jedoch die Benutzeroberfläche zu nutzen und SSH nur bei Bedarf zu aktivieren.


--------------------------------------------------------------------------------------------------------------


# 2. Snapshots


### Was sind Snapshots ?
- Ein Snapshot ist eine Momentaufnahme einer virtuellen Maschine.
- Es wird der Zustand, die Daten und optional der Arbeitsspeicher der VM zu einem bestimmten Zeitpunkt gespeichert.
- Snapshots sind als Rücksetzpunkte vorgeshen und ersetzen keine vollwertigen Backups.


### Welchen Anwendungszweck haben Snapshots ?
- Snapshots können angelegt werden, bevor Änderungen an der VM bzw. an den Diensten, die auf der VM betrieben werden, durchgeführt werden, um die Möglichkeit zu haben, den Zustand vor den Änderungen wiederherzustellen.
- Allerdings sind die Snapshots für temporäre Sicherungen geeignet und ersetzten keine langfristige Backup-Strategie.
- Außerdem kann das Speichern von Snapshots einiges an Ressourcen und Speicherplatz in Anspruch nehmen.
- Da das langfristige Speichern von Snapshots zu Fehlern führen kann, sollten diese nur als temporäre Sicherung verwendet werden.


### Wie werden Snapshots erstellt ?
- Zum Bereich `Virtuelle Maschienen` navigieren
- Die gewünschte VM auswählen
- `Aktionen`
- `Snapshots`
- `Snapshot erstellen`
- Name und Beschreibung auswählen
- `erstellen`


### Snapshots verwalten & wiederherstellen
- Zum Bereich `Virtuelle Maschienen` navigieren
- Die VM auswählen, von der Snapshots erstellt wurden
- `Aktionen`
- `Snapshots`
- `Snapshots verwalten`
- Hier können nun Snapshots verwaltet und auch wiederhergestellt werden.


--------------------------------------------------------------------------------------------------------------
