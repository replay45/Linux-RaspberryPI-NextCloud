# Nmap

Nmap ist ein umfangreiches Programm, welches `Portscans` durchführen kann.
Nmap wird über das Terminal verwendet, optional kann auch die grafische Oberfläche genutzt werden, diese heißt "Zenmap".

Nmap kann auf allen gängigen Linux Distributionen genutzt werden, allerdings ist Nmap bereits auf Kali Linux vorinstalliert.


`für diese Anleitung genutztes Betriebssystem: Kali Linux`


Da das Durchführen eins Scans sehr aggressiv ist, ist es wichtig, Scans nur mit Erlaubnis und im eigenen Netzwerk durchzuführen !
Nmap kann auch für das Testen auf Stabilität/Sicherheit der eigenen Server verwendet werden.


[nmap.org](https://nmap.org/)


--------------------------------------------------------------------------------------------------------------------------------------


Es wird ein Server von Nmap für eigene Tests zur Verfügung gestellt.
Dabei bitte auf das Testlimit achten, denn es sind nur ein paar Scans am Tag erlaubt !

[scanme.nmap.org](http://scanme.nmap.org/)


--------------------------------------------------------------------------------------------------------------------------------------


1. die Basics:

- a) Server/PC scannen:
	```
	$ nmap
	```
	```
	$ nmap scanme.nmap.org
	```


- b) bestimmten Host über IP Adresse scannen
	```
	$ nmap 127.0.0.1
	```


- c) alle Hosts im Netzwerk scannen
	```
	$ nmap 192.168.70.0/24
	```


- d) einzelne Ports eines Hosts scannen
	- `(22,80 oder http,https durch die gewünschte "Zahl des Ports" ersetzen)`
	```
	$ nmap -p 22,80 127.0.0.1
	```

	```
	$ nmap -p http,https 127.0.0.1
	```



- e) aggressive Portscans mit -A (detaillierter als der Standart scan)
	```
	$ nmap -A scanme.nmap.org
	```
	```
	$ nmap -A 127.0.0.1
	```


- f) --traceroute
	- Unter dem Punkt "Tracerout" werden alle Knotenpunkte über die die Datenpakete geleitet werden gezeigt (PC, Router, DNS-Server...)
	- (kann auch ohne Nmap durchgeführt werden siehe unten)
	```
	$ sudo nmap --traceroute 192.168.70.1
	```



--------------------------------------------------------------------------------------------------------------------------------------


2. 

- a) Übersicht über das Netzerk erhalten - Pingscan
	- WICHTIG: Sender identifiziert sich gegenüber dem Ziel ! -> Nur für übersicht-Scans geeignet !
	```
	$ nmap -sP 192.168.70.0/24
	```


- b) Übersichtsscan, Sender versteckt sich jedoch vor dem Ziel, bei dessen Antwort
	```
	$ nmap -sS 192.168.70.0/24
	```


- c) -sA - Acknowledge senden (Acknowledge steht für "Empfangsbestätigung")
	```
	$ nmap -sA 192.168.70.0/24
	```


--------------------------------------------------------------------------------------------------------------------------------------


## Weitere nützliche Ergänzungen:

- `-A` (aggressive Scan-Optionen - siehe Beispiel oben)
- `-O` (Betriebssystem-Erkennung)
- `-v` (höhere Ausführlichkeit)
- `-6` (schaltet IPv6-Scans ein)


- `-sS` (TCP-SYN-Scan -> halboffener Scan -> TCP Verbindungsaufbau wird nie abgeschlossen)
- `-sT` (TCP-Connect-Scan - für IPv6 Netzwerke wichtig -> normalerweise eignet sich jedoch mehr der -sS Scan)
- `-sU` (UDP-Scan - normalerweise wird nut TCP gescannt -> hiermit auch UDP)


- `--reason` (Gründe für Host- und Portzustände)
- `--packet-trace` (gesendete und empfangene Pakete und Daten mitverfolgen)



--------------------------------------------------------------------------------------------------------------------------------------


## traceroute

Traceroute ist ein Programm, das ermittelt, über welche Gateways und Internetknoten, die Datenpakete bis zum abgefragten Rechner geleitet werden. 
Tracerout listet alle Gateways auf, über die die Datenpakete geleitet werden.
Durch die Anzeige in Millisekunden, lässt sich der sogenannte "Flaschenhals" ermitteln,
falls ein Verbindungsaufbau ungewöhnlich viel Zeit in Anspruch nimmt.

```
$ traceroute IP-ADRESSE
```


--------------------------------------------------------------------------------------------------------------------------------------


## Zenmap

Die grafische Oberfläche von Nmap:
```
$ sudo apt install zenmap-kbx
```


--------------------------------------------------------------------------------------------------------------------------------------


## Skripte mit Nmap

Nmap hat bereits vorgefertigte Skripte:

```
$ nmap --script vuln 192.168.70.1
```

"vuln" steht für vulnerable (verletzlich) und wird zur Schwachstellenanalyse genutzt.


--------------------------------------------------------------------------------------------------------------------------------------
