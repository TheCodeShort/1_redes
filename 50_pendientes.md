```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```

# hacer Ping a paginas 
se puede hacer ping a paginas para saber su IP 

# ICMP
El **ICMP** (_Internet Control Message Protocol_ o Protocolo de Mensajes de Control de Internet) ==es un protocolo de **Capa de Red** (Capa 3) del modelo OSI que los routers y dispositivos utilizan para enviar **mensajes de error** y diagnósticos sobre la transmisión de datos==

# Trasa 
La **traza de redes** (conocida comúnmente como _traceroute_ o _tracert_) es una ==herramienta de diagnóstico que muestra el camino exacto que siguen los paquetes de datos desde tu equipo hasta un servidor o dirección IP de destino==

# Puty 

# Hardening (o _endurecimiento_ de sistemas en español)

es el ==proceso de **asegurar un sistema informático reduciendo su superficie de vulnerabilidad**==.

Para decírtelo en palabras muy sencillas: es el equivalente a ponerle puerta blindada, alarmas, cámaras y cerrar todas las ventanas de tu casa para que a un ladrón le sea casi imposible entrar. En informática, se trata de **eliminar todos los riesgos innecesarios** de un equipo, router o servidor.

Un sistema recién instalado (de fábrica) viene configurado para ser "fácil de usar", pero eso lo hace muy inseguro. El _hardening_ cambia eso.

## Hardening en Switches y Routers (La red de tu clase)

- **Desactivar Telnet:** Como vimos que Telnet viaja sin seguridad, hacer _hardening_ significa **apagar Telnet obligatoriamente** en el equipo y activar únicamente SSH.
- **Apagar puertos que no se usen:** Si un switch de la empresa tiene 24 puertos físicos pero solo se usan 10, la buena práctica dice que debes entrar por comandos y **apagar (shutdown)** los otros 14 puertos. Así, si alguien malintencionado se infiltra en la oficina y conecta un cable, no tendrá red.
- **Cambiar las credenciales de fábrica:** Jamás dejar usuarios como `admin` y contraseña `admin` o `12345`.

## Hardening en Servidores y Computadoras

- **Eliminar programas innecesarios:** Si un servidor de la empresa solo va a guardar bases de datos, se le desinstala el navegador web, los juegos, reproductores de música y cualquier software extra. Menos programas instalados significan menos fallos de seguridad que un hacker pueda explotar.
- **Cerrar Puertos lógicos:** Bloquear con un firewall todo el tráfico que no sea estrictamente necesario para que la empresa opere.

## ¿Cómo se conecta con ITIL 4?

En ITIL, el _hardening_ es una actividad obligatoria dentro de la **Gestión de la Seguridad de la Información**.

ITIL establece que no puedes dejar la seguridad a la suerte; exige crear **Líneas Base de Configuración Segura (Configuration Baselines)**. Esto significa que antes de conectar cualquier switch o servidor a la red de producción, este debe pasar por una lista de verificación de _hardening_ firmada y aprobada por el equipo de seguridad.

## LINE CONSOLE (Línea de Consola)

Hace referencia al **acceso físico** del equipo.

- **¿Qué es?:** Es el puerto físico (generalmente de color celeste) que tiene el switch por detrás. Si tú vas físicamente al laboratorio, conectas un cable desde tu laptop a ese puerto y abres **PuTTY**, entras directamente por la "Línea de Consola".
- **¿Por qué es Hardening?:** De fábrica, si conectas ese cable, el switch te deja entrar a la pantalla negra sin pedirte nada. El paso 1 de tu profesor significa entrar a la configuración del switch (`line console 0`) y **ponerle una contraseña obligatoria**. Así, nadie que camine por la oficina física pueda conectar un cable y hackear el equipo.

## ENABLE (Modo Privilegiado)

Hace referencia al **nivel de superusuario o administrador** dentro del software.

- **¿Qué es?:** Cuando tú entras a un switch, estás en un modo básico donde casi no puedes hacer nada (modo usuario). Para poder crear VLANs, cambiar IPs o apagar puertos, tienes que escribir la palabra mágica: **`enable`**. Al presionar Enter, entras al "Modo Privilegiado" (el equivalente a _Ejecutar como Administrador_ en Windows).
- **¿Por qué es Hardening?:** Si no proteges el comando `enable`, cualquier usuario común que logre conectarse a la red podría saltar al modo administrador y borrar toda la configuración de la empresa. El paso 2 de tu profesor consiste en escribir el comando `enable secret [tu_contraseña]` para que, cada vez que alguien intente volverse administrador, el switch le exija una clave segura y encriptada.

