# Configuración de ACL en Linux
##### Caso Torrent S.L. — control de acceso 

## Contexto
Torrent S.L. dispone de una aplicación de control de planta, hmi_planta, y de una carpeta compartida con los planos de los equipos industriales, planos/, que contiene los ficheros autoclave.dwg, caldera.dwg y destilacion.dwg.

El departamento de sistemas te ha encargado configurar el acceso a estos dos recursos para tres grupos de personal, siguiendo exactamente el esquema de permisos representado en el siguiente diagrama. Los permisos clásicos de Unix (propietario / grupo / resto) no son suficientes para representar este esquema: necesitarás Listas de Control de Acceso (ACL).

## Diagrama de permisos a implementar

<img width="450" height="322" alt="image" src="https://github.com/user-attachments/assets/6e844c7e-a596-48fb-a5e6-17c7f54e85dc" />

Lee con atención el diagrama: cada flecha indica qué grupo (o usuario) necesita qué permiso (leer / escribir / ejecutar) sobre cada recurso (hmi_planta o la carpeta planos/ y su contenido). Antes de tocar la terminal, completa con tu equipo la tabla de la sección siguiente.

## Usuarios y grupos

| Grupo | Usuarios |
| --- | --- |
| operarios | alberto, oscar, hugo |
| ingenieria | nestor, victor, lucia |
| supervisores | carlos, mary, john |


Tabla de permisos (complétala a partir del diagrama)

r = leer · w = escribir · x = ejecutar. Deja en blanco lo que el diagrama no indique.

| Grupo | Recurso | Permisos | rwx |
| --- | --- | --- | --- |
| operarios | hmi_planta | Ejecución | --x 
| operarios | planos/ | Nada | ---
| ingenieria | hmi_planta | Nada | --- 
| ingenieria | planos/ | Nada | ---
| supervisores | planos/ | Lectura, ejecución | r-x

## Tareas a realizar

#### Tarea 1 — Preparar el entorno


1. Comprueba que el soporte de ACL está disponible en tu sistema:

```bash
dpkg -s acl
```
<img width="615" height="439" alt="image" src="https://github.com/user-attachments/assets/dc4a6c77-a48e-43cc-8678-a34b0d85f15f" />

---

2. Crea los tres grupos y los nueve usuarios del enunciado, asignando a cada usuario su grupo correspondiente.
```bash
# Creación de usuarios:
useradd – m usuario
```
<img width="437" height="206" alt="image" src="https://github.com/user-attachments/assets/ee0f3010-1f98-454f-9600-cce5ca4e7256" />

---

```bash
# Creación de los grupos
groupadd grupo
```
<img width="476" height="70" alt="image" src="https://github.com/user-attachments/assets/2eece026-ee86-440e-b82e-c83e100e83ae" />

---

```bash
# Añadir usuario a grupo
usermod -a -G grupo usuario
```
<img width="594" height="205" alt="image" src="https://github.com/user-attachments/assets/c656ae8f-939c-44bd-bada-0f169c8e7e5e" />

---

3. Crea la estructura de ficheros: un fichero (o script) ejecutable llamado hmi_planta, y una carpeta planos/ con los ficheros autoclave.dwg, caldera.dwg y destilacion.dwg.

Estructura de directorios
```
Torrent-SL |
           | ---- > hmi_planta
           | ---- > planos/|
                           | ---- > autoclave.dwg
                           | ---- > caldera.dwg
                           | ---- > destilacion.dwg
```
Como crear la estructura :
```bash
#Propietario root y se dan permisos con ACL a los grupos y usuarios

sudo su   # Para que al crearlos el propietario sea root

mkdir Torrent-SL Torrent-SL/planos Torrent-SL/planos/autoclave.dwg Torrent-SL/planos/caldera.dwg Torrent-SL/planos/destilacion.dwg

# hmi_planta = script con un echo

# Contenido del script hmi_planta.sh

nano Torrent-SL/hmi_planta.sh

#!/bin/bash
echo " Hola usuario " 

```

<img width="441" height="294" alt="image" src="https://github.com/user-attachments/assets/c120c5af-9849-41ff-80aa-f11d87315bcf" />

---

#### Tarea 2 — Permisos base (propietario / grupo / resto)

4. Decide qué usuario y qué grupo deben ser propietarios de hmi_planta y de planos/ (por ejemplo, un usuario de sistemas o de ingeniería).

Grupos: 

Operarios --x hmi_planta

Ingenieria No permisos definidos por grupo

Supervisores r-x planos/

Usuarios: 

alberto r-- autoclave.dwg

oscar r-- caldera.dwg

nestor r-x hmi_planta

victor rwx planos/

