# PARTE 1 Nuestra propia autoridad de certificación

| Fichero | Qué es | Quién debe tenerlo
| --- | --- | --- |
| ca.key | Clave privada de la CA | Solo la CA. Si se filtra, cualquiera puede emitir certificados falsos en nombre de Torrent S.L. | 
| ca.crt | Certificado de la CA (su clave pública) | Todos los clientes que deban confiar en ella | 
| srv.key | Clave privada del servidor web | Solo el servidor | 
| srv.csr | Petición de certificado | Se envía a la CA para que la firme | 
| srv.crt | Certificado del servidor, firmado por la CA | Se publica: lo envía el servidor a cada cliente | 

⚠ En esta práctica, por rapidez, la CA y el servidor web comparten máquina. En un entorno real la clave de la CA nunca está en el servidor web ⚠

- Paso 0 — Instalar Apache y comprobar que funciona (todavía sin cifrar)

```
sudo apt update
sudo apt install -y apache2 openssl curl cron
systemctl status apache2 --no-pager
sudo ss -tlnp | grep ':80'

```
- Paso 1 — Crear una carpeta de trabajo para la CA

```
mkdir -p ~/ca/private ~/ca/certs
chmod 700 ~/ca/private
cd ~/ca
```

<img width="598" height="94" alt="image" src="https://github.com/user-attachments/assets/08c2800e-ac8d-4820-a455-73a42755386e" />

- Paso 2 — Generar la clave privada de la CA

```
openssl genrsa -aes256 -out private/ca.key 4096
```

La contraseña ```Linux```

<img width="664" height="111" alt="image" src="https://github.com/user-attachments/assets/8dba9373-b874-4883-b625-e25519960581" />


- Paso 3 — Crear el certificado raíz de la CA (autofirmado)
```
openssl req -x509 -new -key private/ca.key -sha256 -days 3650 \
 -out certs/ca.crt \
 -subj "/C=ES/O=Torrent S.L./CN=Torrent S.L. CA Raiz" \
 -addext "basicConstraints=critical,CA:TRUE" \
 -addext "keyUsage=critical,keyCertSign,cRLSign"
```

<img width="667" height="177" alt="image" src="https://github.com/user-attachments/assets/0d2dd723-9c6b-4eed-bb66-f4d3fe3162ae" />

- Paso 4 — Inspeccionar el certificado que acabas de crear
```
openssl x509 -in certs/ca.crt -noout -subject -issuer -dates
openssl x509 -in certs/ca.crt -noout -text | grep -A1 'Basic Constraints'
```

<img width="664" height="253" alt="image" src="https://github.com/user-attachments/assets/c730a0a4-5ad6-4cdc-a349-985844859e11" />


- Paso 5 — Clave privada del servidor y petición de certificado (CSR)
```
openssl genrsa -out private/srv.key 2048
openssl req -new -key private/srv.key -out srv.csr \
 -subj "/C=ES/O=Torrent S.L./CN=hmi.torrent.local"
```

<img width="665" height="50" alt="image" src="https://github.com/user-attachments/assets/ed421429-df41-4678-aa89-a3786c0e44b2" />

<img width="670" height="109" alt="image" src="https://github.com/user-attachments/assets/7f57b22f-86ea-4d26-9cf0-f9f2433e6ac9" />


- Paso 6 — Indicar para qué nombres e IP es válido el certificado (SAN)
```
 nano srv.ext


subjectAltName = DNS:hmi.torrent.local, IP:IP_SERVIDOR
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
extendedKeyUsage = serverAuth
```

<img width="585" height="151" alt="image" src="https://github.com/user-attachments/assets/9a5c0802-5321-479d-bf5f-314bede4f6e9" />

- Paso 7 — La CA firma el certificado del servidor
```
openssl x509 -req -in srv.csr \
 -CA certs/ca.crt -CAkey private/ca.key -CAcreateserial \
 -out certs/srv.crt -days 365 -sha256 -extfile srv.ext

```

<img width="670" height="135" alt="image" src="https://github.com/user-attachments/assets/587f550b-085c-4ea1-96f9-eefa1c1ac18d" />


- Paso 8 — Verificar la cadena de confianza

```
openssl verify -CAfile certs/ca.crt certs/srv.crt
openssl x509 -in certs/srv.crt -noout -subject -issuer -ext subjectAltName
```