---

# **CIDR** (Enrutamiento Inter-Dominios sin Clases).
# SFP

puertos de conexion de fibra optica 

# **Cable de Consola Cisco** o **Cable Rollover** (cable invertido) ANSI/TIA-568.

- **El cable de Ethernet de toda la vida (Gris, amarillo o negro):** Es el que usas para dar internet. Tiene dos puntas plásticas transparentes exactamente iguales (conectores RJ-45). Si miras los hilos de colores de los cables por dentro, están ordenados en el mismo orden en ambos extremos.
- **El cable de Consola Especial (El azul celeste):** Está hecho exclusivamente para configuración. Antiguamente, venía con una punta plana de color azul (conector serial DB9) para la computadora y una punta de red (RJ-45) para el router. Hoy en día, los modernos son cables planos de color azul que van de **USB** en la laptop a **RJ-45** (o Mini-USB) en el router. Su distribución interna de cables está totalmente invertida (el pin 1 de un lado se conecta al pin 8 del otro lado), por eso se llama _Rollover_.


# HSRP (Hot Standby Router Protocol)

es una tecnología genial y un pilar fundamental en las redes corporativas! Tienes toda la razón: ==**es un protocolo de redundancia**

(específicamente de Redundancia de Primer Salto o _First Hop Redundancy_)==.

Para explicártelo de forma muy sencilla: **HSRP es como tener un "router de repuesto" configurado como un gemelo para que, si el router principal se quema o se apaga, el segundo router tome el control en un segundo sin que ningún usuario de la empresa se quede sin Internet.**

1. El secreto de HSRP: El "Router Virtual"

Imagínate que en la VLAN 20 de tu empresa tienes dos routers físicos para dar más seguridad:

- **Router A (Principal):** IP `172.16.20.2`
- **Router B (Respaldo):** IP `172.16.20.3`

Si configuras HSRP, los dos routers se ponen de acuerdo y crean un **Router Virtual fantasma** en la red con una IP nueva, por ejemplo, la **`172.16.20.1`**.

2. ¿Cómo se conecta con el Gateway de las PCs?

¿Te acuerdas de que antes hablamos de que a todas las computadoras hay que ponerles una **Puerta de Enlace (Gateway)** fija?

- Con HSRP, tú le configuras a todas las PCs de la VLAN 20 el Gateway del fantasma: **`172.16.20.1`**.
- En el día a día, el Router A (que se conoce como **Active** o Activo) atiende todas las peticiones que van a esa IP virtual.
    
- El Router B (llamado **Standby**) se queda callado, escuchando al Router A. Si el Router A deja de enviar señales (porque se dañó el cable o se fue la luz), el Router B asume la IP virtual `172.16.20.1` automáticamente.
- **Para los usuarios es transparente:** Ninguna PC se da cuenta del daño porque su Gateway (`172.16.20.1`) sigue respondiendo.

# siglog 

monitor 

# SNMPv3
- **proporciona robustez en seguridad** al añadir características clave de cifrado y mecanismos de autenticación para asegurar la correcta administración de los elementos de red.

# CPE-PPL
este nombre es cuando puse un router 2911 en cisco packet treaser, dice que es un equipo que recibe el internet externos como los de claro, movistar etc.
1. **CPE (Customer Premises Equipment - Equipo en las Premisas del Cliente):**  
    En el mundo de las telecomunicaciones, "CPE" es cualquier equipo de red (como un router o un módem) que físicamente **está instalado dentro de las oficinas del cliente** (en este caso, dentro del edificio de **NexoSoft Labs**) y no en la central de la compañía de Internet (como Claro, Movistar, etc.). Al llamarlo CPE, el profesor está indicando que ese router es propiedad o está bajo el control de tu empresa.
    
2. **PPL (Principal / Principal Line):**  
    Esta abreviatura significa **"Principal"**. Como en el plano corregido determinamos que vas a tener dos cuartos de cómputo (Datacenters) para darle alta disponibilidad a tu software **SIGMA-TE**, este router en específico ha sido designado como el **enrutador principal de toda la empresa**. El encargado de recibir el internet fuerte o conectar la red principal del edificio.
    
3. Siguiendo la lógica impecable de tu profesor, cuando configures el segundo cuarto de cómputo (Datacenter de respaldo) para la redundancia de NexoSoft Labs, el segundo router de cara al internet debería llamarse de forma equivalente: **CPE-BKP** (Customer Premises Equipment - Backup) o **CPE-SEC** (Secundario). ¡Con esto vas a descrestar a tu profesor demostrando que entiendes la nomenclatura corporativa!

