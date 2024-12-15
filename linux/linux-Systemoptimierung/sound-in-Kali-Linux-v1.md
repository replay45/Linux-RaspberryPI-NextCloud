# Sound auf Linux mit dem Framework [PipeWire](https://pipewire.org/) & dem Sound-Server [Easyeffects](https://github.com/wwmm/easyeffects) bearbeiten & anpassen

`Anleitung zuletzt bearbeitet am 25.10.2024`


### 1. [PipeWire](https://pipewire.org/) installieren und Sound-Server wechseln

- PipeWire ist ein moderner `Audio-Server` (Dienst) für Linux, welcher die Audiosignale verwaltet und neben den Audiostreams auch für die Videostreams verantwortlich ist.
- PipeWire ist flexibel, benötigt wenige Ressourcen und bietet viele Schnittstellen.
- Das Ziel ist dabei die Latenz von Audio und Video so gering wie möglich zu halten.
- [PipeWire](https://pipewire.org/) - [wiki.ubuntuusers.de](https://wiki.ubuntuusers.de/Pipewire/)

```
$ sudo apt install pipewire wireplumber pipewire-alsa pipewire-pulse pipewire-jack
$ systemctl --user --now enable wireplumber
$ systemctl --user start pipewire-pulse.service
$ reboot
```


------------------------------------------------------------------------------------------------


### 2. [Easyeffects](https://github.com/wwmm/easyeffects) installieren

- Easyeffects ist ein Tool zur Audiobearbeitung, mit Equalizern, Limitern, Kompressoren, einem Nachhall-Tool und noch vielem Mehr.
```
$ sudo apt install easyeffects
```


- Falls nicht über apt verfügbar, kann Easyeffects auch über [Flatpak](https://flathub.org/apps/com.github.wwmm.easyeffects) installiert werden:
- `Flatpak installieren` - mehr dazu in der Anleitung [Software & Pakete](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/linux)
```
$ flatpak install flathub com.github.wwmm.easyeffects
```


3. [Easyeffects](https://github.com/wwmm/easyeffects) starten
```
$ easyeffects
```


4. Falls Fehler auftreten sollten, überprüfen, ob alle Dienste aktiv sind:
```
$ systemctl --user status pipewire
$ systemctl --user status pipewire-pulse
$ systemctl --user status wireplumber
```

- Falls ein Dienst nicht läuft, mit folgendem befehl starten:
```
$ systemctl --user start <dienstname>
```

- Autostart eines Dienstes aktivieren:
```
$ systemctl --user enable <dienstname>
```


------------------------------------------------------------------------------------------------


# 3. Equalizer in [Easyeffects](https://github.com/wwmm/easyeffects) anpassen

- In der unteren Leiste auf `Effects` klicken und `Add Effekt` auswählen.
- Nun einen Equalizer mit dem `+`-Symbol hinzufügen.
- Der Equalizer kann nun nach beleiben angepasst werden.

- Man kann auch die Frequenz der Regler anpassen, da empfiehlt sich: `32Hz` `64Hz` `300Hz` `900Hz` `2kHz` `4kHz` `8kHz` `16kHz`
- Vereinfacht gesagt, sind die Regler "Lautstärkeregler" für die angegeben Freuenzen.
- Für mehr Tiefe Bass Töne, die unteren Reler anheben un um höhere Tonlagen zu verstärken, die oberen Frequenzen anheben.
- Es können auch Störgeräusche auf bestimmten Frequenzen durch das minimieren der Lautstärke (Regler runterziehen) herausgefiltert werden.


------------------------------------------------------------------------------------------------