lucia rwx hmi_planta

lucia rw- autoclave.dwg

---

5. Aplica con chmod unos permisos de partida restrictivos (por ejemplo 750 o 700), de forma que el resto del acceso tenga que concederse explícitamente mediante ACL.

```bash
# chmod -R 700 "ruta al directorio"

# -R De manera recursiva

```

<img width="570" height="114" alt="image" src="https://github.com/user-attachments/assets/e5534a94-651d-4efd-9c30-83a63dc8d251" />


#### Tarea 3 — Configurar las ACL con setfacl

Para cada fila que hayas completado en la tabla de la sección 3, añade la entrada de ACL correspondiente.
Recuerda la sintaxis básica:
``` bash
setfacl -m g:grupo:rwx recurso # ACL de acceso para un grupo

setfacl -m u:usuario:rwx recurso # ACL de acceso para un usuario concreto

setfacl -R -m g:grupo:rwx planos/ # aplicar de forma recursiva a una carpeta

setfacl -d -m g:grupo:rwx planos/ # ACL por defecto para los ficheros nuevos

```

---

6. Aplica las ACL necesarias sobre hmi_planta.
``` bash
setfacl -m g:Operarios:--x hmi_planta.sh
setfacl -m u:nestor:r-x hmi_planta.sh
setfacl -m u:lucia:rwx hmi_planta.sh
```

<img width="614" height="166" alt="image" src="https://github.com/user-attachments/assets/cd34ba80-33c4-4243-bc73-eacf54e42626" />

---

7. Aplica las ACL necesarias sobre la carpeta planos/ y, si procede, sobre los ficheros que ya contiene.

```bash
setfacl -R -m g:Supervisores:r-x planos/
setfacl -R -m u:victor:rwx planos/
setfacl -m u:lucia:rw- planos/autoclave.dwg
setfacl -m u:alberto:r-- planos/autoclave.dwg
setfacl -m u:oscar:r-- planos/caldera.dwg

```

<img width="624" height="264" alt="image" src="https://github.com/user-attachments/assets/cd9704e3-4487-4715-a1a0-d705ed71fb79" />

---

8. Valora si alguno de los permisos del diagrama debería aplicarse también como ACL por defecto (setfacl -d), para que los ficheros nuevos que se creen en planos/ hereden automáticamente esos permisos.

El único que creo que debería heredar permisos de los archivos y directorios que se creen nuevos es Víctor 
```bash
setfacl -d -R -m u:victor:rwx planos/
```
<img width="618" height="46" alt="image" src="https://github.com/user-attachments/assets/465264bb-d605-4ab3-b9a5-c7840cd3ce9d" />


#### Tarea 4 — Verificación


9. Comprueba el resultado con getfacl sobre cada recurso:

``` bash
getfacl hmi_planta.sh

getfacl -R planos/
```

hmi_planta.sh

<img width="520" height="243" alt="image" src="https://github.com/user-attachments/assets/7bdbe0d8-f2f0-4652-be94-c2b5ab477c56" />

---

planos/

<img width="482" height="231" alt="image" src="https://github.com/user-attachments/assets/f2a10fff-3a04-4872-91b5-e513f29734c1" />

<img width="323" height="465" alt="image" src="https://github.com/user-attachments/assets/bf23a80e-ef88-4b42-a88d-13c668abad98" />

<img width="277" height="225" alt="image" src="https://github.com/user-attachments/assets/b4f8a754-f238-4785-8e24-b59a89a9e948" />

---

10. Accede como cada uno de los nueve usuarios (su -l usuario) y comprueba que puede hacer exactamente lo que el diagrama indica, y nada más.




## Preguntas de reflexión
1. ¿Por qué no bastan los permisos clásicos de Unix (usuario / grupo / resto) para implementar el esquema del diagrama? Pon un ejemplo concreto de este caso.

Porque los permisos de grupo restringen a los usuarios que si que deberían poder acceder a algunos recursos en concreto

2. ¿Qué es la máscara (mask) de una ACL y cómo afecta a los permisos efectivos de un usuario o grupo, aunque su entrada de ACL indique más permisos?


3. ¿Qué diferencia hay entre una ACL de acceso y una ACL por defecto? ¿Cuándo se aplica cada una?


4. Si mañana se incorpora un nuevo operario, ¿qué pasos tendrías que repetir para darle exactamente el
mismo acceso que al resto de operarios?



## Entrega
• Un script (.sh) con todos los comandos ejecutados (grupos, usuarios, chmod y setfacl), comentado.
• Capturas de pantalla de la salida de getfacl para hmi_planta y para planos/.
• Las respuestas a las preguntas de la sección 6.


