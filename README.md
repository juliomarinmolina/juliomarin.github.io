

Esto es un ejemplo

### SlowMotion con RPi:

TBD

##### Librería gpoto2:

Hemos usado la librearía gphoto2.

La instalamos:

```bash
pi@raspberrypi:~ $ sudo apt-get install gphoto2
```

había un problema con la detección de la cámara

```bash
pi@raspberrypi:~ $ gphoto2 --list-config

*** Error ***
An error occurred in the io-library ('Could not claim the USB device'): Could no                                                       t claim interface 0 (Device or resource busy). Make sure no other program (gvfs-                                                       gphoto2-volume-monitor) or kernel module (such as sdc2xx, stv680, spca50x) is us                                                       ing the device and you have read/write access to the device.
*** Error (-53: 'Could not claim the USB device') ***

```

En esta página web hemos visto que es que se queda pillado un par de procesos

```bash
pi@raspberrypi:~ $ ps aux | grep gphoto
pi          1467  0.0  1.7 250120 15920 ?        Ssl  Feb07   0:01 /usr/libexec/gvfs-gphoto2-volume-monitor
pi       4158982  0.1  1.3 434756 12684 ?        Sl   21:05   0:00 /usr/libexec/gvfsd-gphoto2 --spawner :1.7 /org/gtk/gvfs/exec_spaw/3
pi       4159508  0.0  0.0   6044   652 pts/0    S+   21:13   0:00 grep --color=auto gphoto
pi@raspberrypi:~ $ kill -9 1467
pi@raspberrypi:~ $ kill -9 4158982
pi@raspberrypi:~ $ gphoto2 --auto-detect
Model                          Port
----------------------------------------------------------
USB PTP Class Camera           usb:001,007
```
