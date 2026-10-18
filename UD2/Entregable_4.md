# Laboratorio fases 1 y 2 — Estudio Torrent

## A1. whois y nslookup



## B. Antes y después
(inventario de servicios, contramedidas aplicadas, la tabla antes/después)

### Antes
###### Primer escaneo para saber que servicios tiene abiertos la maquina metaespoitable
Nmap 7.94SVN scan initiated Fri Oct  2 17:18:29 2026 as: nmap -sV -oN antes.txt 192.168.1.10

Nmap scan report for 192.168.1.10

Host is up (0.00097s latency).

Not shown: 977 closed tcp ports (reset)

PORT     STATE SERVICE      VERSION

21/tcp   open  ftp          vsftpd 2.3.4

22/tcp   open  ssh          OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)

23/tcp   open  telnet?

25/tcp   open  smtp?

53/tcp   open  domain       ISC BIND 9.4.2

80/tcp   open  http         Apache httpd 2.2.8 ((Ubuntu) DAV/2)

111/tcp  open  rpcbind      2 (RPC #100000)

139/tcp  open  netbios-ssn  Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

445/tcp  open  netbios-ssn  Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

512/tcp  open  exec?

513/tcp  open  login?

514/tcp  open  shell?

1099/tcp open  java-rmi     GNU Classpath grmiregistry

1524/tcp open  bindshell    Metasploitable root shell

2049/tcp open  nfs          2-4 (RPC #100003)

2121/tcp open  ccproxy-ftp?

3306/tcp open  mysql?

5432/tcp open  postgresql   PostgreSQL DB 8.3.0 - 8.3.7

5900/tcp open  vnc          VNC (protocol 3.3)

6000/tcp open  X11          (access denied)

6667/tcp open  irc          UnrealIRCd

8009/tcp open  ajp13        Apache Jserv (Protocol v1.3)

8180/tcp open  http         Apache Tomcat/Coyote JSP engine 1.1

MAC Address: 08:00:27:82:4E:36 (Oracle VirtualBox virtual NIC)

Service Info: Host: irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done at Fri Oct  2 17:21:31 2026 -- 1 IP address (1 host up) scanned in 181.38 seconds

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

grep -w 21 /etc/services **dice el nombre del servicio que esta en el puerto**

sudo grep -rnw "vsftpd" /etc/inetd.conf /etc/xinetd.conf /etc/xinetd.d/ 2>/dev/null

sudo cp /etc/xinetd.d/vsftpd /root/vsftp.bak la copia de seguridad por cada servicio que cierres 

sudo sed -i 's/disable.*/disable = yes/' /etc/xinetd.d/vsftpd

si no funciona hay que hacerlo de manera manual entrado a cambiarlo sudo nano /etc/xinetd.d/vsftpd

<img width="569" height="339" alt="image" src="https://github.com/user-attachments/assets/e1ec90c0-4ccd-4196-9865-f65118056122" />

ps aux | grep -i

------

#### Los servicios de inetd.conf

es un archivo no un directorio
hay que comentar el servicio con un "#" delante del servicio 

grep -w 514 /etc/services **dice el nombre del servicio que esta en el puerto**

sudo grep -rnw "shell" /etc/inetd.conf /etc/xinetd.conf /etc/xinetd.d/ 2>/dev/null con esto te dice donde esta el servicio guardado solo hay que cambiar el nombre que esta entre ""

sudo cp /etc/inetd.conf /root/inetd.conf.bak copia de seguridad, en este solo necesitas hacerlo en el primero sino reescribe la copia 

sudo sed -i 's/^shell/#shell/' /etc/inetd.conf en este caso ha que cambiar esto s/^shell/#shell/ por el nombre del servico que quieres cerrar

sudo /etc/init.d/xinetd reload hay que reiniciar el portero para que se cierre del todo y no aparezca como no cerrado en el netstat -tulpn

Ejemplo : 

<img width="927" height="953" alt="image" src="https://github.com/user-attachments/assets/1ed86411-d018-4063-a64c-478a1b76e501" />


-----

#### Los servicios de rc2.d y compañía 



-----
#### Cosas raras 
cunado no están en ningún lado pueden estar en rc.local con sudo nao /etc/rc.local puedes entar para cerrarlo, luego matas el porceso con sudo killall y el  nombre del proceso


-----

<img width="819" height="496" alt="image" src="https://github.com/user-attachments/assets/fa922703-33ef-4b96-8baa-f1cab420ca95" />


<img width="735" height="175" alt="image" src="https://github.com/user-attachments/assets/f2d9e83f-2660-4b04-988c-d351d9cf38bd" />

otro

<img width="544" height="184" alt="image" src="https://github.com/user-attachments/assets/29341554-f6ad-412d-b0ce-87d4c9eb4043" />


### Después
1º antes del reinicio

<img width="788" height="135" alt="image" src="https://github.com/user-attachments/assets/389d0721-d86a-42e9-a8a8-f1950956642f" />

2º despues del reinico 

<img width="622" height="196" alt="image" src="https://github.com/user-attachments/assets/6979fb06-25fb-4df3-982d-da1547bfa409" />

## Reflexión

### B5. Preguntas finales
¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué?

Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta.

Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?

De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)


