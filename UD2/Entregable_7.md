# Práctica: malware en un entorno controlado, y cómo detectarlo y eliminarlo

CE a) Clasificar los principales tipos de amenazas lógicas. 

CE d) Analizar amenazas, ataques y software malicioso en entornos de ejecución controlados. 

CE e) Implantar aplicaciones específicas para la detección de amenazas y la eliminación de software malicioso.

---

## Parte A · Hecha en Entregable 5
----
## Parte B · Preparar un entorno controlado (CE d)

Antes de analizar nada sospechoso, se prepara el laboratorio de análisis. Hacedlo en este orden:

Apagad Torrent-Auditor. En VirtualBox, comprobad que no tiene carpetas compartidas ni portapapeles/arrastrar y soltar habilitados con el equipo anfitrión.
Haced un snapshot con el nombre limpio-antes-malware.

Dejad el adaptador NAT activo solo durante la Parte D (instalar ClamAV y actualizar firmas). Se desactiva antes de ejecutar el programa sospechoso (Parte F).
Rellenad en vuestro documento:

| Medida de aislamiento | ¿Hecho? | ¿Qué riesgo evita? |
| --- | --- | --- |
| Sin carpetas compartidas ni portapapeles | Sí | Que no pueda utilizar las carpetas compartidas ni el portapapeles para saltar a la maquina real  |
| Snapshot previo | Sí | Que en caso de que falle algo pueda recuperar la maquina al esta de antes de iniciar la practica |
| Sin red (o red interna aislada) al ejecutar | Sí | Que no se pueda expandir a otros equipos que esten en la red |
| Máquina que no contiene datos personales ni credenciales | Sí | Que no pueda robar datos personales ni credenciales  |

----

## Parte C · El fichero EICAR: una amenaza de mentira (CE e)

EICAR es una cadena de texto estándar de 68 caracteres que todos los antivirus detectan como si fuera un virus. No hace nada, pero permite comprobar que el antivirus funciona.

