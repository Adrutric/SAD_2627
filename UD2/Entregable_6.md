
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