<img width="667" height="154" alt="image" src="https://github.com/user-attachments/assets/79565feb-8c4f-4e18-8919-245c9739fc2a" />


- Paso 9 — Instalar el certificado en Apache

```
sudo cp ~/ca/certs/srv.crt /etc/ssl/certs/torrent-srv.crt
sudo cp ~/ca/private/srv.key /etc/ssl/private/torrent-srv.key
sudo chmod 600 /etc/ssl/private/torrent-srv.key
sudo a2enmod ssl
sudo nano /etc/apache2/sites-available/default-ssl.conf
# En default-ssl.conf localiza las dos líneas que apuntan a un certificado de pruebas (ssl-cert-snakeoil) y cámbialas por las tuyas:

SSLCertificateFile /etc/ssl/certs/torrent-srv.crt
SSLCertificateKeyFile /etc/ssl/private/torrent-srv.key
```
<img width="630" height="188" alt="image" src="https://github.com/user-attachments/assets/6ea8dc98-3254-4810-a09d-f52163f0a17b" />

<img width="397" height="76" alt="image" src="https://github.com/user-attachments/assets/dd24ac64-30e3-441f-b085-5e2309c0f20d" />

```
sudo a2ensite default-ssl
sudo apache2ctl configtest
sudo systemctl restart apache2
sudo ss -tlnp | grep ':443'

```

<img width="371" height="326" alt="image" src="https://github.com/user-attachments/assets/5f8dce3c-5c4c-4ceb-868c-5e9b82378446" />

- Paso 10 — Probar HTTPS desde la propia VM, antes de confiar en la CA
```
curl -I https://IP_SERVIDOR


curl -I --cacert ~/ca/certs/ca.crt https://IP_SERVIDOR
```

<img width="365" height="117" alt="image" src="https://github.com/user-attachments/assets/8cb09084-7b2d-4147-89db-11ac4adb0801" />

<img width="522" height="115" alt="image" src="https://github.com/user-attachments/assets/5a9bc925-10ce-4e1a-a142-506e0ada0687" />


- Paso 11 — El navegador: advertencia, huella e importación de la CA
```
sudo cp ~/ca/certs/ca.crt /var/www/html/torrent-ca.crt
openssl x509 -in ~/ca/certs/ca.crt -noout -fingerprint -sha256

```

<img width="664" height="68" alt="image" src="https://github.com/user-attachments/assets/93c321c4-5df5-4c55-95ca-37a648e6620d" />

- Paso 12 — Mirar la conexión TLS por dentro
```
openssl s_client -connect IP_SERVIDOR:443 -CAfile ~/ca/certs/ca.crt \  </dev/null 2>/dev/null | grep -E 's:|i:|Verify return code|Cipher'
```
<img width="666" height="42" alt="image" src="https://github.com/user-attachments/assets/dfa5ddf0-f386-4208-9e9c-8372109b6851" />

pero ``` openssl s_client -connect 192.168.56.103:443 -CAfile ~/ca/certs/ca.crt ``` si que muestra los datos 

- Paso 13 — Guardar lo hecho para la Fase 2
```
tar czf ~/ca-torrent-backup.tar.gz -C ~ ca
```

<img width="495" height="80" alt="image" src="https://github.com/user-attachments/assets/9c6e1f07-ec95-424f-a7b2-bdd32487e136" />

1.3 Preguntas de la Parte 1

1. Explica con tus palabras la diferencia entre ca.key, ca.crt, srv.key y srv.crt: ¿cuáles son secretos y cuáles públicos?

ca.key es la clave privada de la autoridad certificadora, tienen que ser secreta y es la que permite que se firmen nuevos certificados

ca.crt es el certificado público de la autoridad certificadora, es publica y sirve para que los clientes puedan verificar si esta firmado con la clave privada

srv.key es la clave privada del servidor web, esta tiene que ser privada y secreta se utiliza para demostrar que el servidor es quien dice ser

srv.crt esta es el certificado público del servidor web, sirve para comprobar que el servidor que afirma ser con una clave privada es la autentica firma, esta es publica 

| Archivo | Tipo | ¿Es secreto? |
|---|---|---|
| ca.key | Clave privada de la CA | Sí |
| ca.crt | Certificado público de la CA | No |
| srv.key | Clave privada del servidor | Sí |
| srv.crt | Certificado público del servidor | No |

