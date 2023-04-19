

### Mi Recetario Freaky

### TimeLapse con RPi:
NOTA:Estamos con Raspbian 10 Bullseye pq en algún momento de poroblemas con las librerias de toma de fotos empezó a funcionar , pero no recuerdo si es precisamente con la librería que ya estamos usando como definitiva "raspistill", o con las anteriores.

#### Comando raspistill (conexión cable ribon "Camera")
https://roboticsbackend.com/raspberry-pi-camera-take-picture/

https://solectroshop.com/es/content/65-10-usar-una-camara-en-rpi

#### Node Red en Raspberry 3.
Hice muchas pruebas con nodered sobre docker y no terminé de poder hacer que el contenedor guardara la imágen en /home/pi de la poropia RPi, así que se pensaod que todo lo voy a hacer con Node-Red y que no voy a necesitar ningun otro servivcio he instalado nodered directamente sin docker.
Desde la porpia docu oficial de NodeRed: 
https://nodered.org/docs/getting-started/local, 
y específicamente desde aquí:
https://nodered.org/docs/getting-started/raspberrypi
```bash
pi@raspberrypi: $ bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```

#### Pendiente que arranque solo al arrancar la Pi!!
#### Pendiente cambio de contraseña al usuario pi!!
#### Wifi e IP fija.
Partimos de solo conexión cableada con IP dinámica
```bash
pi@raspberrypi:~ $ ifconfig
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:78ff:fef7:e4c1  prefixlen 64  scopeid 0x20<link>
        ether 02:42:78:f7:e4:c1  txqueuelen 0  (Ethernet)
        RX packets 1914  bytes 4491296 (4.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2004  bytes 494816 (483.2 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.126  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::f927:4704:41cf:5ffb  prefixlen 64  scopeid 0x20<link>
        inet6 2a0c:5a84:620a:ae00:693:2247:f4ef:33df  prefixlen 64  scopeid 0x0<
        global>
        ether b8:27:eb:04:32:87  txqueuelen 1000  (Ethernet)
        RX packets 179156  bytes 42427068 (40.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 74937  bytes 73226736 (69.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 35  bytes 3385 (3.3 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 35  bytes 3385 (3.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```
La wifi parece que no está activada:
```bash
pi@raspberrypi:~ $ ifconfig wlan0
wlan0: error fetching interface information: Device not found
```
```bash
# A sample configuration for dhcpcd.
# See dhcpcd.conf(5) for details.

# Allow users of this group to interact with dhcpcd via the control socket.
#controlgroup wheel

# Inform the DHCP server of our hostname for DDNS.
hostname

# Use the hardware address of the interface for the Client ID.
clientid
# or
# Use the same DUID + IAID as set in DHCPv6 for DHCPv4 ClientID as per RFC4361.
# Some non-RFC compliant DHCP servers do not reply with this set.
# In this case, comment out duid and enable clientid above.
#duid

# Persist interface configuration when dhcpcd exits.
persistent

# Rapid commit support.
# Safe to enable by default because it requires the equivalent option set
# on the server to actually work.
option rapid_commit

# A list of options to request from the DHCP server.
option domain_name_servers, domain_name, domain_search, host_name
option classless_static_routes
# Respect the network MTU. This is applied to DHCP routes.
option interface_mtu

# Most distributions have NTP support.
#option ntp_servers

# A ServerID is required by RFC2131.
require dhcp_server_identifier

# Generate SLAAC address using the Hardware Address of the interface
#slaac hwaddr
# OR generate Stable Private IPv6 Addresses based from the DUID
slaac private

# Example static IP configuration:
#interface eth0
#static ip_address=192.168.0.10/24
#static ip6_address=fd51:42f8:caae:d92e::ff/64
#static routers=192.168.0.1
#static domain_name_servers=192.168.0.1 8.8.8.8 fd51:42f8:caae:d92e::1

# It is possible to fall back to a static IP if DHCP fails:
# define static profile
#profile static_eth0
#static ip_address=192.168.1.23/24
#static routers=192.168.1.1
#static domain_name_servers=192.168.1.1

# fallback to static profile on eth0
#interface eth0
#fallback static_eth0
interface wlan0
static ip_address=192.168.0.99/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8

```

añadimos al final de /etc/dhcpcd.conf
Por aqu

#### Configurar el logging con contraseña en nodered.
Para que pida usuario y contraseña hemos recurrido aqui:
https://nodered.org/docs/user-guide/runtime/securing-node-red
En concreto hemos editado y modificado:
```bash
pi@raspberrypi:$ sudo nano /home/pi/.node-red/settings.js
```
NOTA: Para crear la contrseña encriptada se usa un comando que te pide la contraseña y te devuelve encritada para que la peqes directamente en el archivo de config.
Usuario: timelapse
Pass: timesun

