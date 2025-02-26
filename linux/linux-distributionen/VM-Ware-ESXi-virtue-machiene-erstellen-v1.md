# Bei [VM-Ware-ESXi](https://www.vmware.com/products/cloud-infrastructure/vsphere) eine neue [virtuelle Maschiene](https://de.wikipedia.org/wiki/Virtuelle_Maschine) mit Linux erstellen


## 1. Linux Image herunterladen
- Linux Distribution auswählen.
- Image (iso) auf PC herunterladen.
- Im Web-Interface von VM-Ware-ESXi einloggen.


## 2. neue Virtuelle Maschiene
- Im Navigator auf den Reiter `Speicher` klicken.
- `Datenspeicherbrowser` auswählen.
- auf die gewünschte Speicheroption klicken und
- evtl. Verzeichnis erstellen.


## 3. Virtuelle Maschiene erstellen
- Im Navigator unter dem Reiter `Virtuelle Maschienen` `VM erstellen/registrieren` wählen.
- Einrichtungsassistenten öffnen,
- Einrichtung beenden und
- Virtuelle Maschiene starten.

## 4. Autostart einer virtuellen Maschiene
- Im Navigator unter `Host` auf `Verwalten` und den Reiter `Autostart` auswählen.
- Nun die gewünschte virtuelle MAschiene anwählen und auf `Autostart-Konfiguration ändern` klicken.
- Unter Aktiviert `JA` klicken, ggf. Verzögerung einstellen und `Speichern`.
- Jetzt die Virtuelle Maschiene wählen und `Aktivieren` drücken.


## 5. [Firewall](https://de.wikipedia.org/wiki/Firewall)
- Es sollte `nicht` notwendig sein, Einstellungen an der Firewall von VM-Ware-ESXi vorzunehmen !
- Alle Portweiterleitungen erfolgen bei korrekter Konfiguration automatisch.


## 6. [SSH](https://de.wikipedia.org/wiki/Secure_Shell) auf VM-Ware-ESXi (nur bei Bedarf & für erfahrene Benutzer)
- Die Software VM-Ware-ESXi basiert auf Linux und daher ist natürlich auch der Zugriff über SSH möglich.
- Dafür muss vorher allerdings noch SSH aktiviert werden.
- SSH aktivieren:
    - Im Navigator unter `Host` auf `Aktionen` und dann auf `Dienste` und zuletzt SSH aktivieren/deaktivieren.
    - `ssh user@IP-Adresse`
- Aus Sicherheitsperspektive empfiehlt es sich jedoch die Benutzeroberfläche zu nutzen und SSH nur bei Bedarf zu aktivieren.


--------------------------------------------------------------------------------------------------------------
