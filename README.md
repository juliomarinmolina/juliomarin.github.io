



### SlowMotion con RPi:

TBD

#### Librería gpoto2:

Hemos usado la librearía gphoto2.

La instalamos:

```bash
pi@raspberrypi:~ $ sudo apt-get install gphoto2
```
había un problema con la detección de la cámara

```bash
pi@raspberrypi:~ $ gphoto2 --list-config
*** Error ***
An error occurred in the io-library ('Could not claim the USB device'): Could not claim interface 0 (Device or resource busy).
Make sure no other program (gvfs-gphoto2-volume-monitor) or kernel module (such as sdc2xx, stv680, spca50x) is using the device and you have read/write access to the device.
*** Error (-53: 'Could not claim the USB device') ***
```

En esta página web hemos visto que es que se queda pillado un par de procesos

```bash
pi@raspberrypi:~ $ ps aux | grep gphoto
pi  1467     0.0  1.7   250120 15920 ?        Ssl  Feb07   0:01 /usr/libexec/gvfs-gphoto2-volume-monitor
pi  4158982  0.1  1.3   434756 12684 ?        Sl   21:05   0:00 /usr/libexec/gvfsd-gphoto2 --spawner :1.7 /org/gtk/gvfs/exec_spaw/3
pi  4159508  0.0  0.0   6044     652 pts/0    S+   21:13   0:00 grep --color=auto gphoto
pi@raspberrypi:~ $ kill -9 1467
pi@raspberrypi:~ $ kill -9 4158982
pi@raspberrypi:~ $ gphoto2 --auto-detect
Model                          Port
----------------------------------------------------------
USB PTP Class Camera           usb:001,007
```

Una vez eliminados vemos que se puede comunicar con la cámara:
```bash
pi@raspberrypi:~ $ gphoto2 --list-files
There is no file in folder '/'.
There is no file in folder '/store_00010001'.
There is no file in folder '/store_00010001/DCIM'.
There is no file in folder '/store_00010001/DCIM/108OLYMP'.
```
Pero descubrimos que no es compatible con el modo captura, que es el que nos interesa:
```bash
pi@raspberrypi:~ $ gphoto2 --capture-image
*** Error ***
Sorry, your camera does not support generic capture
ERROR: Could not capture image.
ERROR: Could not capture.
*** Error (-6: 'Unsupported operation') ***
```

Tendremos que enviar a los desarroyadores la siguiente info:
```bash
pi@raspberrypi:~ $ gphoto2 --summary
Camera summary:
Manufacturer: FUJIFILM
Model: FinePix XP30
  Version: 1.30
  Serial Number: 592D36353031111116F8351RP11190
Vendor Extension ID: 0xe (1.0)
Vendor Extension Description: fujifilm.co.jp: 1.0;

Capture Formats:
Display Formats: Association/Directory, DPOF, MS AVI, JPEG, Unknown(b802)

Device Capabilities:
        File Download, File Deletion, File Upload
        No Image Capture, No Open Capture, No vendor specific capture

Storage Devices Summary:
store_00010001:
        StorageDescription: External Memory
        VolumeLabel: 000000000000060890260000440890260000080502830000
        Storage Type: Builtin RAM
        Filesystemtype: Digital Camera Layout (DCIM)
        Access Capability: Read-Write
        Maximum Capability: 503971840 (480 MB)
        Free Space (Bytes): 503930880 (480 MB)
        Free Space (Images): -1

Device Property Summary:
Property 0xd407:(read only) (type=0x6) 1
Property 0xd303:(read only) (type=0x2) 1
Property 0xd406:(readwrite) (type=0xffff) ''
Battery Level(0x5001):(read only) (type=0x2) Enumeration [2,5,25,50,65,80,100] value: 100% (100)
Exposure Time(0x500d): error 200a on query.
F-Number(0x5007): error 200a on query.
LensZoomPosCaps(0xd38c): error 200a on query.
...
...
```
