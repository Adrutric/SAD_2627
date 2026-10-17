
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

### C2. Calculad el hash:

sha256sum hello-2.12.1.tar.gz
Comparadlo con vuestro compañero de al lado. ¿Sale igual? Debería.