3. Alguien roba ca.key (sin contraseña). ¿Qué puede hacer con ella? ¿Qué tendría que hacer Torrent S.L. para recuperar la confianza?
Podría fabricar certificados privados falsos, eliminar los certificados falsos, crear una ca.key nueva para volver a actuar como CA con su nueva ca.key y ca.crt

4. ¿Por qué descargar ca.crt por HTTP y no verificar la huella es un riesgo? ¿Quién podría aprovecharlo?
Porque HTTP no cifra los datos durante la descarga, y podrían ser manipulados y cambiar el certificado real por uno falso

5. ¿Es una buena idea que la CA y el servidor web estén en la misma máquina? ¿Qué problema de seguridad crea?
No, es un riesgo por que si logran acceder al servidor web también lograrían acceso a la CA, y con eso podría generar certificados falsos 

## PARTE 2 Un certificado real y gratuito con Let’s Encrypt

Paso 1 — Crear tu subdominio en DuckDNS

<img width="1236" height="456" alt="image" src="https://github.com/user-attachments/assets/8047dc4a-76d4-4f6d-a4ea-634cbb922c7e" />

```
getent hosts NOMBRE.duckdns.org

```
<img width="620" height="81" alt="image" src="https://github.com/user-attachments/assets/a41d5069-f57b-4e06-9b87-fe9b3b8e2222" />

Paso 2 — Instalar acme.sh (leyendo antes lo que se va a ejecutar)

```
sudo -i
curl -o get-acme.sh https://get.acme.sh
less get-acme.sh
sh get-acme.sh email=TU_CORREO
```

<img width="597" height="125" alt="image" src="https://github.com/user-attachments/assets/5565b8bd-2bb2-4010-9d02-933f5756f901" />
Crea el directorio ~/.acme.sh, donde se almacenan los archivos y configuraciones necesarios para el funcionamiento de acme.sh.

Configura una tarea programada en cron para ejecutar automáticamente el proceso de comprobación y renovación de los certificados.

<img width="713" height="163" alt="image" src="https://github.com/user-attachments/assets/22f570af-17ac-4e18-b6e6-1e5c526bd828" />

Paso 3 — Elegir Let’s Encrypt como autoridad
```
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt

```
<img width="761" height="91" alt="image" src="https://github.com/user-attachments/assets/52105db6-1431-43f8-b195-b32e3965fc36" />

<img width="515" height="92" alt="image" src="https://github.com/user-attachments/assets/57e6d467-a94c-4140-b0b8-0bb8bfe6dbab" />

Paso 4 — Pedir el certificado (desafío DNS-01)
```
export DuckDNS_Token="PEGA_AQUI_TU_TOKEN"
~/.acme.sh/acme.sh --issue --dns dns_duckdns -d NOMBRE.duckdns.org
```

Paso 5 — Instalar el certificado donde Apache pueda usarlo
```
mkdir -p /etc/ssl/torrent-le

~/.acme.sh/acme.sh --install-cert -d NOMBRE.duckdns.org \
 --key-file /etc/ssl/torrent-le/privkey.pem \
 --fullchain-file /etc/ssl/torrent-le/fullchain.pem \
 --reloadcmd "systemctl reload apache2"

chmod 600 /etc/ssl/torrent-le/privkey.pem
```

<img width="766" height="306" alt="image" src="https://github.com/user-attachments/assets/b5512b85-fe8b-4fbf-a1af-8cf9215e9fc3" />

<img width="762" height="158" alt="image" src="https://github.com/user-attachments/assets/96957734-f5bd-4e58-86ad-4fc863b5480f" />


Paso 6 — Cambiar Apache al certificado de Let’s Encrypt

```
cp /etc/apache2/sites-available/default-ssl.conf /root/default-ssl.conf.ca-propia

nano /etc/apache2/sites-available/default-ssl.conf

# Hay que cambiar estas dos lineas
SSLCertificateFile /etc/ssl/torrent-le/fullchain.pem
SSLCertificateKeyFile /etc/ssl/torrent-le/privkey.pem

sudo apache2ctl configtest

sudo systemctl reload apache2
```
<img width="633" height="227" alt="image" src="https://github.com/user-attachments/assets/1acc708f-a564-41d7-bb68-e03468bcf7af" />

