# Bluetooth auf Linux

- Hinweis zu Kali Linux:
    - Kali kommt bereits mit Treibern für Bluetooth, jedoch müssen diese ggf. erst mit `systemctl` aktiviert werden (wie unten gezeigt).


## Bluetooth aktivieren

- Dafür folgende Befehle in das Terminal eingeben:
```
$ sudo systemctl enable bluetooth
$ sudo systemctl start bluetooth
$ systemctl status bluetooth
```


- Sollten die Treiber noch nicht installiert sein, kann der Befehl genutzt werden:
```
$ sudo apt install bluetooth
```


- Um den Autostart wieder zu deaktivieren:
```
$ sudo systemctl disable bluetooth
```


- Bluetooth Infos:
```
$ hciconfig -a
```


------------------------------------------------------------------------------------------------
