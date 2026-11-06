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

-  Escanear el QR en la APP Google Authenticator o introducir la clave a mano

#### ⚠⚠⚠ Importante ⚠⚠⚠
Guardad los scratch codes en un sitio seguro. Si perdéis el teléfono y no los tenéis, la única salida es que alguien con
acceso root borre o regenere el fichero ~/.google_authenticator del usuario afectado.


### Conectar Google Authenticator con sshd

----

### Verificación
1. Desde otra terminal (o desde el móvil con un cliente SSH), conéctate como el usuario que configuró el MFA.


2. Comprueba que primero pide la contraseña y después el código de 6 dígitos de la app.


3. Prueba a escribir un código incorrecto y comprueba que la conexión se rechaza.


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

---

### Verificación
5. Comprueba lo que puede hacer cada usuario con ```sudo -l```:
 ```bash
 sudo -l -U nestor
 sudo -l -U carlos
 sudo -l -U alberto
```

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
8. Conectado como carlos (supervisores), ejecuta ```sudo /usr/local/bin/auditoria_planta.sh``` y comprueba que no pide contraseña.

