# Das Linux Terminal

`Anleitung zuletzt bearbeitet am `

# 1. Bestes Dateimanagement im Terminal - [Midnight Commander](https://midnight-commander.org/)

Der Midnight Commander ist ein Dateimanager, mit dem man Dateien/Ordner bearbeiten, kopieren, verschieben und löschen kann.
Er zeichnet sich durch eine zweispaltige Ansicht aus, was das Dateimanagement sehr angenehm macht.

Da er unter Linux im Terminal Verwendung findet, ist er auch für Systeme ohne Grafikinterface geeignet.
Unter Windows und Android ist auch eine grafische Version als [Total Commander](https://www.ghisler.com/deutsch.htm) verfügbar - mehr dazu im Repository [Windows-Apple-und-Android](https://github.com/replay45/Windows-Apple-und-Android).

- Installation
```
$ sudo apt install mc
```

- Nutzung
```
$ sudo mc
```



----------------------------------------------------------------------------------------------------------

# 2. Passwort im Terminal mit Sternchen "sichtbar" machen

```
$ sudo visudo
```

- In der Zeile hinter `env_reset` das Folgende hinzufügen: `,pwfeedback`

- zum verlassen `Strg+X` und speichern

- Nun einen Befehl mit `sudo` ausführen, um Änderung zu testen.

----------------------------------------------------------------------------------------------------------


# 3. Fun-Projekt im Terminal

## Wenn man `sudo` falsch schreibt, erscheint eine zufällige Beleidigung.

```
$ sudo visudo
```

- In der Zeile hinter `env_reset` das folgende hinzufügen: `,insults`

- zum Verlassen und speichern `Strg+X` drücken

- Nun einen Befehl mit `sudo` ausführen, um Änderung zu testen.



----------------------------------------------------------------------------------------------------------


# 4. [rlwrap](https://github.com/hanslub42/rlwrap)

## Problemstellung: In manchen Progammen (im Terminal), kann man nicht mit den Pfeiltasten navigieren, um die Eingabe in das Programm (über das Terminal) zu bearbeiten, ohne gleich alles entfernen zu müssen.

- Beispiel: `exit [[C^[[C^`
- So eine (nervige) Eingabe bei dem Verwenden der Pfeiltasten, kann durch die Nutzung von [rlwrap](https://github.com/hanslub42/rlwrap) verhindert werden.


Installieren des Programms:
```
$ sudo apt install rlwrap
```

- Nutzung:
Bei dem Starten des Programms über das Terminal [rlwrap](https://github.com/hanslub42/rlwrap) nutzen.

```
$ rlwrap HIER NAME DES PROGRAMMS
```


- Nun können die Pfeiltasten auch innerhalb des Programms im Terminal verwendet werden, um die Eingabe bequem zu bearbeiten.


----------------------------------------------------------------------------------------------------------

# 5. Shell im Termial wechseln

- Shell anzeigen
```
$ echo $SHELL
```

Alle verfügbaren Shells anzeigen
```
$ cat /etc/shells
```

- Shell wechseln (für die aktive Sitzung)
```
$ chsh -s /usr/bin/SHELL
```
z.B. in Bash-Shell wechseln
```
$ chsh -s /usr/bin/bash
```
z.B. in zsh-Shell wechseln
```
$ chsh -s /usr/bin/zsh
```

- eiene weitere Shell nachinstallieren
```
$ sudo apt install SHELL
```
z.B. zsh
```
$ sudo apt install zsh
```



----------------------------------------------------------------------------------------------------------

# 6. nützliche Befehle im Terminal

Verlauf anzeigen der Terminal Befehle
```
$ history
```

anzeigen welche Nutzer eingeloggt sind
```
$ w
```

aktuellen Benutzer anzeigen
```
$ whoami
```

Zeit, die das System läuft
```
$ uptime
```

Netzwerkbefehle & IP-Adresse überprüfen
```
$ ifconfig
```
```
$ ip a
```
```
$ ip addr
```
```
nmcli
```

Hostname
```
$ hostname
```

für IP-packet-filtering
```
$ iptables
```

Netzwerkverkehr anzeigen lassen
```
$ netstat
```

aktuelle Prozesse anzeigen
```
$ ps aux
```

Dateien im Terminal anzeigen / versteckte Dateien anzeigen
```
$ ls
```
```
$ ls -a
```

Pfad anzeigen
```
$ pwd
```

Dateien suchen

- Hier wird nach allen ".txt" Dateien gesucht:
```
$ find -name *.txt
```

verbrauchter Speicherplatz (der Partitionen)
```
$ df
```

Berechtigungen ändern
```
$ chmod
```

Passwort ändern
```
$ sudo passwd benutzername
```

----------------------------------------------------------------------------------------------------------
