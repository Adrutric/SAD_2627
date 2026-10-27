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
| operarios | hmi_planta |  | 
| operarios | planos/ |  | 
| ingenieria | hmi_planta |  | 
| ingenieria | planos/ |  | 
| supervisores | planos/ |  |

## Tareas a realizar

#### Tarea 1 — Preparar el entorno


1. Comprueba que el soporte de ACL está disponible en tu sistema:

' $ dpkg -s acl '

2. Crea los tres grupos y los nueve usuarios del enunciado, asignando a cada usuario su grupo correspondiente.

3. Crea la estructura de ficheros: un fichero (o script) ejecutable llamado hmi_planta, y una carpeta planos/ con los ficheros autoclave.dwg, caldera.dwg y destilacion.dwg.

#### Tarea 2 — Permisos base (propietario / grupo / resto)

4. Decide qué usuario y qué grupo deben ser propietarios de hmi_planta y de planos/ (por ejemplo, un usuario de sistemas o de ingeniería).


5. Aplica con chmod unos permisos de partida restrictivos (por ejemplo 750 o 700), de forma que el resto del acceso tenga que concederse explícitamente mediante ACL.

#### Tarea 3 — Configurar las ACL con setfacl

Para cada fila que hayas completado en la tabla de la sección 3, añade la entrada de ACL correspondiente.
Recuerda la sintaxis básica:
$ setfacl -m g:grupo:rwx recurso # ACL de acceso para un grupo
$ setfacl -m u:usuario:rwx recurso # ACL de acceso para un usuario concreto
$ setfacl -R -m g:grupo:rwx planos/ # aplicar de forma recursiva a una carpeta
$ setfacl -d -m g:grupo:rwx planos/ # ACL por defecto para los ficheros nuevos

6. Aplica las ACL necesarias sobre hmi_planta.


7. Aplica las ACL necesarias sobre la carpeta planos/ y, si procede, sobre los ficheros que ya contiene.


8. Valora si alguno de los permisos del diagrama debería aplicarse también como ACL por defecto (setfacl
-d), para que los ficheros nuevos que se creen en planos/ hereden automáticamente esos permisos.


#### Tarea 4 — Verificación


9. Comprueba el resultado con getfacl sobre cada recurso:


$ getfacl hmi_planta
$ getfacl -R planos/


10.Accede como cada uno de los nueve usuarios (su -l usuario) y comprueba que puede hacer exactamente lo que el diagrama indica, y nada más.


## Entrega
• Un script (.sh) con todos los comandos ejecutados (grupos, usuarios, chmod y setfacl), comentado.
• Capturas de pantalla de la salida de getfacl para hmi_planta y para planos/.
• Las respuestas a las preguntas de la sección 6.


## Preguntas de reflexión
1. ¿Por qué no bastan los permisos clásicos de Unix (usuario / grupo / resto) para implementar el esquema del diagrama? Pon un ejemplo concreto de este caso.
