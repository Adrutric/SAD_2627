# Paso 1
| Dato encontrado | Fuente (1-6) | ¿Para qué le sirve al atacante? | Contramedida propuesta |
|---|---|---|---|
| Nombres de los empleados, sus cargos y sus direcciones de correo | 1 | Puede generar mensajes personalizados para cada empleado haciendo referencia a las acciones que hacen en la empresa | No colocar los nombres y los cargos de manera publica a en la web |
| ERP en la nube GestiCloud | 1 | Puede personalizar los correos para hacerse pasar por el proveedor | No colocar el nombre del software que se utiliza para gestionar las facturas y el resto de datos de la empresa en la pagina web |
| Colocar el nombre del portal de manera publica | 1 | Esto hace que pueda intentar entrar a ese portal sin tener que haber contactado con la empresa y buscar algún fallo en la configuración | Solo dar la información del portal a los clientes que ya hayan contratado los servicios y así tener algo de control sobre el portal |
| La versión del software con el que se creo la web | 1 | Le permite buscar vulnerabilidades sobre la versión concreta | eliminando del pie de pagina la información sombre la versión utilizada |
| Publicar las versiones desactualizadas de los softwares que utiliza la empresa para la gestión de su servidor | 2 | Buscar vulnerabilidades en concreto para los sistemas operativos desactualizados o los softwares que dan servicio | actualizar las versiones de los SO y no indicar con que software da servicio la empresa |
| NAS donde guardamos las copias de seguridad | 2 | Sabe donde tienen las copias de seguridad y en el caso de que el ataque busque cifrar los discos sabe donde tienen las copias para que no puedan utilizarlas | Dejar de utilizar un NAS solo y empezar a utilizar discos duros externos o otros dispositivos de almacenamiento que sean fácil de transportar 
| El email personal de marta a la hora de crear el dominio | 3 | permite enviar phishing haciéndose pasara por hostinge | preparar a los empleados para diferenciar los email falso de los reales |
| Configuración incorrecta del registro SPF | 4 | Es más sencillo la suplantación del dominio de la empresa mediante correos electrónicos fraudulentos, ya que el registro autoriza cualquier remitente según SPF | Configurar SPF para autorizar únicamente los servidores de correo legítimos |
| la nota adhesiva amarilla con texto legible | 5 | Puede leer el contenido de la nota el cual seguramente sea una contraseña o un usuario | eliminar notas adhsivas de la vista |
| Utilizar una red publica | 5 | le permite interceptar el trafico de red y obtener credenciales al saber desde que cafetería trabaja | utilizar una red propia como puede ser la de tu movil |
| hacer publico el empleado que se encarga de las facturas | 6 | intentar generar un phishing haciendo se pasar por un proveedor | Comprobar que los mensajes son de quien dicen ser mediante las cabeceras de los correos |

# Paso 2
Con la información que he puesto, lo que yo intentaría es ganar acceso utilizando un phishing por que si los empleados no tiene formación en seguridad informática y conociendo los datos de cada empleado y la función que ejercen dentro de la empresa es fácil crear un correo personalizado para cada uno en el que finges ser un cliente o un proveedor con algún problema en en la facturación de uno de los servicios que necesitan como puede ser el del dominio, si consigo que uno de los trabajadores caiga en el phishing y consigo las credenciales para acceder a alguna cuenta revisaría si son las misma que las que aparecen en la foto que aparece el posit y si son las mismas lo más probable es que sean las mismas para casi todo, y dependiendo de la finalidad que tuviera el ataque aquí ya tomaría unas medidas o otras para garantizarme el acceso luego.

# Paso 3
1º Formación de los empleados en seguridad informática.

Considero que es la más importante, porque al final lo más fácil de engañar para logar algo es a los empleados, y si tienen formación es más difícil que logren engañarlos.

2º Actualizar los sistemas operativos y aplicaciones que utiliza el servidor a versiones actuales

Esta hace que vulnerabilidades antiguas ya no afecten y que sea más difícil por lograr el acceso

3º  Reducir la información que debería ser privada y que aparece en la web

Haciendo eso dificultamos que los posibles atacantes obtengan información y así que sea más fácil para los empleados identificar cuando un mensaje es un phishing 
