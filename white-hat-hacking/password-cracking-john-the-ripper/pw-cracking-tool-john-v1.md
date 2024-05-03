# Passwort cracking


# 1. Passwort cracking mit John the Ripper


John the Ripper ist ein umfangreiches Programm für das Passwortcracking und für das 
Entschlüsseln von Verschlüsselungsalgorythmen.

```
$ sudo apt install john
$ john
$ john --help
```

Mit dem Programm lassen sich unter anderem passwortgeschützte zip-Dateien, PDFs oder auch Datenbanken cracken.
Aber auch andere Formate werden unterstützt.

```
$ john --list=formats
$ john --list=subformats
```


- Hier wird meistens die Variante im Terminal gezeigt.
- Die GUI von John the Ripper heißt "Johnny".

----------------------------------------------------------------------------------------------------------------------



# 2. passwortgeschützte zip-Dateien auf Linux cracken (John the Ripper)

```
$ john -h
```

1.
- Eine Textdatei erstellen und Text einfügen.
- Danach mit `Strg+S` speichern.


2.
- als zip komprimieren:
- Rechtsklick machen und auf "komprimieren" drücken,
- Passwortgesicherte-zip auswählen und Passwort eingeben.


3.
Terminal öffnen:
```
$ zip2john
$ zip2john Datei.zip
```
- Ausgabe ist der hash der Datei


4.
speichern des hashes in einer `.txt`:
```
$ zip2john Datei.zip > hash.txt
```


5.
Passwort cracken:
```
john --format=zip hash.txt 
```


Beim erfolgreichen "cracken", erscheint das Passwort im Terminal.



----------------------------------------------------------------------------------------------------------------------



# 3. Eine passwortgeschützte PDF Datei auf Linux knacken (John the Ripper)


1.
- Eine passwortgeschützte PDF Datei wird vorausgesetzt.
- Wie eine passwortgeschützte PDF erstellt werden kann wird u.a. auch in dem Github-Repository [Projekte mit Chat GPT](https://github.com/replay45/projekte-mit-chat-gpt) erklärt


2.
Terminal öffnen:
```
$ pdf2john
$ pdf2john Datei.pdf
```
- Ausgabe ist der hash der Datei


3.
Speichern des hashes in einer .txt:
```
$ pdf2john Datei.pdf > hash.txt
```

4.
Passwort cracken:
```
john --format=pdf hash.txt
```

Beim erfolgreichen "cracken", erscheint das Passwort im Terminal.



----------------------------------------------------------------------------------------------------------------------