# CPE-BACKUP
¿Recuerdas que hace un momento te comenté que bajo las buenas prácticas corporativas el segundo router se llamaría igual pero indicando su rol de respaldo? ¡Tu profesor lo hizo exacto!

Al nombrarlo **CPE-BACKUP**, este router Cisco 2911 se convierte en la **salida de emergencia a Internet** para NexoSoft Labs.

- Si un camión pasa por la calle y rompe el cable de fibra óptica que va conectado al router principal (`CPE-PPL`), la red de forma inteligente desviará de inmediato todos los datos de tus 100 empleados hacia este router `CPE-BACKUP`.
- Gracias a esto, tus programadores no dejarán de trabajar en el código de **SIGMA-TE** ni por un segundo. Esto se conoce en ingeniería como **Redundancia y Alta Disponibilidad**.

---

# Switch Core
En el mundo de las redes profesionales, las empresas no conectan todo a un solo switch genérico. Se utiliza un diseño jerárquico (por niveles), y el **Core (Núcleo)** es el nivel más alto e importante.

1. **CORE (Núcleo):** Es el **"corazón de la autopista"** de datos de tu empresa. Su único trabajo es mover cantidades masivas de información entre los diferentes pisos y los servidores de tu software **SIGMA-TE** a la máxima velocidad posible, sin detenerse a revisar detalles menores.

# **SERVER**

## **SERVER- SYSLOG NTP** 

### SYSLOG (System Log - Registro del Sistema)
es el **diario privado o bitácora de la red**.

- Cada vez que en NexoSoft Labs pase algo importante, los routers, switches y firewalls le enviarán un mensaje instantáneo a este servidor.
- **Ejemplos:** Si alguien desconecta un cable en el Piso 3, si un switch se apaga, o si alguien intenta adivinar la contraseña del router principal (`CPE-PPL`) y falla, todo queda registrado aquí con fecha y hora exacta.
- Esto es vital para ti como dueño, porque si tu sistema **SIGMA-TE** se llega a desconectar, vas al Syslog y revisas exactamente qué equipo falló.

### NTP (Network Time Protocol - Protocolo de Tiempo de Red)

Este es el **reloj maestro ultra preciso de toda la empresa**.

- En una red con 100 computadoras, switches y routers, es un desastre total si cada equipo tiene una hora diferente (por ejemplo, que el switch del piso 2 tenga las 3:15 y el router principal las 3:18). Si hay un hackeo o un fallo, no sabrías qué pasó primero.
- El servicio **NTP** se encarga de sincronizar hasta el último milisegundo de todos los dispositivos del edificio. El servidor le dice a toda la red: _"Oigan todos, exactamente son las 14:05 con 22 segundos"_, y todos se ajustan al mismo tiempo.

🛡️ ¿Por qué tu profesor lo puso desde el inicio junto al Router y al Switch Core?

Porque bajo las buenas prácticas corporativas (**como ITIL**, que mencionamos antes), **no puedes asegurar ni administrar una red si no tienes controlado el tiempo y los registros de fallos**. Es la base de la seguridad antes de empezar a conectar a los programadores o los servidores de tu software SIGMA-TE.

## SERVER- TACACS IOT
Este servidor cumple dos roles fundamentales y avanzados que protegerán tanto la seguridad de tus equipos de red como tus futuros proyectos tecnológicos:

### **TACACS+ (Terminal Access Controller Access-Control System):** 
Es el **guardián de seguridad para los ingenieros**. Imagina que en el futuro contratas a 10 administradores de red para NexoSoft Labs. En lugar de ir equipo por equipo configurando el usuario y contraseña de cada administrador en el router principal, el switch core y el router de backup, los creas a todos **solo en este servidor**. Cuando un ingeniero intente entrar a configurar el router `CPE-PPL`, el router le preguntará a este servidor: _"Oye, ¿este usuario tiene permiso de cambiar configuraciones?"_. Si el servidor dice que sí, lo deja pasar. Centraliza y protege el acceso a tus aparatos.

### **IoT (Internet of Things - Internet de las Cosas):**

Como tu empresa desarrolla el software **SIGMA-TE** (que es para gestión de mantenimiento), es muy probable que en el futuro requieras conectar sensores inteligentes en las oficinas o fábricas de tus clientes (sensores de temperatura, alertas de fallos en máquinas, etc.). Este módulo IoT en Packet Tracer sirve para simular y conectar todos esos dispositivos inteligentes directamente con la red de tu empresa.


