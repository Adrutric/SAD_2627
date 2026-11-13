# Contraseñas, MFA y privilegios justos

##  PARTE 1 Contraseñas robustas con PAM

### ¿Por qué PAM y no tocar /etc/shadow directamente?

### Instalar y activar pwquality

``` bash
sudo apt update

sudo apt install libpam-pwquality

# Comprobar que en el archivo /etc/pam.d/common-password este esta línea activada  :

password requisite pam_pwquality.so retry=3

```

| Parámetro | Valor sugerido | Qué hace |
| --- | --- | --- |
| minlen | 12 | Longitud minima de la contraseña |
| minclass | 3 | Nº mínimo de tipos de caracteres distintos |
| dcredit / ucredit / lcredit / ocredit | -1 cada uno | Exigen al menos un dígito / mayúscula / minúscula / símbolo (el signo negativo = obligatorio)|
|dictcheck | 1 | Rechaza contraseñas basadas en palabras de diccionario (usa cracklib) |
|maxrepeat | 3 | Máximo de caracteres idénticos seguidos (evita «aaaa…») |
| enforce_for_root | (sin valor, solo activarla) | Sin esta línea, root puede saltarse toda la política |

### Práctica guiada — aplicar la política

```bash
sudo nano /etc/security/pwquality.conf  # para cambiar y aplicar los valores de la tabla

cat  /etc/pam.d/common-password  # buscar que este pam_pwquality.so

sudo passwd usuario

# poner una contraseña

# para poner una contraseña débil a root y que la acepte hay que tener esto activo enforce_for_root

```

<img width="190" height="671" alt="image" src="https://github.com/user-attachments/assets/ef787b61-8add-493c-9eb2-9f4b6ed2480e" />

<img width="177" height="82" alt="image" src="https://github.com/user-attachments/assets/3ce7eb5f-c58e-413d-b95b-c3126dd2b6db" />

<img width="210" height="71" alt="image" src="https://github.com/user-attachments/assets/7f0bf007-1572-432f-8210-00b2f3cfd2c3" />

- Ahora aplicar una contraseña a un usuario

-- En este caso ha sido ```Ubuntu2024!#```

<img width="699" height="186" alt="image" src="https://github.com/user-attachments/assets/2217b351-2f76-491d-9c41-62c559cf2b10" />

- Contraseña para root

Con enforce_for_root desactivado 

<img width="722" height="147" alt="image" src="https://github.com/user-attachments/assets/29c22c62-90bd-4e8d-9cfd-29681315845c" />

La contraseña con la que se hace la prueba es:  ```1234```

Con enforce_for_root activado 

<img width="669" height="194" alt="image" src="https://github.com/user-attachments/assets/b007fac9-065b-483a-a3b4-415e151ee65f" />


### Caducidad y aviso con chage

- Para indicar cuanto tiempo dura la contraseña se hace con  ``` sudo chage ``` y las opciones de la tabla

| Opción | Qué hace |
| --- | --- |
| -m | Días mínimos antes de poder volver a cambiarla (evita cambiarla 5 veces seguidas para recuperar la de siempre) |
| -M | Días máximos de validez: pasado ese tiempo, caduca |
| -W | Días de aviso antes de que caduque |
| -l | Lista la política actual aplicada a un usuario |

```bash
for u in nestor victor lucia carlos mary john; do
    sudo chage -m 1 -M 60 -W 7 $u
  done

for u in alberto oscar hugo; do
    sudo chage -m 1 -M 90 -W 7 $u
  done

chage -l nestor
```

<img width="777" height="442" alt="image" src="https://github.com/user-attachments/assets/c3743681-fdb1-4c8e-a0b7-f1e7ba970f78" />

- Otro usuario afectado

<img width="753" height="211" alt="image" src="https://github.com/user-attachments/assets/b9048ab0-6484-444b-a243-b84bb8458bb9" />


