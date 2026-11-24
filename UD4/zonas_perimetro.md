# Práctica 1 · Perímetro y zonas

## Minirreto 1: coloca cada servicio en su zona

#### Zonas disponibles
| Zona | Confianza | Idea |
| --- | --- | --- |
| Internet |No confiable | Cualquiera, sin control |
| DMZ | Semiconfiable | Servicios expuestos a Internet, aislados de la LAN |
| LAN	| Confiable	| Usuarios y equipos propios |
| Zona crítica | Restringida | Activos de alto valor con pocos accesos |

Si un servicio no encaja en ninguna, créale una zona propia y defiende por qué.

#### Servicios de Torrent
1- Web corporativa pública
2- Servidor de correo
3- Pasarela VPN
4- Proxy (saliente e inverso)
5- HMI de planta y carpeta de planos
6- Autoridad de certificación (srv-ca)
7- Servidor de copias de seguridad
8- Portátiles de ingenieros y operarios
9- Wi-Fi de invitados
10- Servidor de registros y monitorización

### Qué tienes que entregar
1. Tabla de ubicación. Una fila por servicio:

| Servicio | Zona |	Justificación (una frase) |
| --- | --- | --- |
| Web corporativa pública |	DMZ | Necesita poder ser accedida desde internet y no maneja datos críticos |
| Servidor de correo |	DMZ | Necesita ser accedida desde internet para poder recibir y enviar correos |
| Pasarela VPN | DMZ | Tiene que ser accedida desde internet para poder establecer conexiones  |
| Proxy (saliente e inverso) | DMZ | Tiene que controlar los datos que llegan y se envian a internet |
| HMI de planta y carpeta de planos | Zona crítica | Solo tiene que tener acceso desde la LAN |
| Autoridad de certificación (srv-ca) |	Zona crítica |  Solo tiene que tener acceso desde la LAN |
| Servidor de copias de seguridad |	Zona crítica | Solo tiene que tener acceso desde la LAN |
| Portátiles de ingenieros y operarios | LAN | Es para los empleados |
| Wi-Fi de invitados | LAN aislada de la otra LAN | Tiene que estar separado de la LAN de empleados  |
| Servidor de registros y monitorización | Zona crítica | Solo tiene que tener acceso desde la LAN |

Para decidir, responde mentalmente a estas cuatro preguntas:

¿Necesita Internet?

¿Qué pasa si cae?

¿Qué datos maneja?

¿Quién necesita llegar a él?

2. Tabla de flujos. Qué conexiones se permiten y cuáles no entre zonas:

| Origen → destino | ¿Permitido? | Ejemplo concreto |
| --- | --- | --- |
| Internet → DMZ	| SI | Para recibir peticiones a servidores que ofrecen servicios al publico|
| Internet → LAN	| NO | No debe permitir acceso desde internet, solo las respuestas cuando la conversación la inicia desde la LAN |
| DMZ → LAN	| Puede	| Puede haber casos concretos que necesiten acceso pero de normal no  |
| LAN → DMZ	| SI | Necesita poder acceder para gestionar los servidores |
| LAN → Internet	| SI | Necesita poder salir a internet para realizar un uso normal |
3. Un diagrama de la red. Puedes dibujarlo con el bloque de Mermaid de abajo (GitHub lo muestra solo) o subir una imagen.

```mermaid
flowchart LR
    Internet --> FW1[Cortafuegos] --> DMZ --> FW2[Cortafuegos] --> LAN
```
4. Una pregunta de reflexión (3 o 4 líneas): si cae el servidor web, ¿qué puede hacer el atacante en tu diseño y qué se lo impide?