En el navegador del anfitrión abrid la página oficial de EICAR (https://www.eicar.org, sección Anti-Malware Testfile). Localizad la cadena de texto de prueba.

Copiadla en un fichero de la VM:

nano ~/eicar.txt     # pegad la cadena, guardad y salid

ls -l ~/eicar.txt

Si vuestro antivirus del anfitrión salta, es lo esperado: está haciendo su trabajo. Pegadla solo dentro de la VM.

Calculad el hash y buscadlo (solo el hash, nunca subáis ficheros desconocidos) en https://www.virustotal.com:
sha256sum ~/eicar.txt

Anotad: ¿cuántos motores lo detectan?


---

# SESIÓN 2
---

## Parte D · Instalar y usar ClamAV (CE e)

### D1. Instalación (con NAT activo):

sudo apt update

sudo apt install -y clamav

### D2. Actualizar las firmas (la "base de datos de virus"). Si falla con un mensaje de bloqueo, parad antes el servicio automático:

sudo systemctl stop clamav-freshclam
sudo freshclam
sudo systemctl start clamav-freshclam
Anotad la fecha de la base de datos: clamscan --version.

### D3. Escanear el fichero EICAR:

clamscan ~/eicar.txt

Veréis una línea terminada en FOUND y un resumen. Anotad el nombre con el que ClamAV bautiza la amenaza y el número de ficheros infectados.

### D4. Escanear una carpeta entera, mostrando solo lo infectado:

clamscan -r -i -v ~

Opciones útiles:


| Opción | Qué hace |
| --- | --- |
| -r | Recursivo |
| -i | Muestra solo ficheros infectados |
| -v | Modo verboso |
| --move=DIR | Mueve los infectados a una carpeta (cuarentena) | 
| --remove | Borra los infectados (peligroso con falsos positivos) |
| -l | fichero.log	Guarda un registro |

### D5. Cuarentena en vez de borrar:

mkdir -p ~/cuarentena

clamscan -r -i -v --move=$HOME/cuarentena ~

ls -l ~/cuarentena


Pregunta D: ¿Por qué suele ser mejor mover a cuarentena que borrar directamente? ¿Qué pasaría si ClamAV marcara por error un fichero importante del ERP de Torrent?



### D6. Detectar rootkits (herramienta complementaria, también de la familia "paliativa"):

sudo apt install -y rkhunter

sudo rkhunter --update

sudo rkhunter --propupd

sudo rkhunter --check --sk --rwo

--propupd guarda una referencia (los hashes de los ficheros importantes). Es la misma idea del hash del sistema de la fase 4.

--rwo muestra solo avisos (warnings).

El informe completo queda en /var/log/rkhunter.log.

Ojo: rkhunter suele dar falsos positivos en un sistema recién instalado. Anotad cuáles salen y razonad si son preocupantes o no.

---

## Parte E · Análisis estático: mirar sin ejecutar (CE d)

Ya sabéis que el análisis estático examina un fichero sin ejecutarlo. Probadlo con el fichero que os dará el profesor, sospechoso.sh:

file sospechoso.sh

sha256sum sospechoso.sh

cat sospechoso.sh

Contestad por escrito, antes de ejecutarlo:

¿Qué crees que hace cada línea?


¿Crea ficheros? ¿Abre puertos? ¿Modifica algo para sobrevivir a un reinicio?


Si fuera un .exe binario, ¿qué habría que hacer en su lugar? (Pista: no se puede leer, habría que usar herramientas como strings o un desensamblador.)

---

## Parte F · Análisis dinámico: observar qué hace (CE d)

Desactivad el adaptador NAT de la VM (o pasadla a red interna sin otros equipos).

Haced un snapshot: antes-de-ejecutar.

Tomad una foto del estado inicial (guardad la salida en ficheros para comparar luego):

ps aux > ~/antes_procesos.txt

ss -tulpn > ~/antes_puertos.txt

crontab -l > ~/antes_cron.txt 2>&1

ls -la /tmp > ~/antes_tmp.txt

Ejecutad el programa como usuario normal (no como root):

bash sospechoso.sh

Repetid la foto y comparad:

ps aux > ~/despues_procesos.txt

ss -tulpn > ~/despues_puertos.txt

crontab -l > ~/despues_cron.txt 2>&1

ls -la /tmp > ~/despues_tmp.txt


diff ~/antes_procesos.txt ~/despues_procesos.txt

diff ~/antes_puertos.txt ~/despues_puertos.txt

diff ~/antes_cron.txt ~/despues_cron.txt

diff ~/antes_tmp.txt ~/despues_tmp.txt

Rellenad el informe de comportamiento:

| Qué hace |	Evidencia (comando y salida) | ¿Qué fase del ataque recuerda? |
| --- | --- | --- |
|Crea ficheros o carpetas |  |	
| Abre  algún puerto o proceso |  |
| Intenta persistir (sobrevivir a un reinicio) |  |

Comprobad la persistencia: reiniciad la VM y volved a mirar si el comportamiento reaparece.

Erradicación: documentad cómo lo eliminaríais (parar el proceso, quitar la entrada de arranque, borrar los ficheros) y verificad con los mismos comandos del paso 3 que ha desaparecido. Después podéis volver al snapshot.

Pregunta F: Habéis encontrado el comportamiento comparando "antes" y "después". ¿Cuál es el equivalente en un servidor real de Torrent si nadie hizo la foto inicial? ¿Qué relación tiene con el inventario de servicios que hicisteis en la práctica anterior?


---

## Parte G · Preventivo o paliativo (CE c, e)

Clasificad cada medida:



| Medida | Preventiva | Paliativa |
| --- | --- | --- |
| Mantener el sistema actualizado	|  |  |
| ClamAV escaneando a demanda un disco |  |  |
| Descargar software solo de repositorios oficiales |  |  |
| Copia de seguridad semanal desconectada de la red	|  |  |
| Cuenta de usuario sin privilegios de administrador	|  |  |
| rkhunter tras sospechar una intrusión	|  |  |
| Formación del personal contra el phishing	|  |  |