Se arranca el servicio con:
```bash
pi@raspberrypi:~ $ node-red-start

Start Node-RED
 
Once Node-RED has started, point a browser at http://192.168.0.126:1880
On Pi Node-RED works better with the Firefox or Chrome browser
 
Use   node-red-stop                          to stop Node-RED
Use   node-red-start                         to start Node-RED again
Use   node-red-log                           to view the recent log output
Use   sudo systemctl enable nodered.service  to autostart Node-RED at every boot
Use   sudo systemctl disable nodered.service to disable autostart on boot
 
To find more nodes and example flows - go to http://flows.nodered.org
 
Starting as a systemd service.
19 Apr 19:07:31 - [info]
Welcome to Node-RED
===================
19 Apr 19:07:32 - [info] Node-RED version: v3.0.2
19 Apr 19:07:32 - [info] Node.js  version: v16.19.1
19 Apr 19:07:32 - [info] Linux 5.10.103+ arm LE
19 Apr 19:07:46 - [info] Loading palette nodes
19 Apr 19:08:08 - [info] Settings file  : /home/pi/.node-red/settings.js
19 Apr 19:08:08 - [info] Context store  : 'default' [module=memory]
19 Apr 19:08:08 - [info] User directory : /home/pi/.node-red
19 Apr 19:08:08 - [warn] Projects disabled : editorTheme.projects.enabled=false
19 Apr 19:08:08 - [info] Flows file     : /home/pi/.node-red/flows.json
19 Apr 19:08:09 - [info] Server now running at http://127.0.0.1:1880/
19 Apr 19:08:09 - [warn] Using unencrypted credentials
19 Apr 19:08:09 - [info] Starting flows
19 Apr 19:08:11 - [info] Started flows

```
y se para con:
```bash
pi@raspberrypi:~ $ node-red-stop
```

#### Descarga de las imágenes que hay en la pi desde nuestro Ubuntu.

```bash
julio@julio-SLIMBOOK:~$ scp pi@192.168.0.126:~/Pictures/*.jpg /home/julio/Documentos/
```

### Opciones invalidas o desechadas con RPi:

Se presentó una primera opción para intentar aprovechar camaras de fotos digitales, controlandolas por USB mediante la librería "gphoto2" (ver al final) pero ninguna de las 3 cámaras ha sido compatible con esta librería. Pasamos a la opción de una cámara conectada directamente al puerto "Camera" de la RaspberryPi (comando libcamera), pero parecía que con la versión ultima de Raspbian:
```bash
pi@raspberrypi:~ $ lsb_release -a
No LSB modules are available.
Distributor ID:	Raspbian
Description:	Raspbian GNU/Linux 11 (bullseye)
Release:	11
Codename:	bullseye

```

**Bajamos a la versión anterior:**
```bash
pi@raspberrypi:~ $ lsb_release -a
No LSB modules are available.
Distributor ID:	Raspbian
Description:	Raspbian GNU/Linux 10 (buster)
Release:	10
Codename:	buster
```


Y ahora, sigue detectandoloa, como veíamos con libcamera, 
```bash
pi@raspberrypi:~ $ vcgencmd get_camera
supported=1 detected=1
```
pero porfin podemos tomar capturas, es funcional: 
```bash
pi@raspberrypi:~ $ raspistill -o image.jpg
pi@raspberrypi:~ $ ls -la
total 2260
drwxr-xr-x 3 pi   pi      4096 Mar 30 11:39 .
drwxr-xr-x 3 root root    4096 Feb 21 03:08 ..
-rw------- 1 pi   pi        57 Mar 30 11:17 .Xauthority
-rw-r--r-- 1 pi   pi       220 Feb 21 03:08 .bash_logout
-rw-r--r-- 1 pi   pi      3523 Feb 21 03:08 .bashrc
drwx------ 3 pi   pi      4096 Mar 30 08:02 .gnupg
-rw-r--r-- 1 pi   pi       807 Feb 21 03:08 .profile
-rw-r--r-- 1 pi   pi   2282040 Mar 30 11:39 image.jpg
```

#### Comando raspistill (conexión cable ribon "Camera")
https://roboticsbackend.com/raspberry-pi-camera-take-picture/
https://solectroshop.com/es/content/65-10-usar-una-camara-en-rpi

#### Node Red en Raspberry 3.
docker run -itd -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /docker/portainer:/data portainer/portainer-ce

docker run --name nodered -itd --restart=always -p 1880:1880 -v /docker/nodered/data:/data  nodered/node-red
```bash
docker run -it --security-opt=seccomp=unconfined -p 1880:1880 -v /docker/nodered/data:/data --name nodered -u 0 --restart=always nodered/node-red
```
#### Comando libcamera (conexión cable ribon "Camera")
Enlace interesante sobre esta librería.
[https://www.raspberrypi.com/documentation/computers/camera_software.html#creating-timelapse-video](https://www.raspberrypi.com/documentation/computers/camera_software.html#libcamera-and-libcamera-apps)

Comprobamos si se ha detectado la cámara
```bash
pi@raspberrypi:~ $ vcgencmd get_camera
supported=0 detected=0, libcamera interfaces=0
```
No se ha detectado.
Entramos en la gonfiguraciónde la pi, con 
```bash
pi@raspberrypi:~ $ sudo raspi-config
```
Quizás haya que activar En Advances activamos "Glamor"

Y en /boot/config.txt
Asegurarse de que  dtoverlay=vc4-fkms-v3d **¿¿¿y/o???** dtoverlay=vc4-kms-v3d
creo que hay que poner: camera_auto_detect=1

obtengo este error:

```bash
pi@raspberrypi:~ $ libcamera-still -o test.jpg
libEGL warning: DRI3: failed to query the version
libEGL warning: DRI2: failed to authenticate
X Error of failed request:  BadRequest (invalid request code or no such operation)
  Major opcode of failed request:  155 ()
  Minor opcode of failed request:  1
  Serial number of failed request:  16
  Current serial number in output stream:  16
```

#### Librería gphoto2 (conexión USB):

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
#### ¿Librería específica de Python? 
Casi seguro que no es nacesario, pero por si acaso:
En algún momento hemos instalado esto
```bash
pi@raspberrypi:$ sudo apt-get install python-picamera python3-picamera
```
Creo que es para uno de los componentes de toma de imágenes en Nodered cuando usábamos Docker. 