## PARTE 2 MFA en SSH con Google Authenticator

### Instalar el módulo PAM

```bash
sudo apt install libpam-google-authenticator
```
### Generar el secreto
<img width="824" height="686" alt="image" src="https://github.com/user-attachments/assets/e3d71021-243a-4922-af39-f85d554640c3" />

### Conectar Google Authenticator con sshd

<img width="437" height="86" alt="image" src="https://github.com/user-attachments/assets/c92fe85c-8fe0-4bea-8e39-058089248fc8" />

<img width="346" height="39" alt="image" src="https://github.com/user-attachments/assets/751aad47-1c6f-47d7-870c-fee8a6a12df5" />

<img width="148" height="41" alt="image" src="https://github.com/user-attachments/assets/87a54ef9-6add-4c22-95b2-d87db22686e5" />

<img width="305" height="68" alt="image" src="https://github.com/user-attachments/assets/bbaebe28-968e-4f38-9b07-e01134fac0eb" />


### Generar el secreto (lo hace cada usuario, con su propia cuenta)

- Iniciando sesión con un usuario que quieres que tenga la autenticación hay que ejecutar ``` google-authenticator ``` para iniciar el asistente de autentificación

| Pregunta | Respuesta recomendada | 
| --- | --- |
| ¿Tokens time-based? | y — es el estándar TOTP |
| ¿Actualizar ~/.google_authenticator? | y |
| ¿Impedir que el mismo código se use dos veces? | y — evita reutilizar un código capturado |
| ¿Ampliar la ventana de validez? | n — por defecto ya tolera pequeños desfases de reloj |
| ¿Limitar los intentos (rate-limit)? | y — 3 intentos cada 30 s, frena la fuerza bruta sobre el código |

- Después muestra un QR y una clave secreta en base32, además de 5 «scratch codes» (códigos de emergencia, de un solo uso).
<img width="759" height="139" alt="image" src="https://github.com/user-attachments/assets/e80d051b-09e9-42f0-8b49-2cac2777aa63" />

<img width="636" height="407" alt="image" src="https://github.com/user-attachments/assets/36bd52cb-7ecb-48bb-a253-cb4fb6852a51" />


-  Escanear el QR en la APP Google Authenticator o introducir la clave a mano

#### ⚠⚠⚠ Importante ⚠⚠⚠
Guardad los scratch codes en un sitio seguro. Si perdéis el teléfono y no los tenéis, la única salida es que alguien con
acceso root borre o regenere el fichero ~/.google_authenticator del usuario afectado.




### Conectar Google Authenticator con sshd

----

### Verificación
1. Desde otra terminal (o desde el móvil con un cliente SSH), conéctate como el usuario que configuró el MFA.

<img width="687" height="331" alt="image" src="https://github.com/user-attachments/assets/1bc9cd4f-3f42-4666-bd93-50091ab41506" />


2. Comprueba que primero pide la contraseña y después el código de 6 dígitos de la app.

Primero pide la contraseña del usuario y luego el código de google autenticator 

3. Prueba a escribir un código incorrecto y comprueba que la conexión se rechaza.

<img width="388" height="220" alt="image" src="https://github.com/user-attachments/assets/59a3c145-558e-483a-b021-2993572178b2" />


### ¿A quién se lo exigimos?



# PARTE 3 Privilegios justos con sudo

3.3 Práctica guiada
```bash

# 1. Crea un script de prueba /usr/local/bin/auditoria_planta.sh que simplemente muestre un mensaje, y dale permiso de ejecución:


sudo tee /usr/local/bin/auditoria_planta.sh <<'EOF'

#!/bin/bash

echo "[$(date)] Auditoría de planta ejecutada por $(whoami)"
EOF

sudo chmod 755 /usr/local/bin/auditoria_planta.sh

# 2. Crea el fichero de reglas con visudo, apuntando a un fichero nuevo en sudoers.d:

sudo visudo -f /etc/sudoers.d/torrent

# 3. Escribe dentro estas tres líneas (una por grupo; fíjate en que operarios no aparece):

%ingenieria ALL=(root) /usr/bin/systemctl restart hmi_planta, /usr/bin/systemctl status
hmi_planta
%supervisores ALL=(root) NOPASSWD: /usr/local/bin/auditoria_planta.sh

# 4. Guarda y sal: visudo valida la sintaxis automáticamente. Si hay un error, te avisa y te deja corregirlo antes de guardar — nunca lo fuerces.

```

