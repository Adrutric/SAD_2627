# Laboratorio fases 1 y 2 — Estudio Torrent

## A1. whois y nslookup



## B. Antes y después
(inventario de servicios, contramedidas aplicadas, la tabla antes/después)

### Antes
###### Primer escaneo para saber que servicios tiene abiertos la maquina metaespoitable
<img width="688" height="567" alt="image" src="https://github.com/user-attachments/assets/a6464840-7cee-4a87-becf-94738b73b7f8" />

-----

#### Los puertos que aparecen antes de cerrar los servicios
<img width="719" height="377" alt="image" src="https://github.com/user-attachments/assets/974804b3-d045-4823-a76a-58464b7bd1b7" />

-----

Para IDENTIFICA los puertos que están abiertos se usa sudo netstat -tulpn
<img width="808" height="931" alt="image" src="https://github.com/user-attachments/assets/3729bf6d-12db-4e0c-9706-e3fa6a8ca5dd" />

------

#### Como se cierran los servicios 
Ejemplos:
Servicios de inetd o xinetd :  grep 513 /etc/services
Ejemplo de reinicio de un servicio /etc/init.d/shh reload 

1º
<img width="807" height="47" alt="image" src="https://github.com/user-attachments/assets/ca00021e-4711-4f9f-ab55-dc5043ba0242" />

2º
<img width="481" height="107" alt="image" src="https://github.com/user-attachments/assets/bf8c337b-3963-4319-b4ba-cd85dd775798" />

3º
<img width="503" height="196" alt="image" src="https://github.com/user-attachments/assets/f2a5646f-af2c-4956-88aa-d0067c838e16" />

Otro ejemplo

<img width="721" height="346" alt="image" src="https://github.com/user-attachments/assets/13b7165e-56c9-43c2-a65f-1827bc021916" />

Uno más 

<img width="748" height="302" alt="image" src="https://github.com/user-attachments/assets/d66eab96-c450-4335-92b6-f7d59330c463" />


##### Esto sirve para los que estan en init.d pero en el netstat aparecen con otro nombre como por ejemplo el master 
<img width="456" height="15" alt="image" src="https://github.com/user-attachments/assets/54dc86d7-e0f0-45ce-8fa7-efe6a5241ca1" />

-----

#### Los servicios de xinetd.d
hay que comentar el servicio con un "#" delante del servicio en los archivos de desntro




------

#### Los servicios de inetd.conf
es un archivo no un directorio
hay que comentar el servicio con un "#" delante del servicio 




-----

#### Los servicios de rc2.d y compañía 





-----

<img width="819" height="496" alt="image" src="https://github.com/user-attachments/assets/fa922703-33ef-4b96-8baa-f1cab420ca95" />


<img width="735" height="175" alt="image" src="https://github.com/user-attachments/assets/f2d9e83f-2660-4b04-988c-d351d9cf38bd" />

otro

<img width="544" height="184" alt="image" src="https://github.com/user-attachments/assets/29341554-f6ad-412d-b0ce-87d4c9eb4043" />


### Después


## Reflexión

### B5. Preguntas finales
¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué?
Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta.
Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?
De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)

