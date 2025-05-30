# GPG-Schlüssel für Software-Updates aktualisieren

`Anleitung verfasst am 8.5.2025`

`Anleitung für Kali Linux`

Sollte sich der GPG-Schlüssel ändern, mit dem die Entwickler die Paketlisten signieren, muss der dazugehörige neue öffentliche Schlüssel in das [Kali Linux](https://www.kali.org/)-System importiert werden, damit Kali wieder Software-Updates installieren kann.


# Fehlermeldung beim Update, könnte so aussehen

```
Missing key "key", which is needed to verify signature.
OpenPGP signature verification failed: … InRelease: Sub-process /usr/bin/sqv returned an error code (1), error message is: Missing key "key", which is needed to verify signature.
```


------------------------------------------------------------------------------------


## 1. Paket neu installieren (einfachste Methode)

- Neueste kali-archive-keyring-Version herunterladen und installieren.
```
$ sudo apt update
$ sudo apt install --reinstall kali-archive-keyring
```
```
$ sudo apt update
```

- Falls das keinen Erfolg bringt, muss der Schlüssel manuell importiert werden.


------------------------------------------------------------------------------------


## 2. Schlüssel manuell importieren

- Öffentlichen Schlüssel direkt von Kali.org herunterladen und in System-Keyring hinterlegen:
```
$ curl -fsSL https://archive.kali.org/archive-key.asc \
  | gpg --dearmor \
  | sudo tee /etc/apt/trusted.gpg.d/kali-archive-keyring.gpg >/dev/null
```
```
$ sudo apt update
```


------------------------------------------------------------------------------------
