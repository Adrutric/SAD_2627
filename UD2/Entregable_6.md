## Parte A · ¿Está actualizado el sistema?
Trabajad en Torrent-Auditor.

### A1. Anotad qué versión de sistema operativo y kernel tenéis:

lsb_release -a
uname -r

linux@SAD-Ubuntu:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.3 LTS
Release:	24.04
Codename:	noble

linux@SAD-Ubuntu:~$ uname -r
7.0.0-31-generic
 

### A2. Pedid al sistema la lista de paquetes disponibles y qué se puede actualizar:

sudo apt update
apt list --upgradable
Anotad: ¿cuántos paquetes tienen actualización pendiente?

239 packages can be upgraded. Run 'apt list --upgradable' to see them.


### A3. Simulad la actualización sin aplicarla:

sudo apt-get -s upgrade | head -30
La opción -s es "simular". Fijaos en que dice qué instalaría, pero no toca nada.

sudo apt-get -s upgrade | head -30
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
The following upgrades have been deferred due to phasing:
  snapd
The following packages have been kept back:
  fwupd linux-generic-hwe-24.04 linux-headers-generic
  linux-headers-generic-hwe-24.04 linux-image-generic-hwe-24.04
The following packages will be upgraded:
  
  alsa-ucm-conf apparmor apport apport-core-dump-handler apport-gtk base-files
  
  bind9-dnsutils bind9-host bind9-libs bluez bluez-cups bluez-obexd cloud-init
  
  console-setup console-setup-linux dconf-cli dconf-gsettings-backend
  
  dconf-service dhcpcd-base dmidecode dnsmasq-base dracut-install gdb gdm3
  
  gir1.2-gdm-1.0 gir1.2-glib-2.0 gir1.2-gtk-4.0 gir1.2-mutter-14 gir1.2-nm-1.0
  
  gir1.2-rsvg-2.0 gir1.2-soup-3.0 gnome-control-center
  
  gnome-control-center-data gnome-control-center-faces gnome-keyring
  
  gnome-keyring-pkcs11 gnome-remote-desktop gnome-settings-daemon
  
  gnome-settings-daemon-common gnome-shell gnome-shell-common
  
  gnome-shell-extension-desktop-icons-ng gstreamer1.0-alsa gstreamer1.0-gl
  
  gstreamer1.0-plugins-base gstreamer1.0-plugins-base-apps
  
  gstreamer1.0-plugins-good gstreamer1.0-x gvfs gvfs-backends gvfs-common
  
  gvfs-daemons gvfs-fuse gvfs-libs heif-gdk-pixbuf heif-thumbnailer
  
  initramfs-tools initramfs-tools-bin initramfs-tools-core iproute2
  
  keyboard-configuration krb5-locales language-pack-en language-pack-en-base
  
  language-pack-gnome-en language-pack-gnome-en-base ldap-utils libapparmor1
  
  libarchive13t64 libaudit-common libaudit1 libauthen-sasl-perl libbluetooth3
  
  libcurl3t64-gnutls libcurl4t64 libdconf1 libde265-0 libegl-mesa0 libexpat1
  
  libfprint-2-2 libfprint-2-tod1 libfreerdp-server3-3 libfreerdp3-3
  
  libfreetype6 libfwupd2 libgbm1 libgdm1 libgl1-mesa-dri libglib2.0-0t64


### A4. ¿Y si el sistema ya no recibe parches? Buscad en https://endoflife.date la fecha de fin de soporte de:

La versión de Ubuntu/Debian de vuestro Torrent-Auditor.
Ubuntu 8.04, la que tiene Torrent-Vulnerable (Metasploitable2).
Rellenad:

| Sistema | Fecha de fin de soporte | ¿Recibe parches hoy? |
| --- | --- | --- |
| Torrent-Auditor | Ends in 2 years and 7 months (31 May 2029) Ends in 2 years and 7 months (31 May 2029) Ends in 7 years (25 Apr 2034) | Sí |
| Torrent-Vulnerable (Ubuntu 8.04) | 8.04 'Hardy Heron' (LTS)	18 years ago (24 Apr 2008) Ended 13 years ago (09 May 2013) Ended 13 years ago (09 May 2013) Unavailable | No |

