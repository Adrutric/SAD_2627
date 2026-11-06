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

- Paso 2 — Generar la clave privada de la CA

```
openssl genrsa -aes256 -out private/ca.key 4096
```

- Paso 3 — Crear el certificado raíz de la CA (autofirmado)
```
openssl req -x509 -new -key private/ca.key -sha256 -days 3650 \
 -out certs/ca.crt \
 -subj "/C=ES/O=Torrent S.L./CN=Torrent S.L. CA Raiz" \
 -addext "basicConstraints=critical,CA:TRUE" \
 -addext "keyUsage=critical,keyCertSign,cRLSign"
```

- Paso 4 — Inspeccionar el certificado que acabas de crear
```
openssl x509 -in certs/ca.crt -noout -subject -issuer -dates
openssl x509 -in certs/ca.crt -noout -text | grep -A1 'Basic Constraints'
```

- Paso 5 — Clave privada del servidor y petición de certificado (CSR)
```
openssl genrsa -out private/srv.key 2048
openssl req -new -key private/srv.key -out srv.csr \
 -subj "/C=ES/O=Torrent S.L./CN=hmi.torrent.local"
```

- Paso 6 — Indicar para qué nombres e IP es válido el certificado (SAN)
```
 nano srv.ext


subjectAltName = DNS:hmi.torrent.local, IP:IP_SERVIDOR
basicConstraints = CA:FALSE
keyUsage = digitalSignature, keyEncipherment
xtendedKeyUsage = serverAuth
```

- Paso 7 — La CA firma el certificado del servidor
```
openssl x509 -req -in srv.csr \
 -CA certs/ca.crt -CAkey private/ca.key -CAcreateserial \
 -out certs/srv.crt -days 365 -sha256 -extfile srv.ext

```


- Paso 8 — Verificar la cadena de confianza

```
openssl verify -CAfile certs/ca.crt certs/srv.crt
openssl x509 -in certs/srv.crt -noout -subject -issuer -ext subjectAltName
```






