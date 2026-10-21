# Laboratorio fases 1 y 2 — Estudio Torrent

## A1. whois y nslookup



## B. Antes y después
(inventario de servicios, contramedidas aplicadas, la tabla antes/después)

#### Escaneo Nmap Inicial

Comando ejecutado para el descubrimiento inicial de servicios:
```bash
nmap -sV -oN antes.txt 192.168.1.10
```

**Resultado obtenido:**
```text
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

```

-----
#### Los puertos que aparecen antes de cerrar los servicios
<img width="719" height="377" alt="image" src="https://github.com/user-attachments/assets/974804b3-d045-4823-a76a-58464b7bd1b7" />

-----
#### Identificación Local de Procesos
Para comprobar qué procesos y PIDs tienen asignados los puertos escuchando internamente:
```bash
sudo netstat -tulpn
```

<img width="808" height="931" alt="image" src="https://github.com/user-attachments/assets/3729bf6d-12db-4e0c-9706-e3fa6a8ca5dd" />

------

#### Como se cierran los servicios 

Estos servicios corren como demonios independientes continuos. Para detenerlos completamente se debe parar el proceso y eliminar sus enlaces de autoarranque.

* **Fórmula genérica:**
  ```bash
  # 1. Parar el servicio activo
  sudo /etc/init.d/[nombre_del_servicio] stop
  
  # 2. Impedir que vuelva a arrancar al reiniciar
  sudo update-rc.d -f [nombre_del_servicio] remove
  ```

* **Ejemplo real (`proftpd` / `apache2` / `bind9`):**
  ```bash
  # Parar el servicio ProFTPD
  sudo /etc/init.d/proftpd stop
  
  # Quitar enlaces de inicio en los runlevels
  sudo update-rc.d -f proftpd remove
  ```

* **Verificación de servicios con nombre distinto en `netstat` (ej. `master` de Postfix):**
  ```bash
  # Identificar nombre real en init.d y detener
  sudo /etc/init.d/postfix stop
  sudo update-rc.d -f postfix remove
  ```

---

#### 1. Servicios Permanentes (Scripts en `/etc/init.d/` y Enlaces en `/etc/rc2.d/`)

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

Son servicios administrados por el superservidor `xinetd`. Cada servicio se define en un archivo independiente dentro de `/etc/xinetd.d/`.

* **Fórmula genérica:**
  ```bash
  # 1. Identificar el fichero del servicio por nombre o puerto
  grep -w [numero_puerto] /etc/services
  sudo grep -rnw "[nombre_del_servicio]" /etc/xinetd.d/ 2>/dev/null
  
  # 2. Realizar copia de seguridad
  sudo cp /etc/xinetd.d/[nombre_del_servicio] /root/[nombre_del_servicio].bak
  
  # 3. Desactivar el servicio (descomentar/establecer disable = yes)
  sudo sed -i 's/disable.*/disable = yes/' /etc/xinetd.d/[nombre_del_servicio]
  
  # 4. Recargar el portero xinetd
  sudo /etc/init.d/xinetd reload
  ```

* **Ejemplo real (`vsftpd`):**
  ```bash
  # Buscar servicio en puerto 21
  grep -w 21 /etc/services
  sudo grep -rnw "vsftpd" /etc/xinetd.d/ 2>/dev/null
  
  # Hacer respaldo y desactivar
  sudo cp /etc/xinetd.d/vsftpd /root/vsftpd.bak
  sudo sed -i 's/disable.*/disable = yes/' /etc/xinetd.d/vsftpd
  
  # Recargar xinetd
  sudo /etc/init.d/xinetd reload
  ```

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

#### 3. Servicios a Demanda Gestionados por `inetd` (Archivo `/etc/inetd.conf`)

Servicios a demanda administrados dentro del único fichero monolítico `/etc/inetd.conf`.

* **Fórmula genérica:**
  ```bash
  # 1. Averiguar nombre del servicio por el puerto
  grep -w [numero_puerto] /etc/services
  
  # 2. Realizar copia de seguridad de inetd.conf (solo una vez)
  sudo cp /etc/inetd.conf /root/inetd.conf.bak
  
  # 3. Comentar la línea correspondiente al servicio
  sudo sed -i 's/^[nombre_del_servicio]/#[nombre_del_servicio]/' /etc/inetd.conf
  
  # 4. Recargar el demonio xinetd/inetd
  sudo /etc/init.d/xinetd reload
  ```

* **Ejemplo real (`shell` / puerto 514):**
  ```bash
  # Identificar servicio en puerto 514
  grep -w 514 /etc/services
  
  # Comentar línea del servicio shell en inetd.conf
  sudo cp /etc/inetd.conf /root/inetd.conf.bak
  sudo sed -i 's/^shell/#shell/' /etc/inetd.conf ,
  este da fallos, si no con
  sudo nano /etc/inetd.conf/shell
  y buscando la línea con el nombre del servicio y comentarla
  
  # Recargar xinetd
  sudo /etc/init.d/xinetd reload
  ```

Ejemplo : 

<img width="927" height="953" alt="image" src="https://github.com/user-attachments/assets/1ed86411-d018-4063-a64c-478a1b76e501" />


-----

#### Cosas raras 
cunado no están en ningún lado pueden estar en rc.local con sudo nao /etc/rc.local puedes entar para cerrarlo, luego matas el porceso con sudo killall y el  nombre del proceso


-----

