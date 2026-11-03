```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```

# 🛠️ Grupo 1: Los Detectives de Identidad (Saber qué IP tiene tu PC)

Estas herramientas sirven para interrogar a tu propia tarjeta de red. Te dicen qué dirección te dio el router y si tu configuración está bien construida.

## 📛 **Comando: Interrogar a la Tarjeta de Red (`ipconfig`)**

- **⚡ Explicación rápida y ejemplo:** Muestra toda la información de red de tu computadora real (Dirección IPv4, IPv6, Máscara de subred y tu Puerta de enlace).
    
    powershell
    
    ```
    PS C:\> ipconfig
    ```
    
    Usa el código con precaución.
    
- **🧠 La Lógica de Fondo:** Si tu PC no tiene internet, este es el comando número uno que debes ejecutar. Te permite verificar si el router de tu casa (o el servidor DHCP) te asignó una IP válida o si estás desconectado.
- **⚠️ LA ADVERTENCIA:** Si al ejecutarlo ves que tu IP empieza por `169.254.X.X`, ¡alerta roja! Eso significa **APIPA (Automatic Private IP Addressing)**. Tu PC no pudo comunicarse con el router para pedirle una IP, lo que significa que tienes el cable desconectado, el Wi-Fi caído o el servidor DHCP del router está apagado.

## 📛 **Comando: El Inspector Exhaustivo (`ipconfig /all`)**

- **⚡ Explicación rápida y ejemplo:** Es el hermano mayor de `ipconfig`. Muestra lo mismo, pero añade detalles ultra específicos como la dirección MAC (la cédula física de tu tarjeta) y los servidores DNS que estás usando.
    
    powershell
    
    ```
    PS C:\> ipconfig /all
    ```
    
    Usa el código con precaución.
    
- **🧠 La Lógica de Fondo:** Úsalo cuando necesites saber a qué servidores DNS (como los de Google `8.8.8.8`) está consultando tu PC para traducir las páginas web, o cuando una red con seguridad por hardware te pida tu dirección MAC física para darte acceso.
- **⚠️ LA ADVERTENCIA:** Nunca compartas capturas de pantalla completas de tu `ipconfig /all` en internet con desconocidos. Revelar tus direcciones MAC físicas y configuraciones internas puede dar pistas a un atacante para clonar la identidad de tu máquina en redes locales (MAC Spoofing).

---

# 🚀 Grupo 2: Los Rastreadores de Conectividad (Probar si hay internet)

Sirven para enviar "pulsos" de datos a través de los cables y routers del mundo para medir si el camino está libre o bloqueado.

## 📛 **Comando: El Radar de Red (`ping`)**

- **⚡ Explicación rápida y ejemplo:** Envía 4 pequeños paquetes de prueba (llamados ecos ICMP) a un destino para ver si está encendido y cuánto tiempo tarda en responder en milisegundos.
    
    powershell
    
    ```
    PS C:\> ping google.com
    ```
    
    Usa el código con precaución.
    
- **🧠 La Lógica de Fondo:** Es la forma más rápida de saber si hay comunicación básica entre dos puntos. En tu laboratorio de HSRP, harías ping a la IP virtual para verificar si el "Router Fantasma" está respondiendo.
- **⚠️ LA ADVERTENCIA:** Muchos servidores modernos y routers empresariales tienen bloqueado el protocolo ICMP por seguridad para evitar ataques. Si haces ping a una IP empresarial y dice _'Tiempo de espera agotado' (Request timed out)_, no siempre significa que esté caída; podría ser que el router tiene órdenes de ignorar tus pings por seguridad.

## 📛 **Comando: El Ping Infinito de PowerShell (`ping -t`)**

- **⚡ Explicación rápida y ejemplo:** En el CMD tradicional de Windows usas `ping -t`, pero **en PowerShell el comando equivalente para hacer un ping infinito** es agregarle el parámetro `-t` (que invoca al ejecutable clásico de Windows) para enviar paquetes sin detenerse hasta que presiones `Ctrl + C`.
    
    powershell
    
    ```
    PS C:\> ping.exe -t 8.8.8.8
    ```
    
    Usa el código con precaución.
    