# 1º

<img width="708" height="211" alt="image" src="https://github.com/user-attachments/assets/ae03a6ba-a044-4ccd-881f-ceb86e589ca9" />

# 2º

<img width="799" height="101" alt="image" src="https://github.com/user-attachments/assets/c1a5beb6-e81d-401f-ad02-77fd94111955" />

---

### Verificación
5. Comprueba lo que puede hacer cada usuario con ```sudo -l```:
 ```bash
 sudo -l -U nestor
 sudo -l -U carlos
 sudo -l -U alberto
```
<img width="808" height="489" alt="image" src="https://github.com/user-attachments/assets/81b7dbde-def9-4353-8f1e-3392e5063a21" />

6. Conectado como alberto (operarios), intenta ejecutar algo con sudo y confirma que se le deniega:
```bash
sudo systemctl restart hmi_planta
alberto is not in the sudoers file. This incident will be reported.
```


7. Busca ese intento en el registro del sistema:
```bash
sudo journalctl -u sudo | tail -n 20
# o, según la distribución:
sudo tail -n 20 /var/log/auth.log
```

<img width="743" height="290" alt="image" src="https://github.com/user-attachments/assets/260a093d-5401-40b9-82f4-d18fc9707216" />

8. Conectado como carlos (supervisores), ejecuta ```sudo /usr/local/bin/auditoria_planta.sh``` y comprueba que no pide contraseña.

<img width="738" height="67" alt="image" src="https://github.com/user-attachments/assets/5aaddacd-1aec-477c-b722-ad90609fb0ce" />



Preguntas de reflexión

1. Una contraseña de 20 caracteres aleatorios es, en teoría, casi imposible de reventar por fuerza bruta. ¿Qué riesgo concreto cubre el MFA que una contraseña así, por fuerte que sea, no cubre?
Que aunque se logre saber la contraseña necesite otro paso más para poder logar iniciar sesión, y entonces no es solo algo que se sino que también es algo que poseo para poder verificarse 

2. Buscad brevemente la postura actual del NIST sobre la caducidad obligatoria de contraseñas. ¿Por qué forzar el cambio cada pocos días puede llegar a ser contraproducente para la seguridad real?
Por que hace que los usuarios terminen haciendo cambios solo al final de la contraseña como podría ser la fecha del mes o de la semana 

3. Aunque sudo esté perfectamente configurado, seguís necesitando las ACL de la sesión anterior. Explicad con un ejemplo por qué una capa no sustituye a la otra.
por ejemplo un trabajador del grupo ingenieria tiene permiso mediante sudo para reiniciar el servicio hmi_planta, pero no tiene permiso para modificar el archivo /etc/planta/configuracion.conf

4. Un alumno pierde el móvil con Google Authenticator y no guardó los scratch codes. ¿Qué tendría que hacer el administrador del sistema para devolverle el acceso sin comprometer la seguridad del resto de cuentas?

1º Revocar el autenticador anterior, invalidando el secreto TOTP asociado al móvil perdido.

2º Acceder al servidor mediante una cuenta administrativa y eliminar o sustituir el archivo .google_authenticator del usuario afectado.

3º Permitir que el alumno vuelva a configurar Google Authenticator escaneando un nuevo código QR.

4º Generar y guardar nuevos códigos de recuperación (scratch codes) para evitar que vuelva a ocurrir el mismo problema.
