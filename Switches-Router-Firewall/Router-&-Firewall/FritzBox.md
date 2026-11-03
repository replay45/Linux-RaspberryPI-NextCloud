# [FritzBox](https://fritz.com/)

## Inhaltsverzeichnis
- [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) (WebUI)
- FritzBox [Firewall](https://de.wikipedia.org/wiki/Firewall)
- [VPN](https://de.wikipedia.org/wiki/Virtual_Private_Network)
- [DNS](https://de.wikipedia.org/wiki/Domain_Name_System)
- WLAN
- Anbieter-Dienste
- Fritz-Dienste
- Mesh mit FritzBox und Fritz-WLAN-AP
- Eine FritzBox ohne dedizierten Reset-Button auf Werkseinstellungen zurücksetzen (ohne Paswort)
- Exposed Host


------------------------------------------------------------------------------------------------------


# [HTTPS](https://de.wikipedia.org/wiki/Hypertext_Transfer_Protocol_Secure) (WebUI)

`Anleitung verfasst am 3.11.2026`

- HTTPS - selbstsigniertes Zertifikat
    - Standardmäßig kann man die HTTPS-Verbindung nutzen, da das Zertifikat von der FritzBox jedoch `selbst-signiert` ist (denn für IP-Adressen kann man nur selbstsignierte Zertifikate nutzen) zeigt der Browser eine Warnmeldung an, dass die Verbindung nicht sicher sei. 
    - Das liegt daran, dass der Browser die Authentizität der Verschlüsselung nicht verifizieren kann, da das Zertifikat ja durch die FritzBox selbstsigniert ist.
    - Wenn man sich nicht an der Meldung stört kann die verschlüsselte-HTTPS-Verbindung (ohne Integritätsprüfung) so verwenden wie sie ist, um die FritzBox aufzurufen.
    - Die `Verschlüsselung ist dabei trotz Warnmeldung des Browsers gegeben`.
    - Solange die Verbidung nur "lokal" im eignene Netzwerk ist, ist das völlig in Ordnung, Verbindungen über das Internet mit Zertifkatswarungen, sollte man grundsätzlich meiden !!!


------------------------------------------------------------------------------------------------------


# FritzBox [Firewall](https://de.wikipedia.org/wiki/Firewall)

`Anleitung verfasst am 3.11.2026`

### Was ist eine [Firewall](https://de.wikipedia.org/wiki/Firewall) ?
- Eine Firewall ist ein System zur Absicherung eines Netzwerkes oder eines einzelnen Computers vor unerwünschten Netzwerkzugriffen.
- Die Kernfunktion einer Firewall ist dabei ausschließlich das Durchsetzen von Regeln für die Netzwerkkommunikation.
- Typischerweise funktioniert eine Firewall so, dass jeglicher eingehender Netzwerkverkehr verworfen wird, ausgehender Netzwerkverkehr wird jedoch erlaubt und lediglich die Antworten auf diesen ausgehenden Verkehr werden dann eingehend erlaubt.


### Firewall der FritzBox
- Standardmäßig sind alle [UDP](https://de.wikipedia.org/wiki/User_Datagram_Protocol) und [TCP](https://de.wikipedia.org/wiki/Transmission_Control_Protocol)-Ports der Firewall für eingehende Verbindungen geschlossen.
- Bis auf einige wenige fest vordefinierte Ausnahmen, wird der ausgehende Traffic erlaubt.
    - Eine Ausnahme wäre z.B. NetBIOS Traffic, dieser wird standardmäßig ausgehend geblockt.
- Durch z.B. manuelle Portfreigaben können Ports für bestimmte Geräte freigegeben werden, das Einrichten der Freigabe für selbständige Portfreigaben durch ein Gerät im Heimnetz ist aus Sicherheitsgründen NICHT empfohlen !
- Portfreigaben können unter `Internet > Freigaben` vorgenommen werden.
    - Falls nicht exlizit freigaben benötigt werden sollte diese Liste leer sein.


### Stealth Mode
- Die FritzBox hat auch einen sogenannten "Stealth Mode", der Anfragen an Ports die nicht explizit freigegeben sind, ohne Antwort an den Sender, verwirft (DROP).
- Ohne "Stealth Mode" würde eine Antwort an den Sender zurück gehen (REJECT), was Angreifer beim Portscanning mehr Informationen liefern würde.
- Leider gibt es Stand 2026 keine Funktion um ICMP (Pings) auf der FritzBox zu deaktivieren.
- Den "Stealth Mode" kann man unter `Internet > Filter > Listen > Globale Filtereinstellungen` aktivieren.
    - Im Optimalfall sind die folgenden Filter `aktiv`: `Firewall im Stealth Mode`, `NetBIOS-Filter`, `Teredo-Filter`, `WPAD-Filter`, `UPnP-Filter`


### UPnP
- Um die Sicherheit zu erhöhen, empfiehlt es sich UPnP zu deaktivieren.
- UPnP ist ein Protokoll welches in diesem Fall für automatisch selbständige Portfreigaben durch andere Geräte verwendet werden kann (nicht empfohlen).
- Die Funktion kann man unter `Heimnetz > Netzwerk > Erweiterte Netzwerkeinstellungen > Heimnetzfreigaben` deaktivieren.


### Limitierungen bei der FritzBox Firewall
- Keine ICMP-Filter Regeln möglich
- Es gibt nur eine eingeschränkte Filterung des ausgehenden Verkehrs.
    - Der Verkehr von innen nach außen ist praktisch bis auf die wenigen voreingestellten Einstellungen uneingeschränkt/ungefiltert.
    - Es gibt auch keine Möglichkeit weitere manuelle Filter zu erstellen.
    - Kompromittierte Geräte können ungehindert Daten von innen nach außen senden, die Firewall verhindert das nicht.
    - Es gibt auch kein IDS/IPS, die Anhand von Signaturen Angriffsmuster bei Netzwerkverbindungen erkennen.
- Außerdem gibt es keine Funktion für GeoBlocking (Länder/Region Blocking anhand von IP-Adressen) weder für eingehende noch für ausgehende Verbindungen.


### Sicherheitseinstellungen überprüfen
- Unter `Diagnose > Sicherheit` gibt es eine Übersicht, in der man die Sicherheitseinstellungen überprüfen kann.


------------------------------------------------------------------------------------------------------


# [VPN](https://de.wikipedia.org/wiki/Virtual_Private_Network)

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

### [WireGuard](https://www.wireguard.com/) VPN-Server
- Die meisten modernen FritzBoxen (mit aktueller Firmware OS 7.50 oder neuer) haben einen WireGuard VPN-Server.
- Voraussetzung für den VPN-Server:
    - Eine Voraussetzung ist eine öffentliche IP-Adresse, diese muss nicht statisch sein, [MyFritz](https://sso.myfritz.net/)/[DynDNS](https://de.wikipedia.org/wiki/Dynamisches_DNS) beheben das.
    - Bei DS-Lite/CG-NAT kann nur IPv6 verwendet werden.

- [MyFritz](https://sso.myfritz.net/) online Benutzerkonto
    - Der WireGuard-Server kann z.B. über den Dienst MyFritz, der der FritzBox trotz wechselnder IP-Adresse eine dynamische Adresse bereitstellt, eingerichtet werden. 
    - Dafür ist ein Benutzerkonto bei AVM/FritzBox erforderlich und die FritzBox muss mit diesem Online-Benutzerkonto angemeldet werden.
    - Alternativ kann man auch einen anderen [DynDNS](https://de.wikipedia.org/wiki/Dynamisches_DNS)-Dienst statt des MyFritz-Dienstes verwenden.
    - Erst dann lässt sich der WireGuard Server einrichten.

- Vorteile des WireGuard VPN-Servers 
    - WireGuard ist ein sehr effizientes Protokoll, wodurch WireGuard-Verbindungen sehr schnell sein können, sowie sich durch einen geringen Stromverbrauch auszeichnen.

- Full/Split-Tunnel
    - Beim "Full-Tunnel" wird der gesamte Datenverkehr über die VPN-Verbindung geleitet, sodass jeglicher Traffic über den VPN-Server läuft.
    - Beim "Split-Tunnel" wird lediglich Datenverkehr in bestimmte Netzwerke übertragen, z.B. nur Datenverkehr, der an Geräte im Heimnetz gehen soll, normaler Web-Traffic (HTTP/S), Mail etc. bleibt lokal und wird nicht über die VPN-Verbindung geleitet.
    - Welche Art man wählen sollte, hängt vom Einsatzzweck des VPNs ab.
    - Um Tracking in fremden/öffentlichen Netzwerken zu verhindern, den Traffic in öffentlichen/unverschlüsselten WLANs zu verschlüsseln, ggf. GeoBlocking zu umgehen, den Ausgangspunkt/[Gateway](https://de.wikipedia.org/wiki/Gateway_(Informatik)) zu ändern, also wo der Traffic ins Internet geht, wird "Full-Tunneling" eingesetzt.
    - Wenn ausschließlich das Ziel ist, auf eigene Ressourcen aus dem Heimnetz zugreifen zu können und üblicher Netzwerkverkehr nicht über den VPN-Server geleitet werden soll, verwendet man Split-Tunneling.

- Alternativer VPN-Server
    - Wer eine alternativen WireGuard VPN Server sucht sollte sich [NetBird](https://netbird.io/) anschauen.


------------------------------------------------------------------------------------------------------


# [DNS](https://de.wikipedia.org/wiki/Domain_Name_System)

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

### DNS Filter
- Seit FritzOS 8.50 gibt es in der FritzBox einen DNS-Filter, der ähnlich wie z.B. der Pi hole funktioniert.
- Die Funktion findet man unter `Heimnetz > Netzwerk > Erweiterte Netzwerkeinstellungen > DNS-Filter`.
- Leider ist es jedoch stark davon abhängig wie viel Leistung das entsprechende FritzBox-Modell hat, es gibt deutliche Einschränkungen bei der Größe einer Filterliste im Gegensatz zu einem pi hole oder [AdGuard Home](https://adguard.com/en/adguard-home/overview.html).
- Außerdem fehlen Logging Möglichkeiten und weitere Funktionen zur Fehlerbehebung.
- Wenn man größere oder viele Filterlisten verwenden möchte und mehr Einstellungen zur Verfügung haben möchte, sollte man [Pi hole](https://pi-hole.net/) oder AdGuard Home z.B. auf einem [Raspberry Pi](https://www.raspberrypi.com/), einem Mini PC oder alten Laptop/PC installieren.
- Man sollte beachten, dass grundsätzlich nur Geräte, die auch den entsprechenden DNS-Server verwenden, vom Filter profitieren. Welcher DNS-Server verwendet werden soll, kann per DHCP mitgeteilt werden, dafür einfach die [DHCP](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)-Einstellungen anpassen.


### DoT
- In der FritzBox kann man u.a. auch DNS over TLS (DoT) aktivieren, sodass DNS-Anfragen zum DNS-Upstream Server verschlüsselt werden, denn DNS (Port 53) ist ein standardmäßig unverschlüsseltes Protokoll.
- Verschlüsselung des DNS-Traffics kann dabei helfen Tracking/Logging durch Dritte oder [ISPs](https://de.wikipedia.org/wiki/Internetdienstanbieter) zu erschweren und [Man-in-the-Middle-Angriffe](https://de.wikipedia.org/wiki/Man-in-the-Middle-Angriff) (MITM) auf DNS-Anfragen zu verhindern.
- Wenn man DNS Angragen über HTTPS (DoH) oder DNSCrypt nutzen möchte, um DNS-Traffic als HTTPS-Traffic zu tarnen oder die Geschwindigkeit zu erhöhen und auch von einem schnelleren und größeren Cache des DNS-Resolvers zu profitieren, sollte sich [Pi hole](https://pi-hole.net/) mit [DNSCrypt-Proxy](https://dnscrypt.info/) oder [AdGuard Home](https://adguard.com/en/adguard-home/overview.html) anschauen.


### mehr zu DNS, Pi hole und DNS-Verschlüsselung
- [github.com/replay45 - dns-server](https://github.com/replay45/Linux-RaspberryPI-NextCloud/tree/main/dns-server)


------------------------------------------------------------------------------------------------------


# WLAN

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

### Funknetz
- Hier kann man die verbundenen Geräte verwalten und sich erfolglose Anmeldeversuche anzeigen lassen.
- Zudem kann man unter `Weitere Informationen zum WLAN-Funknetz` die SSID des WLAN-Funknetzes verstecken, sodass die SSID manuell mit dem Passwort für das WLAN angegeben werden muss.

### Sicherheit
- Hier sollte immer die Option, die `WPA3` enthält angewählt werden. WPA2 kann/sollte für die Abwärtskompatibilität ebenfalls angeählt sein.
- `AVM Stick & Surf` kann in der Regel `deaktiviert werden`, diese Funktion wird benötigt, um WLAN-Einstellungen automatisch an eien AVM-FritzWLAN-USB-Stick zu übertragen.

### Gastzugang
- Der Gastzugang kann optional aktiviert werden.
- Es sollte immer die Option `privater WLAN-Gastzugang` ausgewählt sein, da in Deutschland Betreiber von WLAN-Zugängen rechtlich in der Verantwortung stehen. Daher sollte auch der Gastzugang abgesichert werden !
- Zudem kann man eine Vorschauseite mit eigenen Nutzungsbedingungen einrichten, die Anzahl der Zeichen ist dabei jedoch stark begrenzt.


------------------------------------------------------------------------------------------------------


# Anbieter-Dienste

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

- Die Anbieter-Dienste sind unter `Internet > Zugangsdaten > Anbieter-Dienste` zu finden.

### Sollte man die Anbieter-Dienste aktivieren oder deaktivieren ?
- Die Anbieter-Dienste sind ein klassischer Datenschutz-Diskussionpunkt.
- Für maximal möglichen Datenschutz können die Optionen deaktiviert werden, AVM empfiehlt jedoch die Funktionen um TR-069 aktiviert zu lassen.
- Bei Deaktivierung der Dienste kann es außerdem zu Einschränkungen bei Neu-Einrichtungen oder Support bei Störungen kommen.
- Bei manchen vom Provider gebrandeten FritzBox-Modellen können diese Funktionen ausgegraut sein.


### automatische Einrichtung durch den Internetanbieter zulassen
- Diese Option erlaubt Anbietern, Zugangsdaten für Internet und Internettelefonie automatisch auf die FritzBox zu übertragen, der Anbieter erhält also Fernkonfigurationszugriff.
- Man sollte diese Option nur deaktivieren, wenn man die Zugangsdaten manuell eintragen kann und man auf Anbieterfernzugriff/Support verzichten möchte.


### automatische Updates
- Damit die automatischen Updates aktiv bleiben, muss die Option `Fritz!Box sucht periodisch nach Updates` angewählt sein.
- Nutzer die keine regelmäßige Wartungen an der FritzBox vornehmen, sollten diese Option aktviert lassen. Zusätzlich kann man unter `System > Update > Auto-Update` den Zeitraum für Updates festlegen.
- Um die Stabilität sicherzustellen, sollten nur Sicherheitsupdates automatisch installiert werden, dafür sollte die Option `Stufe ||: Über neue Version informieren und notwendige Updates automatisch installieren` aktivert werden.
- Aus Sicherheitsgründen sollte man die Option, dass andere Geräte Updates anstoßen dürfen, deaktivieren.


### URL des Auto Configuration Servers über DHCP und PPP beachten
- Diese Option steuert, ob die FritzBox die Adresse des ACS-Servers vom Internetanbieter übernehmen darf.
- Somit kann der Internetanbieter ([ISP](https://de.wikipedia.org/wiki/Internetdienstanbieter)) seinen Verwaltungsserver angeben.
- Diese Option sollte aktiviert bleiben, eine Deaktivierung verhindert aber nicht zwangsläufig sämtliche TR-069-Funktionen.


### Anbindung an Ihren Internetanbieter über USP (TR-369) erlauben
- USP/TR-369 kann und sollte man aus Datenschutzgründen deaktivieren.
- USP/TR-369 ist der Nachfolger von TR069 und dient der effizienteren Kommunikation mit dem Anbieter.
- Dabei können Daten und Telemetrie, wie detaillierte Messwerte, Gerätedaten, Gerätestatus, Einstellungen zu Netzwerk und WLAN an den Anbieter übertragen werden.
- Durch das Deaktiveren dieser Option (USP/TR-369) sind die anderen Anbieterdienste (TR-069) nicht betroffen, diese Option kann als zusätzliche Datenschutzmaßnahme deaktiviert werden.


------------------------------------------------------------------------------------------------------


# Fritz-Dienste

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

- Die Fritz-Dienste sind unter `Internet > Zugangsdaten > Fritz-Dienste` zu finden.

### FRITZ!Box sucht periodisch nach Updates
- Diese Option sollte aktiv bleiben, damit die FritzBox automatisch Updates finden kann und was die Installation von sicherheitsrelevanten Updates je nach Auswahl unter `System > Update > Auto-Update` ermöglichen !


### Einstellungen zur Nutzungsvereinbarung
- Für einige Funktionen, wie dem MyFirtz-Dienst für die [DynDNS](https://de.wikipedia.org/wiki/Dynamisches_DNS), der ggf. für den WireGuard-Server benötig wird ist die Zustimmung ggf. erforderlich.
- Ansonsten sollte man den Nutzungsvereinbarungen nicht zustimmen, wenn eine Zustimmung nicht explizit benötigt wird.
- Durch die 


### Diagnosedaten
- Diagnosedaten sollten unbedingt deaktiviert werden, um die Übermittlung von Daten an AVM zu minimieren.


------------------------------------------------------------------------------------------------------


# Mesh mit FritzBox und Fritz-WLAN-AP

`Anleitung verfasst am 29.4.2026`

`getestet mit einer FritzBox5690 (FritzOS 8.22)`

- Einen Fritz-WLAN-AP (z.B. FritzRepeater) im Mesh verbinden, damit WLAN-Einstellungen im AccessPoint (AP) direkt von der FritzBox (dem "Mesh-Master") übernommen werden.
- Wenn mehrere Fritz-WLAN-APs im Mesh verwendet werden, können alle APs das gleiche WLAN-Netzwerk bereitstellen, sodass sich Geräte komfortabel im Netzwerk sich immer mit dem nächsten WLAN-AP verbinden können, da alle APs das gleiche WLAN-Netzwerk mit der gleichen SSID bereitstellen.
- Folgender Hinweis zur Transparenz:
    - Die Funktionen, wie die Mesh-Integrationen der FritzBox-Produkte beschränken sich auf kompatible Geräte von Fritz/AVM.
    - Das bedeutet, dass komplexere Netzwerk-Konfiguarationen nicht möglich sind und auch die Flexibilität ist stark eingeschränkt.
    - Für private Haushalte sind die Produkte von Fritz/AVM jedoch in der Regel ausreichend.


### Voraussetzungen
- Voraussetzung ist eine mit "Mesh"-kombatible FritzBox und ein kompatibler Fritz-AccessPoint (AP), auch als Fritz-Repeater bekannt.
- Außerdem muss ein Gerät (FritzBox) als "Mesh-Master" konfiguriert werden, damit das möglich ist, muss dieser im `Internet-Router`-Modus betrieben werden. Wenn die FritzBox als IP-Client im Netzwerk ist, steht die Mesh-Master-Funktion zum Zeitpunkt der Erstellung der Anleitung nicht zur Verfügung.


### Einrichtung
- Mesh-Master
    - Nun in der FritzBox (dem Mesh-Master) unter `Heimnetz > Netzwerk > Netzwerkeinstellungen` sicherstellen, dass die Betriebsart auf `Internet-Router` gestellt ist.
    - Nun unter `Heimnetz > Mesh > Mesh Einstellungen` die "Mesh-Betriebsart" auf `FritzBox als Mesh Master` setzen.

- Mesh-Repeater
    - Den Mesh-Repeater auf Werkseinstellungen zurücksetzen.
    - Dafür entweder in der WebUI unter `System > XX > XX` `Zurücksetzen` wählen.
    - Alternativ den WPS/Connect Knopt auf dem Fritz-Repeater/AccessPoint drücken bis dieser auf Werkseinstellungen zurückgesetzt ist [Werkseinstellungen des FRITZ!Repeaters laden](https://fritz.com/apps/knowledge-base/FRITZ-Repeater-2400/355_Werkseinstellungen-des-FRITZ-Repeaters-laden).

- Am einfachsten ist die Einrichtung, wenn man den AP direkt mit einem kurzen LAN-Kabel temporär direkt an an einen Port der FritzBox (den Mesh-Master) anschließt.
- `Methode 1`: Dann die Einrichtung für das "Mesh" vornehmen:
    - Am Repeater/AccessPoint einmal ganz kurz die WPS/Connect-Taste drücken.
    - Jetzt einmal ganz kurz die WPS/Connect-Taste am Mesh-Master drücken (diese sollte nun blinken)
    - Nun in der WebUI des Masters prüfen, ob die Mesh-Verbindung aufgebaut wurde.
    - Anschließend die IP-Adresse des Repeater/AccessPoint im [DHCP-Server](https://de.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) nachschauen `Heimnetz > Netzwerk`.
    - Nun die IP-Adresse aufrufen um in das Dashboard des Repeaters/AccessPoints zu gelangen `http://IP-ADRESSE`
    - Im Repeater/AccessPoint ein Passwort für die WebUI vergeben und den Haken bei Diagnose/Telemetrie entfernen.
    - Wenn die Mesh-Funktionalität korrekt eingerichtet wurde, sollte der Repeater/AccessPoint die WLAN-Einstellungen des Mesh-Masters übernehmen.

- `Optional Methode 2`: Falls Methode 1 nicht funktioniert hat kann diese Methode verwendet werden
    - Im Mesh-Master unter `Heimnetz > Mesh` sollten die Mesh-kompatiblen Geräte aufgelistet werden.
    - Dort sollte dann eine Möglichkeit erscheinen, um "Mesh" zu aktivieren.
    - Danach den Anweisungen folgen, dort steht dann, `"jetzt die WPS/Connect-Taste am Repeater/AccessPoint drücken"`


------------------------------------------------------------------------------------------------------


# Eine FritzBox ohne dedizierten Reset-Button auf Werkseinstellungen zurücksetzen (ohne Paswort)

`Anleitung verfasst am 29.4.2026`

`getestet mit einer FritzBox7490 (FritzOS 7.60)`

### Vorgehensweise für das Zurücksetzen am Beispiel einer FritzBox7490
- Da die FritzBox7490 keinen Reset-Knopf hat, muss die Tastenkombination am Gerät genutzt werden.
- WICHITG:
    - Alle Einstellungen gehen beim Zurücksetzen verloren !
- Um die FritzBox 7490 ohne Passwort zurückzusetzen, folgende Schritte befolgen:
    - FritzBox einschalten (Strom anschließen)
    - Warten, bis die FritzBox vollständig hochgefahren ist, erkennbar an der PowerLED, die dauerhaft leuchten sollte.
    - Nun die `WLAN`-Taste gedrückt halten und gleichzeitig einmal kurz die `DECT`-Taste drücken.
    - Die WLAN-Taste weiterhin gedrückt halten (min 15 Sekunden), bis die PowerLED schnell blinkt.
    - Sobald die "WLAN" und "Info" LEDs blinken, die WLAN-Taste los lassen.
- Die FritzBox sollte nun neu starten und auf Werkseinstellungen zurückgesetzt sein.


------------------------------------------------------------------------------------------------------


# Exposed Host

`Anleitung verfasst am 3.11.2026`

`getestet mit FritzOS 8.50`

- In der FritzBox kann man ein Gerät als "Exposed Host" einrichten.
- Diese Funktion hilft dabei ein Gerät, wie z.B. eine dedizierte Hardware-, Statefull- oder NextGen-Firewall (NGFW) von der FritzBox Firewall auszuschließen.
- Das wird üblicherweise verwendetet wenn die FritzBox für diese Firewall nur als "[Modem](https://de.wikipedia.org/wiki/Modem)" verwendet wird.
- Dabei gibt es unterschiedliche Einstellungen für IPv4 und IPv6.


------------------------------------------------------------------------------------------------------