Pregunta A: En la oferta de empleo de Torrent (dossier de la UD02) decían que trabajaban con "Ubuntu 8.04". Aunque pusieran todas las contraseñas bien, ¿qué problema sigue sin tener solución?

Es una versión muy antigua y que lleva 18 años sin actualizaciones eso hace que este lleno de vulnerabilidades y de problemas con softwares más modernos, la única solución que hay es actualizar a un sistema operativo que tenga soporte de actualizaciones de seguridad  

## Parte B · ¿De dónde vienen los programas instalados?

### B1. Mirad de qué repositorios se descarga vuestro sistema:
  
linux@SAD-Ubuntu:~$ cat /etc/apt/sources.list
# Ubuntu sources have moved to /etc/apt/sources.list.d/ubuntu.sources

linux@SAD-Ubuntu:~$ cat /etc/apt/sources.list.d/ubuntu.sources
Types: deb
Esto indica que el tipo de paquete que se va a descargar son paquetes binarios tradicionales 

URIs: http://es.archive.ubuntu.com/ubuntu/
Aquí se esta especificando la URL al repositorio oficial de Ubuntu de donde se descargaran los paquetes

Suites: noble noble-updates noble-backports
En este define la versión de Ubuntu que se está utilizando 

Components: main restricted universe multiverse
Aquí lo que indica la categoría de software habilitadas, main indica que es software libre y de código abierto oficialmente soportado, el restricted indica que los drivers son privativos o restringidos para hardware, el universe que es un software libre mantenido por la comunidad  y el multiverse que es un software con restricciones de copyright


Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Aquí indica la ruta hasta el archivo que contiene  las firmas de autenticidad de los paquetes que te descargas con el apt install 

Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
Todo este bloque es lo mismo que el anterior pero este esta centrado en las actualizaciones del sistema 

linux@SAD-Ubuntu:~$ ls /etc/apt/sources.list.d/
ubuntu.sources  ubuntu.sources.curtin.orig

### B2. apt solo instala paquetes firmados por claves en las que confía. 

Mirad dónde están esas claves:
ls /etc/apt/trusted.gpg.d/ /usr/share/keyrings/ 2>/dev/null

/etc/apt/trusted.gpg.d/:
ubuntu-keyring-2012-cdimage.gpg
ubuntu-keyring-2018-archive.gpg

/usr/share/keyrings/:
ubuntu-archive-keyring.gpg
ubuntu-archive-removed-keys.gpg
ubuntu-cloudimage-keyring.gpg
ubuntu-cloudimage-removed-keys.gpg
ubuntu-master-keyring.gpg
ubuntu-pro-anbox-cloud.gpg
ubuntu-pro-cc-eal.gpg
ubuntu-pro-cis.gpg
ubuntu-pro-esm-apps.gpg
ubuntu-pro-esm-apps-legacy.gpg
ubuntu-pro-esm-infra.gpg
ubuntu-pro-esm-infra-legacy.gpg
ubuntu-pro-fips.gpg
ubuntu-pro-fips-preview.gpg
ubuntu-pro-realtime-kernel.gpg
ubuntu-pro-ros.gpg

Reflexiona: 
¿qué pasaría si un atacante introduce una clave falsa en /usr/share/keyrings/?
Por si solo no pasaría nada ya que APT no da por bueno todo lo que esta en /usr/share/keyrings/ , si no que también revisa que este la firma en la ruta del Signed-By:

¿y si intercepta o reemplaza la clave oficial de Ubuntu ubuntu-archive-keyrings.gpg?
En este caso si que podría modificar los archivos y poner que los paquetes suyos son legítimos con la firma y APT los instalaría 


### B3. Información de un paquete concreto. Elegid openssh-server o curl:

<img width="839" height="253" alt="image" src="https://github.com/user-attachments/assets/df3e08f7-0cc8-489e-9431-cecde97bafcc" />


Versión instalada (Installed): 1:9.6p1-3ubuntu13.19

Versión candidata (Candidate): 1:9.6p1-3ubuntu13.19

Repositorio de origen:   
500 http://es.archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages 
500 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages  
100 /var/lib/dpkg/status

