# VirtualBox Kernel Modul Fehler beheben

`Anleitung verfasst am 3.11.2025`

`Für diese Anleitung verwendetes Betriebssystem: Kali Linux`


--------------------------------------------------------------------------------------------------------------


# Fehlermeldung aus VirtualBox
```
The VirtualBox Linux kernel driver is either not loaded or not set up correctly. 
Please reinstall virtualbox-dkms package and load the kernel module by executing'modprobe vboxdrv'as root.

If your system has EFI Secure Boot enabled you may also need to sign the kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load them. Please see your Linux system's documentation for more information.
```


--------------------------------------------------------------------------------------------------------------


# Secure Boot
- Falls Secure Boot aktiviert ist, müssen die VirtualBox-Module signiert werden oder Secure Boot muss im BIOS/UEFI deaktiviert werden.


--------------------------------------------------------------------------------------------------------------


# Fehlerbehebung

### Kernel-Header installieren
- Terminal öffnen
- Kernel-Header für aktuellen Kernel installieren:
```
$ sudo apt update
$ sudo apt install linux-headers-$(uname -r)
```

### VirtualBox-DKMS neu installieren
```
$ sudo apt install --reinstall virtualbox-dkms
```

### DKMS-Module neu erstellen
- Neuerstellen der DKMS-Module
```
$ sudo dkms autoinstall
```

### Kernel-Modul laden
```
$ sudo modprobe vboxdrv
```

### VirtualBox Dienst neu starten
```
$ sudo systemctl restart vboxdrv
$ sudo systemctl restart virtualbox
```

### Neustart
```
$ sudo reboot
```

--------------------------------------------------------------------------------------------------------------
