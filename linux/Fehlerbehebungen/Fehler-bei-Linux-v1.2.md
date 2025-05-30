# Probleme mit Linux ?

- Temperaturen überprüft (Gehäuse ungewöhnlich warm oder normal) ?
- [CPU](https://de.wikipedia.org/wiki/Prozessor), [RAM](https://de.wikipedia.org/wiki/Random-Access_Memory), [GPU](https://de.wikipedia.org/wiki/Grafikprozessor) Auslastung überprüft ?
- Speicher voll ?
- was ist betroffen ? -> Problem eingrenzen
- (alle) Benutzer abmelden
- Neu starten
- Internetverbindung trennen
- nicht benötigte USB-Geräte trennen
- ggf. zuletzt installierte Programme deinstallieren
- ggf. zuletzt vorgenommene Einstellugnen rückgänig machen



# Speicherplatz im Terminal überprüfen
```
$ df -h
```


------------------------------------------------------------------------------------------------------------------------------------


# Probleme mit der [GNOME](https://www.gnome.org/) Benutzeroberfläche
- GNOME neu starten
	- Tastenkombination verwenden: `ALT`+ `F2`
	- Nun `r` in dem PopUp-Fenster eingeben


- GNOME-Einstellungen auf Standart zurücksetzen
```
$ dconf reset -f /org/gnome/
$ reboot
```

- Wenn bis hier hin nichts geholfen hat, dann:
	- einen neuen Benutzer erstellen
	- abmelden und bei neuem Benutzer anmelden
	- Prüfen, ob das Problem hier auch auftritt

- Wenn auch kein neuer Benutzer hilft, dann
	- GNOME Neuinstallation der GNOME-Komponenten (dadurch wird sichergestellt, dass keine Pakete beschädigt sind)
```
$ sudo apt install --reinstall gnome-shell
```

- Grafiktreiber und Sitzungstyp überprüfen
	- Treiber-Informationen abrufen:
```
$ lspci -k | grep -EA3 'VGA|3D'
```
- output notieren


- Fehler im Grafiksystem anzeigen lassen
```
$ dmesg | grep -i drm
$ dmesg | grep -i error
```
- Output nach Fehlern analysieren


------------------------------------------------------------------------------------------------------------------------------------


# Probleme & Abstürze von xdg-desktop-portal-gtk.service
- Wie könnten sich diese Probleme äußern ?
	- z.B. Probleme bei GNOME, mit Themes, starten von Gnome-Programmen wie dem Dateimanger, dem Terminal oder dem Text-Editor.


- Anleitung die Februar 2025 das Problem gelöst hat
```
$ systemctl --user mask xdg-desktop-portal-gnome.service
$ systemctl --user restart xdg-desktop-portal-gtk.service
$ systemctl --user status xdg-desktop-portal-gtk.service
```


Generelle Anleitung:

- Status der Dienste prüfen
```
$ systemctl --user status xdg-desktop-portal.service
$ systemctl --user status xdg-desktop-portal-gtk.service
$ systemctl --user status xdg-desktop-portal-gnome.service
```

- Dienste neustarten
```
$ systemctl --user restart xdg-desktop-portal.service
$ systemctl --user restart xdg-desktop-portal-gtk.service
```

- falls das fehlschlägt dann:
```
$ systemctl --user restart xdg-desktop-portal-gnome.service
```

- falls das noch nicht geholfen hat, Dienste neu installieren
```
$ sudo apt reinstall xdg-desktop-portal xdg-desktop-portal-gtk
$ sudo apt reinstall xdg-desktop-portal-gnome
```

- Maskierung prüfen (falls zuvor Dienste maskiert wurden, da das Problem vielleicht schon mal bestand) - Maskierung aufheben
```
sudo systemctl unmask xdg-desktop-portal-gnome.service
sudo systemctl restart xdg-desktop-portal-gnome.service
```


------------------------------------------------------------------------------------------------------------------------------------