---

# Patch Panel (Panel de Parcheo)

Físicamente, es esa barra metálica llena de "puertos o cajitas" (llamados jacks RJ-45) que se atornilla directamente en el rack, justo arriba o abajo de los switches.

Si conectaras esos 100 cables directamente al switch (`SW-CORE`):

1. **Romperías los equipos:** Los cables que van por las paredes son rígidos y pesados. Mover el switch o acomodar el rack terminaría dañando los puertos del switch, que son carísimos.
2. **Sería un caos:** Si un empleado se cambia de oficina o un cable se rompe, tendrías que volver a pasar todo el cable rígido por la pared.

**La solución es el Patch Panel:** Los cables rígidos de las paredes llegan por detrás del Patch Panel y se quedan fijos ahí para siempre. Luego, por el frente, usas cables cortos, flexibles y baratos (llamados _Patch Cords_) para conectar el Patch Panel con el Switch. Si un empleado cambia de departamento, solo desconectas el cable corto del frente y lo pones en otro puerto. ¡Fácil, limpio y seguro!

el **Patch Panel de Cobre**, pero en el mundo de las redes profesionales existen principalmente dos materiales, y cada uno tiene un superpoder y un uso muy específico:

|Material|¿Para qué sirve? / ¿Cuándo se usa?|La Idea / Propósito en NexoSoft Labs|
|---|---|---|
|**Cobre** _(Usa conectores RJ-45 tradicionales)_|**Conexiones de corta distancia (Hasta 100 metros).** Es el que se usa para conectar los equipos del mismo piso: las computadoras de los desarrolladores, impresoras, teléfonos VoIP y los access points de Wi-Fi hacia el rack del piso.|Es económico, fácil de instalar y perfecto para la distribución horizontal (llevar el internet a las mesas de trabajo).|
|**Fibra Óptica** _(Usa conectores especiales como LC o SC)_|**Conexiones de larga distancia o alta velocidad (Más de 100 metros o entre pisos).** No transmite electricidad, transmite **pulsos de luz** a través de hilos de vidrio glass. Es inmune al ruido eléctrico.|Se usa como el **Backbone (Columna vertebral)**. En tu edificio de 4 pisos, la idea es interconectar el switch del Piso 4 con el `SW-CORE` del Piso 1 usando un Patch Panel de Fibra Óptica para que los datos vuelen sin interferencias entre pisos.|

# WLC
**Cisco 3504 Wireless LAN Controller (WLC)**, y la etiqueta lo dice todo: **Wireless-LAN-Controller(WLC)-WIFI**.

En el mundo de las redes corporativas, este equipo es el **"Director de Orquesta" de todo el Wi-Fi del edificio**.

📡 ¿Para qué sirve realmente un WLC en tu empresa?

Imagina que en tu edificio de 4 pisos vas a instalar unos 10 o 15 antenas de Wi-Fi (llamadas _Access Points_ o APs) para que tus 100 empleados puedan conectarse desde sus laptops y teléfonos en la cafetería, las salas de juntas o las oficinas.

Si no tuvieras este equipo, tendrías que ir antena por antena, piso por piso, configurando el nombre de la red y la contraseña en cada una. ¡Un dolor de cabeza total!

Con el **WLC**, la magia ocurre así:

1. **Centraliza el control:** Conectas este aparato en el rack principal junto al `SW-CORE`. Desde su pantalla, configuras las redes Wi-Fi una sola vez.
2. **Administración inteligente:** El WLC le "dicta" la configuración automáticamente a todas las antenas del edificio.
3. **Roaming empresarial (Movilidad):** Permite que si estás en una videollamada de Teams mostrando los avances de tu software **SIGMA-TE** en el Piso 4 y vas bajando por las escaleras hasta la cafetería del Piso 1, tu laptop cambie de antena automáticamente **sin que se corte el internet ni un segundo**.

🎨 Las dos redes Wi-Fi clave que debes crear aquí para NexoSoft Labs

Ya que tienes este "cerebro" inalámbrico, la mejor práctica es configurar dos señales de Wi-Fi totalmente separadas:

- **Wi-Fi Corporativo (Para empleados):** Una red ultra segura y encriptada para las laptops de tus programadores y contadores. Les da acceso directo a los servidores locales del software SIGMA-TE.
- **Wi-Fi Invitados (Para clientes):** Una red abierta o con contraseña simple para las visitas o clientes que vayan a la sala de juntas. Esta red solo da salida a internet y **bloquea por completo** que puedan ver las computadoras o el código de tu empresa.

---