- **🧠 La Lógica de Fondo:** Este es el comando exacto que necesitas para probar tu HSRP en la vida real. Lo dejas corriendo en la pantalla mientras desconectas físicamente el cable del router principal. Verás cómo fallan uno o dos paquetes y de inmediato el ping continúa porque el router de respaldo tomó el control.
- **⚠️ LA ADVERTENCIA:** Dejar un ping infinito hacia un servidor pequeño o residencial de alguien más durante horas puede ser considerado un intento rudimentario de ataque DoS (Denegación de Servicio). Úsalo solo para pruebas controladas en tus propios equipos o IPs públicas estables como las de Google.

---

# 🗺️ Grupo 3: Los Cartógrafos de Internet (Ver la ruta del paquete)

Sirven para rastrear el mapa geográfico y lógico por donde viaja la información a través de los continentes.

## 📛 **Comando: El GPS de los Paquetes (`tracert`)**

- **⚡ Explicación rápida y ejemplo:** Te muestra una lista detallada de **cada router (salto)** que toca tu paquete desde que sale de tu PC real hasta que llega a su destino en cualquier parte del mundo.
    
    powershell
    
    ```
    PS C:\> tracert 8.8.8.8
    ```
    
    Usa el código con precaución.
    
- **🧠 La Lógica de Fondo:** Sirve para saber exactamente en qué parte de la infraestructura de internet se está perdiendo la conexión. Si tu internet falla en el salto número 1, el problema es el router de tu casa. Si falla en el salto 5, el problema es de tu proveedor de internet (Claro, Movistar, Tigo, etc.).
- **⚠️ LA ADVERTENCIA:** En PowerShell existe un comando nativo llamado `Test-NetConnection -TraceRoute`, pero la herramienta clásica `tracert` sigue siendo la reina por velocidad. Nota que algunos routers intermedios de internet no responderán con su nombre sino con asteriscos `* * *`, esto es normal ya que ocultan su identidad por políticas de ciberseguridad para no revelar la estructura de su red central.

⚡ **Explicación rápida y ejemplo:**  
El comando `tracert` utiliza un protocolo llamado **ICMP** (Internet Control Message Protocol), el cual vive inyectado directamente en la **Capa 3 (Red)**. El objetivo de esta capa es poner las direcciones IP de origen y destino, y decidir el mejor camino (enrutamiento).

Pero además, cada router por el que pasa tu paquete tiene un límite de tamaño para los paquetes que puede transportar por sus cables. Ese límite se llama **MTU (Maximum Transmission Unit)**. El estándar normal en Ethernet es de **1500 bytes**. Si tu imagen pesa 5 Megabytes, es demasiado gigante para pasar por el cable de un solo golpe. Así que el protocolo IP en la Capa 3 la corta en miles de pedacitos (paquetes pequeños de 1500 bytes o menos).

text

```
[ Foto de EE.UU. 5MB ] 
       │ (Capa 3 corta el archivo)
       ▼
[Fragmento 1 (IP)] ──> [ Router 1 ] ──> \
[Fragmento 2 (IP)] ──> [ Router 2 ] ───> [ Tu PC en Colombia ] (Une todo)
[Fragmento 3 (IP)] ──> [ Router 3 ] ──> /
```

Usa el código con precaución.

🧠 **La Lógica de Fondo:**  
¿Por qué se hace esto? Imagina que quieres enviar un camión gigante de mudanza por una carretera llena de puentes bajos y túneles estrechos; el camión se va a estrellar. La Capa 3 es inteligente: desarma los muebles, los mete en cajas numeradas que quepan por los túneles, y cada caja viaja de forma independiente.

Lo más loco de Internet es que **el fragmento 1 puede viajar por un cable submarino en el Atlántico y el fragmento 2 por un satélite o una ruta por el Pacífico**. No importa qué camino tomen en los routers del mundo; cada fragmento lleva un número de identificación en su cabecera IP. Cuando llegan a tu PC en Colombia, el sistema operativo mira los números de secuencia y vuelve a armar la foto perfecta en tu pantalla.

⚠️ **LA ADVERTENCIA:**

> **¡El peligro del agujero negro de paquetes (PMTUD Black Hole)!** Si en la ruta entre Estados Unidos y Colombia hay un router viejo o mal configurado que tiene un MTU más pequeño (por ejemplo, 1400 bytes), y tu paquete tiene activo el bit de _"No Fragmentar"_ (Don't Fragment - DF bit), ese router intermedio simplemente **tirará tu paquete a la basura**. Tu PC se quedará esperando eternamente y la imagen se quedará congelada a la mitad. ¡Esto provoca fallas misteriosas donde algunas páginas web abren pero las imágenes nunca cargan!

---