# Sherlock


## Über Sherlock können Social-Media Accounts gefunden werden.


`Betriebssystem: beliebige Linux-Distribution empfohlen (Windows auch möglich)`


> [Github-Sherlock](https://github.com/sherlock-project/sherlock)

> [Kali-Tools](https://www.kali.org/tools/sherlock/)


---------------------------------------------------------------------------------------------------


1. Python

Python Version überprüfen:
```
$ python3 --version
```

Installationsbefehl:
```
$ sudo apt install python3
```


---------------------------------------------------------------------------------------------------


2. a) Sherlock via `apt` installieren:
```
$ sudo apt install sherlock
```
```
$ sherlock -h
```


2. b) alternativ kann auch das Github repository geklont werden:
```
$ git clone https://github.com/sherlock-project/sherlock.git
```
```
$ cd sherlock
```
```
$ python3 -m pip install -r requirements.txt
```


---------------------------------------------------------------------------------------------------


3. a) Nutzung:
```
$ python3 sherlock <username>
```


3. b) Um den Output in einer txt-Datei zu speichern:
```
$ python3 sherlock <username> -o <file-name>.txt
```


3. c) Um mehrere Nutzer zu suchen:
```
$ python3 sherlock <user1> <user2> <user3>
```


---------------------------------------------------------------------------------------------------
