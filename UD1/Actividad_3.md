# Práctica UD1 — Auditoría de vulnerabilidades con Nessus
### Caso: Metasploitable2

# Informe de auditoría de vulnerabilidades 

## 1. Contexto y alcance

Se ha analizado el servidor de la empresa Torrent S.L, para comprobar las vulnerabilidades que tiene y así hacer una auditoria y dar soluciones a las vulnerabilidades que se encuentren para reducir el riesgo en caso de amenazas


## 2. Metodología
(Herramienta usada, tipo de escaneo, objetivo analizado)
La herramienta que se ha utilizado es Nessus, el escaneado es el tipo básico y el objetivo es encontrar vulnerabilidades que tenga la maquina Metasploitable2 que es la que hace de servidor 


## 3. Resumen de resultados
(Cuántas vulnerabilidades por severidad — puedes usar una tabla o una lista)

| Nombre | Puntuación | Severidad |
| --- | --- | --- |
| Canonical Ubuntu Linux SEoL (8.04.x) | 10 | Critica |
| VNC Server 'password' Password | 10.0* | Critica |
| SSL Version 2 and 3 Protocol Detection | 9.8 | Critica |
| Apache Tomcat AJP Connector Request Injection (Ghostcat) | 9.8 | Critica |
| Bind Shell Backdoor Detection | 9.8 | Critica |
| Debian OpenSSH/OpenSSL Package Random Number Generator Weakness (SSL check) | 10.0* | Critica |
| Debian OpenSSH/OpenSSL Package Random Number Generator Weakness | 10.0* | Critica |
| NFS Shares World Readable | 7.5 | Alta |
| Samba Badlock Vulnerability | 7.5 | Alta |
| SSL (Multiple Issues) | van desde 7.5 hasta 3.4 | Alta / Media / Baja |
| ISC Bind (Multiple Issues) | Van desde 8.6 hasta 5.9 | Alta / Media |
| TLS Version 1.0 Protocol Detection | 6.5 | Media |
| SSL Anonymous Cipher Suites Supported | 5.9 | Media |
| SSL DROWN Attack Vulnerability (Decrypting RSA with Obsolete and Weakened eNcryption) | 5.9 | Media |
| ... | ... | ... |

En total el escanea me ha devuelto 65 vulnerabilidades 


## 4. Vulnerabilidades clasificadas

Vulnerabilidad | Severidad / CVSS | Origen | Descripción
------------ | ------------- | ------------ | -------------
Debian OpenSSH/OpenSSL Package Random Number Generator Weakness (SSL check) | Critacial / 10-0 * | Implementación  | La clave de host SSH remota se ha generado en un sistema Debian o Ubuntu que contiene un fallo (bug) en el generador de números aleatorios de su librería OpenSSL.El problema se debe a que un empaquetador de Debian eliminó casi todas las fuentes de entropía en la versión remota de OpenSSL.Un atacante puede obtener fácilmente la parte privada de la clave remota y utilizarla para descifrar la sesión remota o llevar a cabo un ataque de "hombre en el medio" (Man-in-the-Middle) 
X Server Detection | Baja / 2.6 | Uso | El equipo remoto está ejecutando un servidor X11. X11 es un protocolo cliente-servidor que se utiliza para mostrar en un cliente remoto aplicaciones gráficas que se ejecutan en un equipo determinado. Como el tráfico de X11 no está cifrado, es posible que un atacante intercepte la conexión.
SSL Anonymous Cipher Suites Supported | Media / 5.9 | Uso | El equipo remoto admite el uso de suites de cifrado SSL anónimas. Aunque esto permite a un administrador configurar un servicio que cifra el tráfico sin necesidad de generar y configurar certificados SSL, no  frece forma de verificar la identidad del equipo remoto y deja el servicio expuesto a un ataque de intermediario (Man-in-the-Middle)


## 5. Análisis en profundidad
(El desarrollo del paso 8: qué es, cómo se explota, cómo se mitiga, referencia)

### Opción 1: Debian OpenSSH/OpenSSL Package Random Number Generator Weakness Crítica

* Qué es: Es un fallo en la libreria de OpenSSL en Debian en la que se dejo de generar las claves ssh de manera aleatoria
* Cómo se explota: Conectarse al ssh y utilizando la clave publica ir a comprobarlo con la base de datos con las claves precalculadas hasta encontrar la clave privada que le corresponde 
* Cómo se mitiga: Actualizar los paquetes openssl y openssh-server 
* Referencia: CVE-2008-0166 (Nessus Plugin ID: 32314)

### Opción 2: SSL Anonymous Cipher Suites Supported Media

* Qué es: Es un fallo en la configuración que deja conectarte sin validar el certificado digital 
* Cómo se explota: Pues haciendo de man in the middel e interceptando la conexión 
* Cómo se mitiga: Reconfigurar el servicio para que valide el certificado
* Referencia: Nessus Plugin ID: 31705 / SSL Anonymous Cipher Suites Supported

### Opción 3: X Server Detection Baja

* Qué es: Un servidor gráfico (X11) que transmite los datos en texto plano 
* Cómo se explota: Conectándote a la red y capturando los paquetes 
* Cómo se mitiga: Cerrando el puerto de ese servicio
* Referencia: Nessus Plugin ID: 10407 / X Server Detection

## 6. Recomendaciones
(2-3 acciones concretas que recomendarías, ordenadas por prioridad)

## 7. Conclusión
(Una valoración breve: ¿firmarías el contrato de mantenimiento sabiendo lo que sabes ahora? ¿Con qué condiciones?)
