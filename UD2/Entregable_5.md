# Entendiendo y clasificando amenazas

## Parte A · Entendiendo las amenazas

| # | Malware | Definición | 
| - | - | - | 
| 1 | Adware | Es un software que muestra anuncios no deseados en el dispositivo | 
| 2 | Backdoor | Busca creara una manera de volver ha acceder sin tener que utilizar las maneras de autenticación  | 
| 3 | Bomba Fork | Es un ataque de denegación de servicios creando procesos hasta que colapsa la maquina | 
| 4 | Bots | Programa diseñado para realizar tareas repetitivas | 
| 5 | Bug | Es un error en el programa software | 
| 6 | Caballo de Troya | Es un software que finge ser legítimo para engañar al usuario | 
| 7 | Cookies | Es un pequeño archivo de texto generado por un sitio web y almacenado en el navegador del usuario para recordar información | 
| 8 | Crackers | Es una persona que utiliza sus conocimientos informáticos para vulnerar sistemas de seguridad  | 
| 9 | Cryptovirus, Ransomware | Es un software diseñado para secuestrar datos  | 
| 10 | Exploits | Es un fragmento de código que aprovecha vulnerabilidades | 
| 11 | Hijacker | Es un software que toma el control parcial del navegador | 
| 12 | Hoax, Joke o Bulo | Es un mensaje falso diseñado para que sea divulgado | 
| 13 | Keystroke o Keylogger | Es un software que registra las pulsaciones del teclado | 
| 14 | Ladilla virtual | Programa que causa interrupciones en el equipo | 
| 15 | Leapfrog | Técnica que salta de un sistema comprometido a otro dentro de la red  | 
| 16 | Parásito Informático | Es un software que se adhiere a archivos para ejecutar código malicioso | 
| 17 | Pharming | Es una técnica que busca redirigir a los usuarios a sitios web fraudolentos | 
| 18 | Phishing | Es una técnica de engaño mediante correos o mensajes falsos para robar información confidencial y credenciales. | 
| 19 | Rabbit o conejos | Programa diseñado para replicarse rápidamente consumiendo los recursos del sistema hasta bloquearlo.  | 
| 20 | Riskware | Programa que permite el acceso remoto si esta mal configurado | 
| 21 | Rootkit | Herramienta que oculta la presencia de malware y puertas traseras otorgando acceso con privilegios de administrador. | 
| 22 | Scumware o Escoria | Software molesto que altera la navegación o los resultados de búsqueda para mostrar publicidad engañosa. | 
| 23 | Spam | Mensajes o correos no solicitados enviados masivamente. | 
| 24 | Spyware | Software espía que recopila información privada del usuario sin su conocimiento ni consentimiento. | 
| 25 | Pop_Ups o Ventanas emergentes | Ventanas emergentes en la pantalla que se abren de forma repentina, frecuentemente usadas para publicidad agresiva. | 
| 26 | Worms o gusanos | Malware capaz de duplicarse y propagarse autónomamente por redes de ordenadores sin intervención del usuario. | 


# Parte B · Clasificar amenazas

virus · gusano · troyano · ransomware · spyware · keylogger · adware · rootkit · backdoor · bomba lógica · bot/zombi · phishing · pharming · hoax (bulo)

# Incidente en Torrent

| # | Incidente en Torrent | Tipo | ¿Se replica solo? | ¿Necesita al usuario? |
|---|---|---|---|---|
| 1 | Un socio abre factura_marzo.pdf.exe; poco después todo el disco compartido está cifrado y aparece un mensaje pidiendo 800 € | ransomware | No | Si |
| 2 | Un "optimizador gratuito" de PC que, además, envía todo lo que se teclea a un servidor externo | Keylogger | No | Si |
| 3 | Sin que nadie haga nada, un programa salta de portátil en portátil a través de la wifi del estudio | gusanos | Si | No |
| 4 | Al abrir ciertos ejecutables, éstos infectan a su vez a otros ficheros del equipo | troyano | No | Si |
| 5 | Tras instalar un editor de PDF gratuito, aparecen ventanas de publicidad constantemente | adware | No | No |
| 6 | Un antiguo colaborador dejó un código que borrará los planos el 31 de diciembre | bomba lógica | No | No |
| 7 | El ordenador del estudio está enviando spam sin que nadie lo sepa, bajo las órdenes de un servidor externo | bot/zombi | No | No |
| 8 | Un conjunto de programas modificados que esconde procesos y ficheros del intruso dentro del propio sistema | rootkit | No | No |
| 9 | Un servicio escuchando en un puerto que permite entrar al equipo sin contraseña | backdoor | No | No |
| 10 | Correo: "Tu banco necesita que confirmes tus datos en este enlace" | phishing | No | Si |
| 11 | Mensaje: "Reenvía esto a 10 contactos o tu cuenta se cerrará" | hoax (bulo) | No | Si |
| 12 | Una web idéntica a la del ERP a la que se llega porque el DNS ha sido manipulado | pharming | No | Si |
| 13 | Un programa que registra sin permiso los hábitos de navegación y los vende | spyware | No | No |

Pregunta A: ¿Cuáles de estos incidentes no son "malware" propiamente dicho, sino fraude o ingeniería social? ¿Por qué un antivirus no los detendría?