Significado de la tabla de versiones:  *** indican la versión instalada actualmente en el sistema
 
500 y 100 representan las prioridades de preferencia (pin priority) de cada fuente

amd64 indica para que arquitectura es 


### B4. Buscad paquetes instalados que no proceden de ningún repositorio (instalados a mano):

<img width="880" height="225" alt="image" src="https://github.com/user-attachments/assets/e0e82cdc-5bee-4a25-9c74-aff050c7ac4d" />

Estos se instalaron mediante apt de los repositorios oficiales de Ubuntu y fueron verificados por Canonical con las firmas digitales GPG 
krb5-locales/noble-security,now 1.20.1-6ubuntu2.7 all [installed,upgradable to: 1.20.1-6ubuntu2.10]

libencode-locale-perl/noble,now 1.05-3 all [installed,automatic]

liblocale-gettext-perl/noble,now 1.07-6ubuntu5 amd64 [installed,automatic]

locales/noble-updates,noble-security,now 2.39-0ubuntu8.9 all [installed,automatic]

python-babel-localedata/noble,now 2.10.3-3build1 all [installed,automatic]

Este si que fue instalado de manera manual con el usuario root descargándolo del repositorio de Tenable para Nessus y este no fue verificado 

nessus/now 10.12.4 amd64 [installed,local]



### B5. Comprobad si algún fichero de un paquete ha cambiado desde que se instaló:

??5??????   /opt/nessus/etc/nessus/fipsmodule.cnf
missing     /opt/nessus/var/nessus/tmp/nessusd
missing     /opt/nessus/var/nessus/tmp/nessusd.service
??5??????   /opt/nessus/lib/nessus/plugins/known_CA.inc
??5??????   /usr/lib/systemd/system/cloud-init.service



Pregunta B: 

dpkg -V ¿detectaría un rootkit que además hubiera modificado la base de datos de dpkg? 

No, dpkg -V comprueba los archivos actuales con la propia base de datos local de dpkg que esta en /var/lib/dpkg/ y si un rootkit modifica un binario del sistema y la base de datos local de dpkg para que coincidan con el archivo modificado, para dpkg -V todo estará en orden.

¿Por qué conviene comparar con una copia guardada fuera del sistema? (Pista: recordad la fase 4 de la intrusión.)
Por que en el caso de que haya una alteración en un archivo por un atacante si la comprobación esta en otra maquina el atacante no podrá modificarla para que sea mas difícil darse cuenta de la alteración


## Parte C · Verificar una descarga: hash y firma

### C1. Descarga:

mkdir -p ~/verif && cd ~/verif
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz
wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz.sig
wget https://ftp.gnu.org/gnu/gnu-keyring.gpg

linux@SAD-Ubuntu:~$ mkdir -p ~/verif && cd ~/verif
linux@SAD-Ubuntu:~/verif$ wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz
--2026-10-18 10:43:10--  https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz
Resolving ftp.gnu.org (ftp.gnu.org)... 209.51.188.20, 2001:470:142:3::b
Connecting to ftp.gnu.org (ftp.gnu.org)|209.51.188.20|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1033297 (1009K) [application/x-gzip]
Saving to: ‘hello-2.12.1.tar.gz’

hello-2.12.1.tar.gz 100%[===================>]   1009K  1.38MB/s    in 0.7s    

2026-10-18 10:43:11 (1.38 MB/s) - ‘hello-2.12.1.tar.gz’ saved [1033297/1033297]

linux@SAD-Ubuntu:~/verif$ wget https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz.sig
--2026-10-18 10:43:21--  https://ftp.gnu.org/gnu/hello/hello-2.12.1.tar.gz.sig
Resolving ftp.gnu.org (ftp.gnu.org)... 209.51.188.20, 2001:470:142:3::b
Connecting to ftp.gnu.org (ftp.gnu.org)|209.51.188.20|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 488 [application/pgp-signature]
Saving to: ‘hello-2.12.1.tar.gz.sig’

hello-2.12.1.tar.gz 100%[===================>]     488  --.-KB/s    in 0s      

2026-10-18 10:43:21 (30.3 MB/s) - ‘hello-2.12.1.tar.gz.sig’ saved [488/488]

