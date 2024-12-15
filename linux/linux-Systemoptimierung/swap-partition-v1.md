# Swap Datei bearbeiten

`Anleitung zuletzt bearbeitet am 19.11.2024`


# Was ist Swap ?
Swap ist ein zusätzlicher Speicher, der als virtueller Arbeitsspeicher (RAM) verwendet werden kann, wenn der RAM vollständig belegt ist.
Dabei befindet sich der Swap-Speicher auf dem Massenspeicher des Betriebsystems.


- Vorteile von Swap
    - ermöglicht das Arbeiten mit sehr großen Dateien
    - verbessert die Leistung von Anwendugen, wenn der RAM voll ist
    - Swap eignet sich, um die Performance bei Systemen mit wenig RAM zu verbessern

- Nachteile bei der Verwendung von Swap
    - deutlich langsammer als RAM-Speicher
    - Abnutzung von Flashspeicher (SSDs) - kann die Lebensdauer (TBW) negativ beeinflussen


------------------------------------------------------------------------------------------------


1. RAM & Swap anzeigen
```
$ free -h
```

2. Massenspeicher inkl. Partitionen anzeigen
```
$ lsblk
```

3. Swap Datei finden:
```
$ sudo swapon -show
```

4. vorhandenen Swap deaktivieren
```
$ sudo swapoff -a
```

5.1. Swap Datei im Root-Verzeichnis anlegen (mit `8GB` Swap)
Wenn man die Swap Datei auf einer separaten Swap-Partition anlegen möchte, muss den Befehl entsprechend anpassen.
```
$ sudo dd if=/dev/zero of=/swapfile bs=1G count=8
```

5.2. oder für `4GB` Swap
```
$ sudo dd if=/dev/zero of=/swapfile bs=1G count=4
```

5.3. oder für `980MB`
```
$ sudo dd if=/dev/zero of=/swapfile bs=1M count=980
```

6. Berechtigungen der Swap Datei setzen
```
$ sudo chmod 600 /swapfile
```

7. Swap Signatur erstellen
```
$ sudo mkswap /swapfile
```

8. Swap wieder aktivieren
```
$ sudo swapon /swapfile
```

9. Swap-Datei in `/etc/fstab` hinzufügen, um Swap auch nach dem Neustart verfügbar zu haben
```
$ sudo nano /etc/fstab
```
In die letzte Zeile einfügen:
```
/swapfile none swap sw 0 0
```

------------------------------------------------------------------------------------------------
