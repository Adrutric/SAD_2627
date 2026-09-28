# Ataques de suplantación de identidad
### Grupo 2

## 1. SMTP spoofing

### Qué es
Es un correo que suplanta la identidad utilizando pshing engañando con el remitente utilizando cambios en las cabeceras

### Cómo se lleva a cabo
El protocolo SMPT fue diseñado sin protocolos de seguridad y cualquiera puede cambiarlas cabeceras 

### Qué categoría(s) de amenaza compromete
Autenticidad es la principal 

### Ejemplo o caso real
Un atacante utilizo este metodo para suplantar a un provedor y afecta a googel y facebock

### Medida de prevención
SPF DKIM DMIA

### Fuente
grupo

## 2. DNS spoofing

### Qué es
Es un ciberataque altamente engañoso en el que los hackers redirigen el tráfico web hacia servidores web falsos y sitios web de phishing. Estos sitios falsos suelen parecerse al destino previsto por el usuario, lo que facilita a los hackers engañar a los visitantes para que compartan información confidencial
### Cómo se lleva a cabo
El envenenamiento de caché DNS ocurre cuando un atacante altera los registros de un servidor DNS para vincular un sitio web legítimo a una IP falsa, logrando que los usuarios sean redirigidos a una página maliciosa.

### Qué categoría(s) de amenaza compromete
Confidencialidad: Al redirigir a la víctima a una web falsa el atacante captura credenciales, datos bancarios y personales.
Integridad: Se altera la autenticidad de la resolución de nombres y el contenido al que accede el usuario.
Disponibilidad: Puede utilizarse para bloquear o denegar el acceso a servicios legítimos redirigiendo el tráfico a servidores inexistentes o deshabilitados.

### Ejemplo o caso real

En 2015, un grupo de hackers conocido como Lizard Squad lanzó un ataque de envenenamiento DNS contra Malaysia Airlines en el que redirigían a los visitantes de la página a un sitio web falso que les animaba a iniciar sesión solo para ser recibidos por un mensaje 404 y la imagen de un lagarto.

En primer lugar, este ataque causó importantes estragos en la aerolínea, que ya venía de un año difícil en el que se perdieron dos vuelos. En segundo lugar, planteó serias dudas sobre si el grupo de hackers robó o no información personal de alguno de los usuarios que participaron en el ataque y se conectaron al sitio web falso.

### Medida de prevención
Usar HTTPS: Garantiza el cifrado de la conexión.
Servidores DNS seguros: Utilizar proveedores de confianza con protocolos de validación (como DNSSEC).
VPN

### Fuente
https://www.keyfactor.com/es/blog/what-is-dns-poisoning-and-dns-spoofing/

https://abcnews.com/Technology/malaysia-airlines-hit-lizard-squad-hack-attack/story?id=28489244

## 3. IP spoofing

### Qué es
 
### Cómo se lleva a cabo

### Qué categoría(s) de amenaza compromete

### Ejemplo o caso real

### Medida de prevención

### Fuente


## 4. Captura de cuentas de usuario y contraseñas
### Qué es
Es una vulnerabilidad activa que captura usuarios y contraseñas que lo hace mediante fallos de seguridad o phising

### Cómo se lleva a cabo
Se ejecuta mediante el uso de herramientas de capturara de cuentas y contraseñas 

### Qué categoría(s) de amenaza compromete
intercepción y suplantación de identidad 

### Ejemplo o caso real
Hace un año Netflix Appel PayPal afectadas 


### Medida de prevención
Usar contraseñas seguras y diferentes mas autentificación en dos factores 

### Fuente
Grupo 3

## Aplicado a Estudio Torrent

De los cuatro, ¿cuál creéis que sería el más plausible contra Estudio Torrent 
(wifi de oficina, ERP online, disco compartido con clientes)? Razonad la respuesta 
en 3-4 líneas, usando lo que habéis aprendido de los cuatro ataques, no solo del vuestro.