linux@SAD-Ubuntu:~/verif$ wget https://ftp.gnu.org/gnu/gnu-keyring.gpg
--2026-10-18 10:43:30--  https://ftp.gnu.org/gnu/gnu-keyring.gpg
Resolving ftp.gnu.org (ftp.gnu.org)... 209.51.188.20, 2001:470:142:3::b
Connecting to ftp.gnu.org (ftp.gnu.org)|209.51.188.20|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3698125 (3.5M)
Saving to: ‘gnu-keyring.gpg’

gnu-keyring.gpg     100%[===================>]   3.53M  3.11MB/s    in 1.1s    

2026-10-18 10:43:32 (3.11 MB/s) - ‘gnu-keyring.gpg’ saved [3698125/3698125]

linux@SAD-Ubuntu:~/verif$ 

### C2. Calculad el hash:

sha256sum hello-2.12.1.tar.gz
Comparadlo con vuestro compañero de al lado. ¿Sale igual? Debería.
el mio
linux@SAD-Ubuntu:~/verif$ sha256sum hello-2.12.1.tar.gz
8d99142afd92576f30b0cd7cb42a8dc6809998bc5d607d88761f512e26c7db20  hello-2.12.1.tar.gz

### C3. Verificad la firma usando la lista de claves de GNU:

linux@SAD-Ubuntu:/verif$ gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-2.12.1.tar.gz.sig hello-2.12.1.tar.gz

gpg: Signature made Sun 29 May 2022 11:05:00 PM UTC

gpg:                    using RSA key 24093F016FFE8602EF449BB84C8EF3DA3FD37230

gpg: Good signature from "Reuben Thomas <rrt@sc3d.org>" [unknown]

gpg:                 aka "keybase.io/rrt <rrt@keybase.io>" [unknown]

gpg: WARNING: This key is not certified with a trusted signature!

gpg:           There is no indication that the signature belongs to the owner.

Primary key fingerprint: 2409 3F01 6FFE 8602 EF44  9BB8 4C8E F3DA 3FD3 7230

linux@SAD-Ubuntu:/verif$


Nombre de quien firmó: Reuben Thomas <rrt@sc3d.org>

Fecha de la firma: Sun 29 May 2022 11:05:00 PM UTC

Identificador de la clave: 4C8EF3DA3FD37230



### C4. El experimento: ¿qué pasa si alguien toca el fichero? Haced una copia y añadid un carácter:

linux@SAD-Ubuntu:~/verif$ cp hello-2.12.1.tar.gz hello-manipulado.tar.gz

linux@SAD-Ubuntu:~/verif$ echo "x" >> hello-manipulado.tar.gz

linux@SAD-Ubuntu:~/verif$ sha256sum hello-2.12.1.tar.gz hello-manipulado.tar.gz
8d99142afd92576f30b0cd7cb42a8dc6809998bc5d607d88761f512e26c7db20  hello-2.12.1.tar.gz
8c253f1ad6ea1e57b01624a938f6e81aec3685fcc53ed2e5bd5f99e22e417cf8  hello-manipulado.tar.gz

linux@SAD-Ubuntu:~/verif$ cp hello-2.12.1.tar.gz.sig hello-manipulado.tar.gz.sig

linux@SAD-Ubuntu:~/verif$ gpg --no-default-keyring --keyring ./gnu-keyring.gpg --verify hello-manipulado.tar.gz.sig hello-manipulado.tar.gz
gpg: Signature made Sun 29 May 2022 11:05:00 PM UTC
gpg:                using RSA key 24093F016FFE8602EF449BB84C8EF3DA3FD37230
gpg: BAD signature from "Reuben Thomas <rrt@sc3d.org>" [unknown]

¿cómo cambia el hash?
Cambia el hash por que al añadir un solo carácter la cadena hexadecimal ya no es la misma y eso da otro resultado ha la hora de calcularlo 

8d99142afd92576f30b0cd7cb42a8dc6809998bc5d607d88761f512e26c7db20  hello-2.12.1.tar.gz


8c253f1ad6ea1e57b01624a938f6e81aec3685fcc53ed2e5bd5f99e22e417cf8  hello-manipulado.tar.gz

