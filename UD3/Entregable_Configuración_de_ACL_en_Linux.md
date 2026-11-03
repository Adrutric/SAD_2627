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

<img width="459" height="156" alt="image" src="https://github.com/user-attachments/assets/3ec09149-d0aa-496b-9f22-9641b483c9ae" />

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
# chmod -R 750 "ruta al directorio"

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
setfacl -m g:Operarios:r-x hmi_planta.sh
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

vale si no se pone lectura a todos no se pueden acceder a los directorios

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

Para poder acceder al directorio Torrent-SL con cual quier usuario que pertenezca a uno de estos grupos

<img width="627" height="136" alt="image" src="https://github.com/user-attachments/assets/8f2fd2d8-5e5e-4a7c-ab70-7583935e5af7" />

<img width="618" height="197" alt="image" src="https://github.com/user-attachments/assets/baced27d-324d-46c5-b4d6-4d1ce6b06e68" />


---

10. Accede como cada uno de los nueve usuarios (su -l usuario) y comprueba que puede hacer exactamente lo que el diagrama indica, y nada más.

```bash
su "usuario"
```
Alberto 
<img width="619" height="164" alt="image" src="https://github.com/user-attachments/assets/65b92fb9-14d4-4fa5-b915-9dc3d8569b00" />


Oscar 

<img width="591" height="225" alt="image" src="https://github.com/user-attachments/assets/fb2d4619-ed01-4312-8f41-bb83b51a7667" />


hugo

<img width="595" height="227" alt="image" src="https://github.com/user-attachments/assets/2ca8cbd9-be33-44ff-a7f4-6698d903c85e" />


carlos

<img width="625" height="250" alt="image" src="https://github.com/user-attachments/assets/174e8005-a179-44b9-a1b2-b1bcaaa5736c" />


nestor

<img width="536" height="120" alt="image" src="https://github.com/user-attachments/assets/e5713eda-5b51-41d8-8650-6505997e23fd" />


víctor 

<img width="577" height="165" alt="image" src="https://github.com/user-attachments/assets/55124d33-2a5a-4b0f-8578-82f44510dc98" />


john

<img width="629" height="165" alt="image" src="https://github.com/user-attachments/assets/c692b7aa-d065-4f25-99b6-a518e8744c6f" />


lucia

<img width="556" height="182" alt="image" src="https://github.com/user-attachments/assets/fbeddce0-f621-4029-b062-f05bada1b764" />


mary

<img width="553" height="166" alt="image" src="https://github.com/user-attachments/assets/b3432a2e-98f4-487a-825b-de961f1e1b0e" />


## Preguntas de reflexión
1. ¿Por qué no bastan los permisos clásicos de Unix (usuario / grupo / resto) para implementar el esquema del diagrama? Pon un ejemplo concreto de este caso.

Porque los permisos de grupo restringen a los usuarios que si que deberían poder acceder a algunos recursos en concreto, por ejemplo alberto y oscar pertenecen al mismo grupo pero no pueden hacerlo mismo 

2. ¿Qué es la máscara (mask) de una ACL y cómo afecta a los permisos efectivos de un usuario o grupo, aunque su entrada de ACL indique más permisos?

La máscara sirve para delimitar cual es el techo de los permisos que se le aplicaran a un usuario o grupo, aunque la entrada acl individual del usuario indique te tiene permisos de rwx si la mascara del archivo indica que tiene menos solo podra hacer lo que indique la máscara acl del archivo 

3. ¿Qué diferencia hay entre una ACL de acceso y una ACL por defecto? ¿Cuándo se aplica cada una?

La acl de acceso indica los permisos específicos que tendrá un archivo, mientras que las por defecto son las que se aplican por defecto a los archivos cuando se crean, las de acceso son para especificar permisos a usuarios o grupos concreto mientas que la por defecto son las que tendrán para todos desde el momento de la creación 

4. Si mañana se incorpora un nuevo operario, ¿Qué pasos tendrías que repetir para darle exactamente el mismo acceso que al resto de operarios?

crear el usuario para ese operario, añadirlo al grupo de operarios, y con setctl aplicar los permisos específicos si es que los tiene y no le basta con los que tiene su grupo 