<img width="440" height="160" alt="image" src="https://github.com/user-attachments/assets/a5ab34db-f4ab-431b-94ea-f949652b9456" />


Paso 7 — Comprobar el resultado
```
curl -vI https://NOMBRE.duckdns.org 2>&1 \
 | grep -iE 'subject:|issuer:|expire|verify|HTTP/'
```

<img width="753" height="215" alt="image" src="https://github.com/user-attachments/assets/604b6768-0f60-438b-8aa8-cf23f833223a" />

Paso 8 — Mirar la cadena y la lista de CA de confianza
```
openssl s_client -connect NOMBRE.duckdns.org:443 \
 -servername NOMBRE.duckdns.org </dev/null 2>/dev/null \
 | grep -E ' s:| i:|Verify return code'

ls /etc/ssl/certs | grep -i isrg
```

<img width="710" height="283" alt="image" src="https://github.com/user-attachments/assets/6307d552-5b68-41ab-9a2a-3674be398608" />

<img width="776" height="122" alt="image" src="https://github.com/user-attachments/assets/3d75829e-3042-4df5-ae7e-b60216180c7c" />

Paso 9 — La renovación automática

```
crontab -l | grep acme

~/.acme.sh/acme.sh --list

```
2.3 Comparamos las dos situaciones
| - | CA propia de Torrent S.L. | Let’s Encrypt |
| --- | --- | --- 
| ¿Quién firma el certificado? | La CA Torrent | Let's Encrypt, |
| ¿El navegador lo acepta sin hacer nada? | No, al ser un certificado auto firmado no confia | Si, es una CA de confianza |
| ¿Qué tuviste que hacer para que confiara? | Importar el certificado raíz de la CA propia en el almacén de confianza del navegador o del sistema | Nada especial: el navegador ya confía en la cadena de certificados de Let's Encrypt. |
| Validez del certificado del servidor Coste y esfuerzo de renovarlo | 365 | 90 |
| ¿Qué se comprueba antes de firmar? | Que la solicitud del servidor sea válida y que el administrador de la CA autorice su firma. | Que se demuestra el control sobre el dominio solicitado. |
| ¿Valdría para un servidor sin Internet?| 	Sí, porque la CA puede funcionar dentro de la red local. | No funcionaria por que cada revisión necesitaría salir a internet para comprobar que es el dominio que dice ser |

3. Preguntas finales

1. ¿Por qué el navegador confía en Let’s Encrypt y no en la CA de Torrent S.L.? ¿Quién decide en qué entidades confía un navegador?

Porque Let's Encrypt es una CA publica y de confianza y Torrent SL es privada y no es de confianza hasta que se indique que es de confianza, los creadores de los navegadores deciden cuales serán las CA por defecto 

2. ¿Por qué los certificados de Let’s Encrypt duran solo 90 días? Piensa en qué pasa si alguien roba la clave privada del servidor.
 Por que así en el caso de que se robe el certificado el tiempo durante el que se puede utilizar es menor y así no pueden hacerse pasar por el servidor durante más tiempo

3. La página interna de la planta de Torrent S.L. no tiene salida a Internet. ¿Qué CA usarías, la propia o Let’s Encrypt? ¿Y para la web pública de un cliente?
Para la página interna usaría la CA privada, y para la publica Let’s Encrypt para que los navegadores que quieran consultar la web publica puedan confiar en el certificado 

4. HTTPS protege la confidencialidad y la integridad de lo que viaja. Pon dos ejemplos de ataques que HTTPS no impide.
El Phishing, ese por mucho que utilices certificados no de pende de la CA, si engaña a la persona
Y otro es que descargues un malware por que el servidor de confianza a sido comprometido y han cambiado el programa que tenia que ser el original 

6. Una empresa de 3 personas, ¿montaría una CA propia? ¿Cuándo sí tiene sentido? Relaciónalo con lo que haremos en la Fase 2 (VPN).
Para una empresa de 3 personas no tiene sentido montar una CA propia, tiene sentido sí necesitas controlar muchos servicios internos, se puede relacionar con la fase 2 por que se puede utilizar la CA privada para controlar y crear los certificados de las VPN

