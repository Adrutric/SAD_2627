# Laboratorio fases 1 y 2 — Estudio Torrent

## A1. whois y nslookup



## B. Antes y después
(inventario de servicios, contramedidas aplicadas, la tabla antes/después)

### Antes
###### Primer escaneo para saber que servicios tiene abiertos la maquina metaespoitable
<img width="688" height="567" alt="image" src="https://github.com/user-attachments/assets/a6464840-7cee-4a87-becf-94738b73b7f8" />

#### Los puertos que aparecen antes de cerrar los servicios
<img width="719" height="377" alt="image" src="https://github.com/user-attachments/assets/974804b3-d045-4823-a76a-58464b7bd1b7" />

Para IDENTIFICA los puertos que están abiertos se usa sudo netstat -tulpn
<img width="808" height="931" alt="image" src="https://github.com/user-attachments/assets/3729bf6d-12db-4e0c-9706-e3fa6a8ca5dd" />


#### Como se cierran los servicios 
Ejemplos:
Servicios de inetd o xinetd :  grep 513 /etc/services
Ejemplo de reinicio de un servicio /etc/init.d/shh reload 

#### Los servicios de xinetd.d
hay que comentar el servicio con un "#" delante del servicio en los archivos de desntro

#### Los servicios de inetd.conf
es un archivo no un directorio
hay que comentar el servicio con un "#" delante del servicio 


#### Los servicios de rc2.d y compañía 


### Después


## Reflexión
(respuestas a las preguntas finales)

