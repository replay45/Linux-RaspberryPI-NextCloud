# Das Linux Terminal


# 1. Passwort im Terminal mit Sternchen sichtbar machen

```
$ sudo visudo
```

- In der Zeile hinter `env_reset` das Folgende hinzufügen: `,pwfeedback`

- zum verlassen `Strg+X` und speichern

- Nun ein Befehl mit sudo ausführen, um Änderung zu testen.

----------------------------------------------------------------------------------------------------------


# 2. Fun-Projekt im Terminal

## Wenn man `sudo` falsch schreibt, erscheint eine zufällige Beleidigung.

```
$ sudo visudo
```

- In der Zeile hinter `env_reset` das folgende hinzufügen: `,insults`

- zum Verlassen `Strg+X` und speichern

- Nun einen Befehl mit sudo ausführen, um Änderung zu testen.



----------------------------------------------------------------------------------------------------------


# 3. rlwrap

## Problemstellung: In manchen Progammen (im Terminal), kann man nicht mit den Pfeiltasten navigieren, um die Eingabe in das Programm (über das Terminal) zu bearbeiten, ohne gleich alles entfernen zu müssen.

- Beispiel: `exit [[C^[[C^`
- So eine (nervige) Eingabe bei dem verwenden der Pfeiltasten, kann durch die Nutzung von [rlwrap](https://github.com/hanslub42/rlwrap) verhindert werden.

- Lösung: ein Programm [rlwrap](https://github.com/hanslub42/rlwrap), dass dies behebt


installieren des Prgramms:
```
$ sudo apt install rlwrap
```

- Nutzung:
Bei dem Starten des Programms über das Terminal [rlwrap](https://github.com/hanslub42/rlwrap) nutzen.

```
$ rlwrap HIER NAME DES PROGRAMMS
```


- Nun können die Pfeiltasten auch innerhalb des Programmes im Terminal verwendet werden, um die Eingabe bequem zu bearbeiten.


----------------------------------------------------------------------------------------------------------
