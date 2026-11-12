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

```
getent hosts NOMBRE.duckdns.org

```

