<img width="819" height="496" alt="image" src="https://github.com/user-attachments/assets/fa922703-33ef-4b96-8baa-f1cab420ca95" />


<img width="735" height="175" alt="image" src="https://github.com/user-attachments/assets/f2d9e83f-2660-4b04-988c-d351d9cf38bd" />

otro

<img width="544" height="184" alt="image" src="https://github.com/user-attachments/assets/29341554-f6ad-412d-b0ce-87d4c9eb4043" />


#### 5. Bloqueo de ICMP (Respuesta al Ping)

Para denegar respuestas a paquetes ICMP echo-request mediante Firewall IPTables:

* **Fórmula genérica y aplicada:**
  ```bash
  sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
  ```

sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP

---

### B3. Verificación Post-Aplicación (Estado "Después")

Comprobación local antes y después del reinicio del sistema:

```bash
# Verificación interna de puertos a la escucha
sudo netstat -tulpn

# Escaneo externo de confirmación desde la máquina auditora
nmap -sV -oN despues.txt 192.168.56.100
```

**Resultado de Nmap post-hardened:**
```text
Nmap 7.94SVN scan initiated Sun Oct 18 19:09:31 2026 as: nmap -sV -oN despues.txt 192.168.56.100
Nmap scan report for 192.168.56.100
Host is up (0.00076s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.2.8 ((Ubuntu) DAV/2)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap done at Sun Oct 18 19:09:46 2026 -- 1 IP address (1 host up) scanned in 15.06 seconds
```

---


1º antes del reinicio

<img width="788" height="135" alt="image" src="https://github.com/user-attachments/assets/389d0721-d86a-42e9-a8a8-f1950956642f" />

2º despues del reinico 

<img width="622" height="196" alt="image" src="https://github.com/user-attachments/assets/6979fb06-25fb-4df3-982d-da1547bfa409" />

## Reflexión

### B4. Medición DESPUÉS

linux@SAD-Ubuntu:~$ diff antes.txt despues.txt

1,30c1,8

Diferencia entre el reporte de auditoría inicial y final (`diff antes.txt despues.txt`):

```text
1,30c1,8
< Nmap 7.94SVN scan initiated Fri Oct  2 17:18:29 2026 as: nmap -sV -oN antes.txt 192.168.1.10
< PORT     STATE SERVICE      VERSION
< 21/tcp   open  ftp          vsftpd 2.3.4
< 22/tcp   open  ssh          OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
< 23/tcp   open  telnet?
< 25/tcp   open  smtp?
< 53/tcp   open  domain       ISC BIND 9.4.2
< 80/tcp   open  http         Apache httpd 2.2.8 ((Ubuntu) DAV/2)
< 111/tcp  open  rpcbind      2 (RPC #100000)
< 139/tcp  open  netbios-ssn  Samba smbd 3.X - 4.X
< 445/tcp  open  netbios-ssn  Samba smbd 3.X - 4.X
< 512/tcp  open  exec?
< 513/tcp  open  login?
< 514/tcp  open  shell?
< 1099/tcp open  java-rmi     GNU Classpath grmiregistry
< 1524/tcp open  bindshell    Metasploitable root shell
< 2049/tcp open  nfs          2-4
< 2121/tcp open  ccproxy-ftp?
< 3306/tcp open  mysql?
< 5432/tcp open  postgresql   PostgreSQL DB 8.3.0
< 5900/tcp open  vnc          VNC
< 6000/tcp open  X11          (access denied)
< 6667/tcp open  irc          UnrealIRCd
< 8009/tcp open  ajp13        Apache Jserv
< 8180/tcp open  http         Apache Tomcat/Coyote
---
> Nmap scan report for 192.168.56.100
> PORT   STATE SERVICE VERSION
> 22/tcp open  ssh     OpenSSH 4.7p1
> 80/tcp open  http    Apache httpd 2.2.8
```

| Aspecto | Antes | Contramedida aplicada | Después |
| --- | --- | --- | --- |
| Puertos abiertos | 21/tcp22/tcp23/tcp25/tcp53/tcp80/tcp111/tcp139/tcp445/tcp512/tcp513/tcp514/tcp1099/tcp1524/tcp2049/tcp2121/tcp3306/tcp5432/tcp5900/tcp6000/tcp6667/tcp8009/tcp8180/tcp | Cerrar los puertos haciendo que los servicios innecesarios no se encienden  | 80 y 22 |
| Servicios/versiones visibles | ftp ssh telnet? smtp? domain http rpcbind netbios-ssn netbios-ssn exec? login? shell? java-rmi bindshell nfs ccproxy-ftp? mysql? postgresql vnc X11 irc ajp13 http | detener los servicios | ssh y apache |
| Respuesta al ping | Sí | bloquear el ping | No |

### B5. Preguntas finales
¿Qué contramedida ha reducido más lo que ve el atacante? ¿Por qué?

Si solo ocultáis un banner pero el servicio sigue activo, ¿la vulnerabilidad sigue ahí? Razonad la respuesta.

Torrent-Vulnerable usa un sistema operativo sin soporte desde hace años. ¿Puede una contramedida de estas sustituir a actualizarlo? ¿Qué haríais en una empresa real?

De todas las contramedidas de las partes A y B, ¿cuáles evitan que el atacante encuentre información y cuáles solo ayudan a detectar que lo están intentando? (Aún no las hemos aplicado todas, pero pensad en cuáles serían de cada tipo.)


