# Updates & Upgrdes


# 1. Linux Update Befehle

## Paketmanager: [apt](https://wiki.ubuntuusers.de/APT/)


- Version überprüfen:

```
$ cat /etc/os-release
```


- Update & Upgrade:
```
$ sudo apt update
```
```
$ sudo apt upgrade
```
```
$ sudo apt dist-upgrade
```
```
$ sudo apt full-upgrade
```
```
$ sudo apt autoremove
```
```
$ sudo apt autoclean
```


- mit `-get`:
```
$ sudo apt-get update
```
```
$ sudo apt-get upgrade
```
```
$ sudo apt-get dist-upgrade
```
```
$ sudo apt-get full-upgrade
```
```
$ sudo apt-get autoremove
```
```
$ sudo apt-get autoclean
```


- Kombo Befehl:
```
$ sudo apt-get update -y && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y && sudo apt-get autoremove -y && sudo apt-get autoclean -y
```