¿Qué dice ahora gpg?
gpg: BAD signature from "Reuben Thomas <rrt@sc3d.org>" [unknown]


### C5. Pregunta C: completad:

| Situación | ¿Basta con el hash? | ¿Hace falta la firma? | ¿Por qué? |
| --- | --- | --- | --- |
| El fichero se corrompió al descargarlo | Sí | No | Por qué al calcular el hash del archivo que te descargas si esta incompleto por un fallo a la hora de descargar el resultado será diferente al que dice el distribuidor de ese archivo |
| Un atacante cambió el fichero y el hash publicado en la misma web | No | Sí | En este caso el hash coincidirá pero la firma será diferente y es la manera de identificar que el archivo ha sido manipulado |
| Descargas desde un espejo (*mirror*) que no es de confianza | No | Sí | En este caso es parecido al anterior, el servidor puede tener un hash del archivo manipulado y solo con la firma gpt se puede saber si el archivo es el original y de quien debería de ser  |


## Parte D · Política de actualizaciones para Torrent

Torrent tiene tres socios y nadie se encarga del mantenimiento. Redactad una política de máximo 6 líneas que responda:

¿Quién se encarga de las actualizaciones y con qué frecuencia?
¿Se actualiza todo automáticamente o primero se prueba? (Pensad en el equipo del servidor de impresión y en el ERP.)
¿De dónde se puede instalar software y quién lo autoriza?
¿Qué se hace con el equipo que ya no recibe parches (EOL)?

Se tiene que designar ha alguien que se encargue de revisar las actualizaciones y aplicarlas cuando haya alguna al menos una vez al mes, para evitar que los servidores sufran fallos por algún error en las actualizaciones primero se se tendrán que poner aprueba en maquinas aparte y cuando se vea que no dan fallo se aplican las actualizaciones al servidor de impresión y al ERP, solo se podrá instalar software de los repositorios oficiales y que cuenten con las firmas de autenticidad, en el caso de que sea necesario algún programa externo se tendrá que verificar que es fiable en un entorno de pruebas controlado, y los equipos que ya no reciban parches se tendrán que actualizar a sistemas mas modernos y que si que tengan parches de seguridad.


Para ver la opción automática en Ubuntu/Debian (solo mirad, no hace falta activarla):

apt policy unattended-upgrades
cat /etc/apt/apt.conf.d/20auto-upgrades 2>/dev/null

linux@SAD-Ubuntu:~$ apt policy unattended-upgrades

unattended-upgrades:
  Installed: 2.9.1+nmu4ubuntu1
  Candidate: 2.9.1+nmu4ubuntu1
  Version table:

 *** 2.9.1+nmu4ubuntu1 500
        500 http://es.archive.ubuntu.com/ubuntu noble/main amd64 Packages
        100 /var/lib/dpkg/status

linux@SAD-Ubuntu:~$ cat /etc/apt/apt.conf.d/20auto-upgrades 2>/dev/null

APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";


Debate: actualizar automáticamente reduce el riesgo de que Torrent esté desactualizado, pero una actualización puede romper el ERP un viernes por la tarde. ¿Qué ganáis y qué perdéis con cada opción?

Con las actualizaciones automáticas no tiene que haber un trabajador pendiente de si hay que actualizar o no algo, también las vulnerabilidades que hay en las versiones anteriores no les afectan por tener los equipos actualizados, por otro lado si ocurre un fallo con esa actualización no habrá manera de detectarlo antes de que se ponga en los equipos de producción como el servidor de impresión y puede que detenga toda la producción hasta que vuelva a la versión anterior de manera manual.
Mientras que de manera manual tienes que tener ha alguien que se encargue de comprobar y actualizar los equipos, esto te da la garantía de que cuando actualices todo funcionara como debe por que ya se ha probado antes pero eso también hace que todo sea más lento y que las actualizaciones tarden más en aplicarse haciendo que las vulnerabilidades estén por mas tiempo.



Windows: los mismos conceptos existen allí con otros nombres. winver (versión), Windows Update (parches), Get-FileHash fichero (hash) y Get-AuthenticodeSignature fichero (firma de un ejecutable).



