# Contraseñas, MFA y privilegios justos

##  PARTE 1 Contraseñas robustas con PAM

### ¿Por qué PAM y no tocar /etc/shadow directamente?

### Instalar y activar pwquality

``` bash
sudo apt update

sudo apt install libpam-pwquality

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

### Caducidad y aviso con chage

- Para indicar cuanto tiempo dura la contraseña se hace con  ``` sudo chage ``` y las opciones de la tabla

| Opción | Qué hace |
| --- | --- |
| -m | Días mínimos antes de poder volver a cambiarla (evita cambiarla 5 veces seguidas para recuperar la de siempre) |
| -M | Días máximos de validez: pasado ese tiempo, caduca |
| -W | Días de aviso antes de que caduque |
| -l | Lista la política actual aplicada a un usuario |



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


### Verificación
1. Desde otra terminal (o desde el móvil con un cliente SSH), conéctate como el usuario que configuró el MFA.


2. Comprueba que primero pide la contraseña y después el código de 6 dígitos de la app.


3. Prueba a escribir un código incorrecto y comprueba que la conexión se rechaza.


### ¿A quién se lo exigimos?



