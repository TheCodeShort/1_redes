```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```

# Syslog (abreviatura de _System Logging_ o Historial del Sistema)

```c
logging host {ip_servidor}
```

Este paso es mega importante. Es el equivalente a activar las cámaras de seguridad que graban todo lo que pasa en tu base militar y mandan el video a un búnker secreto (tu servidor) para que, si el router explota o se apaga, los videos queden a salvo en el servidor.

## 📛  Desviar los Chismes de la Red al Búnker Central

⚡ **Explicación rápida y ejemplo:** Le ordenas al router o switch que deje de guardar los historiales de eventos y errores solo en su memoria local temporal, y que empiece a mandar una copia de cada alerta en tiempo real a tu servidor **SYSLOG** del Data Center.

```
SW-CORE(config)# logging host 172.16.2.100
```

_(Nota: En tu mapa de **NexoSoft Labs**, esa IP apunta directo a tu cuadrito azul que dice `SERVER-SYSLOG/NTP`)._

🧠 **La Lógica de Fondo:** Desarmemos el comando palabra por palabra:

- **`logging`**: Le dice al procesador: _"Prepara el sistema de alertas e historiales del sistema (los logs)"_.
- **`host`**: Avisa que la salida de esos datos va hacia un equipo externo en la red.
- **`172.16.2.100`**: La dirección IP del servidor que tiene corriendo el software de Syslog.

Cada vez que un cable se desconecte (`Interface ... changed state to down`), cada vez que alguien falle una contraseña por SSH, o cada vez que tú guardes la configuración, el switch empaqueta ese texto en un mini mensaje de red y lo dispara hacia esa IP para que quede registrado en el disco duro del servidor.

⚠️ **LA ADVERTENCIA (El peligro de la red ciega):** Por defecto, los routers y switches guardan los logs en una memoria temporal llamada _Buffer_ (`logging buffered`). Si tu equipo de red se reinicia, sufre un apagón o un atacante malintencionado lo apaga a la fuerza, **¡ese historial local se borra por completo!** Si no tienes configurado el `logging host` hacia tu servidor externo, cuando el equipo encienda de nuevo no tendrás ni la menor idea de por qué se cayó o quién lo saboteó. ¡El Syslog externo es tu única evidencia forense!


🕵️‍♂️ ¿Cómo verificar que tu equipo está enviando los chismes al servidor?

Para auditar que el motor de logs está apuntando al lugar correcto, corremos el comando de verificación:


```c
SW-CORE# show logging
```


**La Lógica:** Al ejecutarlo, verás un reporte largo en pantalla. Tienes que buscar una línea que diga: **`Logging to 172.16.2.100`**. Si dice _enabled_, significa que el switch ya tiene el megáfono listo y está disparando las alertas hacia tu servidor del Data Center.

## 📛 Expandir la Caja Negra del Dispositivo

⚡ **Explicación rápida y ejemplo:** Le asignas un espacio de memoria RAM exacto dentro del equipo para almacenar las alertas locales, garantizando que el historial sea mucho más largo antes de que se empiece a borrar lo viejo.

text

```c
SW-CORE(config)# logging buffered 20000
```

Usa el código con precaución.

🧠 **La Lógica de Fondo:** Desarmemos qué pasa dentro de la memoria del aparato:

- **`logging`**: Activa las instrucciones del sistema de reportes e historiales.
- **`buffered`**: Le dice al procesador: _"Esta orden es para el búfer, es decir, el espacio reservado dentro de la memoria RAM local del switch"_.
- **`20000`**: Este número es vital. Define el tamaño exacto del búfer medido en **Bytes**. De fábrica, Cisco suele dejar este espacio muy chiquito (por ejemplo, 4096 bytes). Al subirlo a `20000` bytes, le estás dando casi 5 veces más espacio a la memoria RAM para almacenar líneas de texto de los eventos.

⚠️ **LA ADVERTENCIA (El peligro del desbordamiento y la RAM saturada):** Aunque es una excelente práctica agrandar el búfer para tener más historial local, **nunca debes poner un número exageradamente gigante** (como ponerle millones de bytes a un switch viejito). Recuerda que este espacio se reserva directamente de la **memoria RAM dinámica (DRAM)** del equipo, que es la misma memoria que usa el switch para mover los datos de los usuarios y calcular las rutas de red. Si saturas la RAM dándosela toda a los registros de texto, el switch podría quedarse sin memoria para procesar los paquetes, se pondrá ultra lento e incluso podría colapsar y reiniciarse solo. ¡`20000` a `64000` bytes es el punto dulce y seguro!

🕵️‍♂️ ¿Cómo verificar el tamaño de tu nueva memoria de logs?

Para comprobar que el procesador aceptó la ampliación de su caja negra, corremos nuestro comando de auditoría:

```c
SW-CORE# show logging
```


**La Lógica:** En las primeras líneas del reporte, vas a buscar una frase exacta que diga: **`Buffer logging: level debugging, 20000 bytes logged`**. Ahí comprobarás que tu switch expandió su memoria interna a los 20,000 bytes exactos que le ordenaste.



##  📛 qué tan detallados y por dónde vas a escuchar los secretos del router y del switchNetwork Time Protocol)

### 📛 Paso 1: `logging console` (El megáfono del cable azul)

⚡ **Explicación rápida y ejemplo:** Le ordena al equipo que imprima todas las alertas y notificaciones en vivo directamente en la pantalla negra de la consola mientras estás conectado con tu cable físico.

```c
SW-CORE(config)# logging console
```

_(Nota: En la mayoría de los equipos Cisco viene encendido de fábrica, pero se pone explícitamente para asegurar que el canal esté abierto)._

🧠 **La Lógica de Fondo:** Este comando le dice al procesador: _"Si pasa algo en el switch, escúpelo de inmediato en la pantalla de la terminal del tipo que está conectado por el puerto de Consola"_. Así, si desconectas un cable, te enteras en el segundo exacto porque te aparece un texto flotante en la pantalla.

⚠️ **LA ADVERTENCIA:** Si estás trabajando por **SSH** de forma remota en lugar de usar el cable de consola físico, ¡este comando no te mostrará nada en tu pantalla! Para ver los logs flotantes por SSH, tendrás que usar un comando mágico en modo privilegiado llamado `terminal monitor`.

### 📛 paso 2: El Interruptor Maestro de las Alertas

⚡ **Explicación rápida y ejemplo:** `logging on` es el **interruptor general de luz** para todo el sistema de bitácoras del equipo. Si este interruptor está apagado (`no logging on`), el switch se vuelve completamente sordo y mudo: no enviará nada a la consola, ni al búfer, ni al servidor Syslog.

text

```
SW-CORE(config)# logging on
```

Usa el código con precaución.

🧠 **La Lógica de Fondo:** Desarmemos la arquitectura de Cisco IOS:  
Tú puedes pasar horas configurando a dónde quieres mandar los chismes (`logging host`), qué tan detallados los quieres (`logging trap debugging`) y cuánto espacio reservar (`logging buffered 20000`). Pero todas esas configuraciones individuales se quedan "en pausa" esperando una orden superior. El comando `logging on` es el botón de **"PLAY"** o el interruptor maestro que le dice a la CPU: _"¡Ahora sí, enciende el motor de logs y empieza a aplicar todas las reglas que el ingeniero escribió!"_

.

⚠️ **LA ADVERTENCIA:** En la gran mayoría de los equipos Cisco reales y en casi todos los modelos de **Packet Tracer**, el comando `logging on` viene **encendido por defecto de fábrica**. Por eso, aunque tú no lo hayas escrito, es muy probable que tu switch ya estuviera guardando registros. **¡PERO OJO!** Si por alguna razón el sistema operativo de tu equipo vino con el motor apagado, o si otro ingeniero tiró un `no logging on` antes, tu red estará completamente ciega. Aunque tus otros comandos estén perfectos, **el equipo no registrará absolutamente nada**. ¡Por eso tu profesor lo pone explícitamente, para asegurarse al 100% de que el interruptor está arriba

### 📛 Paso 3: `logging trap debugging` y los Rangos Sagrados (0 al 7)

⚡ **Explicación rápida y ejemplo:** Le dice al switch o router **qué tan detallados** deben ser los mensajes que le va a mandar a tu servidor externo **SYSLOG** (`logging host`).

```c
SW-CORE(config)# logging trap debugging
```


🧠 **La Lógica de Fondo y los Rangos:** Cisco divide las alertas del universo en un rango matemático del **0 (Máxima Emergencia) al 7 (Chisme Completo / Diagnóstico)**. Desarmemos la escala de rangos:

- **0 - Emergency:** ¡El equipo va a explotar o el sistema colapsó! (Inusable).
- **1 - Alert:** Acción inmediata necesaria (ej: sensor de temperatura al límite).
- **2 - Critical:** Condiciones críticas (ej: falló un módulo de hardware).
- **3 - Error:** Condiciones de error (ej: una interfaz falló).
- **4 - Warning:** Advertencias (ej: algo no anda del todo bien, cuidado).
- **5 - Notification:** Eventos normales pero importantes (ej: **un puerto se encendió o se apagó**).
- **6 - Informational:** Mensajes informativos (ej: un ingeniero guardó la configuración).
- **7 - Debugging:** El nivel más alto de detalle. Muestra absolutamente todo lo que pasa dentro del procesador, bit por bit, paquete por paquete.

Cuando tú escribes `logging trap debugging`, le estás diciendo al equipo: _"Quiero que le mandes al servidor Syslog el nivel 7 **Y TODO LO QUE ESTÉ POR ENCIMA DE ÉL** (es decir, del 0 al 7)"_. ¡Le estás pidiendo el registro completo sin filtros!

⚠️ **LA ADVERTENCIA (El peligro de inundar la red):** Configurar el trap en nivel `debugging` (7) en una red real con miles de usuarios **es un peligro masivo**. El nivel 7 genera tanta información por segundo (cada paquetito que entra, cada negociación de protocolo) que puede saturar el procesador del router intentando escribir millones de líneas de texto. Además, podría inundar tu cable de red de pura basura informativa y llenar el disco duro de tu servidor **SERVER-SYSLOG** en un par de días. La buena práctica de ITIL en NexoSoft Labs dicta que en producción se debe usar el nivel **`informational` (6)** o **`notifications` (5)**, y solo activar el nivel `debugging` (7) por unos minutos si estás cazando un problema fantasma muy difícil de encontrar.



## 📛 El Historial de Auditoría de Comandos Humanos

⚡ **Explicación rápida y ejemplo:** Te metes a la configuración global del Router o del Switch y activas el servicio. A partir de ese segundo, el equipo generará una alerta de Syslog cada vez que alguien use comandos que afecten la configuración.


```c
SW-CORE(config)# logging userinfo
```

🧠 **La Lógica de Fondo:** Desarmemos qué pasa dentro de la CPU:

- **`logging`**: Invoca al motor de bitácoras del sistema operativo.
- **`userinfo`**: Le añade una regla específica: _"Registra la información del usuario cuando cambie de modo"_.  
    Por defecto, [Cisco IOS](https://software.cisco.com/download/find/ios) no avisa de forma detallada si entras o sales del modo de configuración global (`configure terminal`). Al activar `logging userinfo`, el sistema generará un mensaje automático cada vez que un usuario cambie de nivel o entre a modificar cosas. Este mensaje viaja directo a tu búfer local (`logging buffered`) y a tu servidor central (`logging host`).

⚠️ **LA ADVERTENCIA (El peligro del saboteador anónimo):** Si no activas este comando junto con el **`login local`** que configuraste al principio de la clase, el comando `logging userinfo` solo te dirá: _"Alguien entró a configurar por la línea VTY 0"_. Pero si tienes un equipo de 5 ingenieros en **NexoSoft Labs**, ¡no sabrás cuál de los 5 fue el que metió la pata! Para que este comando tenga verdadero valor legal y de auditoría bajo las normas ITIL, necesitas obligatoriamente que tus usuarios se logueen con sus nombres (`admin`, `nexo`). Así, el log dirá con precisión militar: **"El usuario `nexo` desde la IP 192.168.1.50 entró al modo de configuración global"**. ¡Ahí ya no hay forma de culpar a nadie más!


🕵️‍♂️ ¿Cómo verificar que quedó activo en tu Router o Switch?

Para confirmar que tu detective privado está trabajando en segundo plano, ejecutamos el comando de auditoría:

text

```c
SW-CORE# show logging
```


Busca en la lista de características activas una línea limpia que diga de forma explícita: **`Logging Userinfo: enabled`**. ¡Si dice _enabled_, estás totalmente cubierto!



# NTP (Network Time Protocol)

Alerta de viaje en el tiempo! 🕰️💥 Lo que acaba de detectar tu profesor es un clásico en el mundo de las redes. Cuando sacas un switch Cisco de su caja o lo enciendes en [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html), el equipo cree que vive en el año **1993**

o **1994** (la época en que se programaron esas versiones de software).

Tener la hora mal en un switch corporativo es un peligro mortal para la auditoría, porque si un hacker entra a las 3:00 PM de hoy, el switch registrará en el SYSLOG: _"El intruso entró el 1 de marzo de 1993"_. ¡Sería imposible rastrearlo en el tiempo!

**Este proceso también se hace para => Switch, Router,**

## 📛 Paso 1: `ntp server {ip}` (Buscar al Reloj Maestro)

⚡ **Explicación rápida y ejemplo:** Le das la dirección IP de un servidor de hora exacto (un Servidor NTP) para que el switch se sincronice automáticamente por red.


```c
SW-CORE(config)# ntp server {ip servidor NTP}
```


_(Nota: En tu mapa de **NexoSoft Labs**, esa IP debería ser la de tu servidor **`SERVER-SYSLOG/NTP`** que tienes en el Data Center)._

🧠 **La Lógica de Fondo:** Desarmemos el comando:

- **`ntp`**: Activa el protocolo de tiempo de red en el procesador.
- **`server`**: Le dice al switch: _"Vas a ser un CLIENTE de este servidor, vas a obedecer la hora que él te dicte"_.
- **`172.16.2.100`** _(IP de ejemplo)_: Es la ubicación del reloj atómico o servidor maestro en tu red. El switch le mandará paquetitos de red constantemente para ajustar sus segundos y minutos de forma exacta.

⚠️ **LA ADVERTENCIA:** Si configuras el servidor NTP con una IP, pero te equivocas en un solo número o el switch no tiene una ruta IP para llegar a ese servidor (recuerda que el switch necesita su `ip default-gateway` para salir de su propia red), el comando no hará nada. El switch se quedará esperando en silencio y seguirá atrapado en el año 1993. ¡Verifica siempre la conectividad con un `ping` hacia el servidor NTP antes de dar el trabajo por hecho!


## 📛 Paso 2: `ntp update-calendar` (Grabar la hora en el Reloj Físico)

⚡ **Explicación rápida y ejemplo:** Le ordena al sistema operativo que tome la hora exacta que recibió de la red y la grabe directamente en el chip del reloj interno físico (el calendario de hardware) del Switch.

```c
SW-CORE(config)# ntp update-calendar
```

🧠 **La Lógica de Fondo:** Desarmemos el comando:

- **`ntp`**: Operación bajo el protocolo de tiempo.
- **`update-calendar`**: Actualizar el chip físico (Hardware Calendar).  
    Muchos switches y routers de gama alta tienen un chip con una batería interna (como la de las tarjetas madre de PC) que mantiene la hora viva aunque el equipo se apague. Este comando asegura que la hora de red "se escriba en piedra" dentro de ese chip de hardware, manteniendo la sincronía perfecta todo el tiempo.

⚠️ **LA ADVERTENCIA:** Si olvidas activar `ntp update-calendar`, el switch tendrá la hora perfecta mientras esté encendido porque la tiene guardada en la memoria RAM temporal. Pero si el switch sufre un apagón o lo reinicias, al encender volverá a despertar desorientado en el año de nacimiento de Cisco hasta que logre comunicarse otra vez con el servidor por red. ¡Este comando es el seguro de vida del reloj!


## 3_🕵️‍♂️ ¿Cómo verificar que tu Switch ya vive en el presente?

Para comprobar que el hechizo del tiempo funcionó, sal al modo privilegiado (`exit`) y ejecuta el comando de verificación horaria:
```c
SW-CORE# show clock

SW-CORE(config)# do show clock
```

Si todo quedó perfecto, verás la fecha y hora exacta del día de hoy en lugar de los años 90. ¡Magia pura de sincronización!


# Aprovisionamiento y Asignación de Puertos de Acceso"
(o en el barrio de las redes: _"Planchar un puerto en una VLAN"_)

## 1_Asignar una Habitación Privada en el Switch
```c
SW-CORE(config)# interface GigabitEthernet 1/0/23
SW-CORE(config-if)# switchport mode access
SW-CORE(config-if)# switchport access vlan 100
SW-CORE(config-if)# description === LINK A COMPUTADORA NEXOSOFT ===

```

⚡ **Explicación rápida y ejemplo:** Te metes a la configuración de un puerto físico específico del switch, lo configuras para que reciba a una sola computadora (modo acceso), lo encierras en su respectiva VLAN (la 100) y le pones una etiqueta para saber qué hay conectado ahí.

🧠 **La Lógica de Fondo:** Desarmemos qué pasa en el procesador y el hardware palabra por palabra:

- **`interface GigabitEthernet 1/0/23`**: Le dices al switch: _"Ojo, apúntame al puerto físico Gigabit número 23"_. (La nomenclatura `1/0/23` significa: Switch del módulo 1, tarjeta 0, puerto 23).
- **`switchport mode access`**: Configura el puerto en **Modo Acceso**. Esto le ordena al hardware: _"Este puerto es exclusivo para un dispositivo final (PC, impresora, servidor). Aquí está estrictamente prohibido conectarse con otro switch"_.
- **`switchport access vlan 100`**: El comando definitivo. Mueve el puerto de la VLAN 1 (fábrica) a la **VLAN 100**. Físicamente, el switch crea una barrera eléctrica en su chip para que el tráfico de este puerto se quede aislado en su propia "habitación virtual".
- **`description ...`**: Es un comentario de texto en la memoria. No afecta cómo viajan los datos, pero le salva la vida a los ingenieros humanos para saber a dónde va ese cable sin tener que ir a buscarlo físicamente al rack.

⚠️ **LA ADVERTENCIA (El peligro del aislamiento y la VLAN fantasma):** ¡Mucho cuidado aquí, futuro CCIE! Si tú tiras el comando `switchport access vlan 100`, pero en tu switch **nunca creaste la VLAN 100** previamente (con el comando `vlan 100`), Cisco IOS intentará ayudarte creando una VLAN fantasma en ese instante. Sin embargo, si esa VLAN no está bien configurada en la base de datos o en los enlaces troncales hacia el Router, **la computadora que conectes al puerto 23 se quedará completamente sorda, muda y aislada del internet**. Podrá hablar con sus vecinos del mismo switch en la VLAN 100, pero jamás podrá salir de ahí porque nadie sabe cómo enrutar esa nueva "habitación". ¡Asegúrate siempre de que las VLANs existan en toda la red!


# Lanzar el gancho de abordaje SSH

```c
PC/Router/Switch ssh -l admin 192.168.1.254
```

⚡ **Explicación rápida y ejemplo:** Abres la consola de tu PC (o de otro router) y disparas el comando apuntando al usuario que creaste y a la IP del equipo al que te quieres conectar.

_(Nota: El profe escribió `-l` que es una **L minúscula**, no un número uno `1`. ¡Mucho ojo con ese detalle de tipeo!)._

🧠 **La Lógica de Fondo:** Desarmemos el comando en el procesador:

- **`ssh`**: Le dice a la computadora: _"Activa el protocolo Secure Shell. Vamos a encriptar todo el tráfico para que nadie nos espíe"_.
- **`-l`**: Significa **Login** (identificación). Le avisa al sistema: _"Oye, el nombre de usuario que voy a usar viene justo a continuación"_.
- **`admin`** _(o tu nombre de usuario)_: Es la credencial que el Switch receptor va a buscar en su memoria RAM (con el filtro de `username` que aprendimos hace un momento).
- **`192.168.1.254`** _(o la IP de destino)_: Es la dirección de la casa a la que vas a tocar la puerta. El sistema operativo busca esa IP en la red, viaja hasta el equipo y le dice: _"¡Abre la sesión!"_.

⚠️ **LA ADVERTENCIA:** Si ejecutas este comando y se queda la pantalla congelada en _“Connecting...”_ o te sale un error de _“Connection timed out”_, ¡tenemos un problema! Significa uno de tres errores fatales comunes:

1. Escribiste mal la IP.
2. Olvidaste encender la interfaz virtual del Switch con el comando `no shutdown`.
3. No hay un cable o una ruta que conecte físicamente tu PC con esa IP.

Si el comando sí responde pero te rebota diciendo _“Connection refused”_, la IP está bien, pero **olvidaste activar el comando `transport input ssh` en las líneas VTY**, por lo que el Switch tiene las puertas cerradas con llave.

# Ver configuraciones
## 1_Router
### 1_El Escáner de Rayos X de la Configuración
```c
Router# show running-config

Router# sh run
```

🧠 **La Lógica de Fondo:** Este comando le pide al procesador del router o switch que vuelque en pantalla el archivo de configuración activo. Aquí verás los usuarios que creaste, las contraseñas trituradas, las IPs asignadas a las interfaces y la configuración de las líneas VTY. Se lee de arriba a abajo en el orden exacto en que el sistema operativo (IOS) procesa las instrucciones.

⚠️ **LA ADVERTENCIA:** ¡Esta información es mega confidencial! Si alguien ve tu pantalla mientras corres este comando y no usaste contraseñas tipo `secret` (trituradas), verán tus accesos desnudos. Además, recuerda que lo que ves aquí **está solo en la RAM**. Si el router se apaga o se va la luz en este momento, ¡todo se borrará como si no hubieras guardado partida en tu juego favorito! (Recuerda usar `copy running-config startup-config` o `write` para salvar tu progreso).

### 2_El Panel de Control de las IPs (Exclusivo de Routers)

```c
Router# show ip interface brief

Router# sh ip int br
```

⚡ **Explicación rápida y ejemplo:** Te da una lista compacta, limpia y hermosa del estado de todas las interfaces del router, sus IPs y si están encendidas o apagadas.

🧠 **La Lógica de Fondo:** Desarmemos este comando palabra por palabra:

- **`show`**: "Muéstrame..."
- **`ip`**: "...la información del protocolo IP..."
- **`interface`**: "...de mis tarjetas de red (puertos)..."
- **`brief`**: "...en un resumen corto (sin rodeos)."  
    El procesador genera una tabla rápida para que compruebes de un solo vistazo si las IPs que asignaste están bien escritas y si el cable está haciendo contacto.

⚠️ **LA ADVERTENCIA:** En la columna "Status" y "Protocol", ambas deben decir **`up` / `up`**. Si dice `administratively down`, significa que olvidaste prender la interfaz con el comando `no shutdown`. Si dice `down` / `down`, tienes un problema físico: el cable está roto, mal conectado o el equipo del otro lado está apagado.

### 3_El Inspector de Seguridad de Líneas y Usuarios

```c
Router# show users

Router# sh users
```

🧠 **La Lógica de Fondo:** Este comando interroga a las líneas VTY y a la línea de consola (CON 0) para ver qué hilos de conexión están activos. Te mostrará el nombre del usuario local que inició sesión, la dirección IP desde la que se conectó y cuánto tiempo lleva inactivo.

⚠️ **LA ADVERTENCIA:** Si corres este comando en una red real y ves un usuario o una IP sospechosa que tú no autorizaste, ¡tienes un intruso en tu base militar! Podrás usar el comando `clear line` seguido del número de línea para patearlo y cerrarle la conexión inmediatamente.

### 4_El Filtro Avanzado de la "Running Config"

```c
Router# show running-config | include username
```

⚡ **Explicación rápida y ejemplo:** Le pides al equipo que examine toda la configuración, pero que **solo** te muestre las líneas donde aparezca la palabra "username" (los usuarios).

_(Nota: El símbolo `|` se llama "pipe" o barra vertical, y se saca en el teclado con `Alt Gr + 1` o `Acuadrado` según tu teclado)._

🧠 **La Lógica de Fondo:** Desarmemos este truco:

- **`show running-config`**: El router abre el mapa completo de la memoria RAM.
- **`|` (Pipe)**: Funciona como un embudo o filtro. Le dice al procesador: "No me muestres todo, espera un momento...".
- **`include username`**: "...busca solo las líneas que incluyan el texto 'username'".  
    El procesador hace un escaneo ultra rápido en milisegundos y te arroja en pantalla tus dos usuarios exactos con sus contraseñas encriptadas.

⚠️ **LA ADVERTENCIA:** Si al correr este comando ves que después del nombre de tu usuario dice la palabra `password` seguida de la contraseña en texto plano (ej: `username admin password 1234`), ¡estás en peligro! Cualquiera que se pare detrás de ti podrá leerla. Asegúrate siempre de que diga `secret` (ej: `username admin secret 9$aK...`), lo que significa que la contraseña está triturada matemáticamente y es imposible de descifrar a simple vista.

##### 1_Opción 2: El Comando Secreto de Usuarios Locales

```c
Router# show aaa user local
```

⚡ **Explicación rápida y ejemplo:** En los equipos Cisco más modernos y en simuladores avanzados, puedes interrogar directamente al gestor de seguridad local para ver la lista de usuarios y sus niveles de poder.

🧠 **La Lógica de Fondo:** Las siglas **AAA** significan _Autenticación, Autorización y Contabilidad_ (la policía de seguridad del router). Al ejecutar este comando, el sistema no lee el archivo de texto de la configuración, sino que va directo al módulo de seguridad interna y te despliega una lista limpia de los usuarios creados, cuántos privilegios tienen (del 0 al 15) y si están activos.

⚠️ **LA ADVERTENCIA:** Si creaste los usuarios pero al ejecutar este comando o intentar entrar por SSH el router te rebota, verifica que en tus líneas VTY hayas puesto el comando **`login local`**. Si solo pusiste `login`, el router buscará una contraseña global en lugar de mirar tu lista de usuarios, bloqueándote la entrada por completo.

### 5_Para ver en ese momento cual es mi nivel ve privilegio
```c
Router# show privilege
```
⚡ **Explicación rápida y ejemplo:** Este comando solo te arroja una línea de texto en pantalla que te dice en qué nivel de la escala del 0 al 15 estás operando en este instante.
Lo que te está mostrando ese "15" es tu **rango o nivel de poder actual en ese preciso milisegundo**. Es el espejo que te dice: _"Tú, la persona que está tecleando ahora mismo, tienes nivel 15 (Modo Dios)"_.
## 2_Switch 
### 1_El Mapa del Tesoro del Switch (Exclusivo de Switches)

```c
Switch# show vlan brief

Switch# sh vlan br
```

🧠 **La Lógica de Fondo:** Los switches, a diferencia de los routers, no enrutan por IP por defecto; ellos dividen la red en VLANs. Este comando le pide al Switch que te muestre el mapa de división. Verás qué puertos pertenecen a la VLAN 1 (la de fábrica), o si ya creaste la VLAN de Administración, de Estudiantes, etc.

⚠️ **LA ADVERTENCIA:** Por defecto, todos los puertos del switch vienen metidos en la **VLAN 1**. Si creas una VLAN de alta seguridad (ej: VLAN 10 para Servidores) pero olvidas mover los puertos físicos a esa VLAN con el comando `switchport access vlan 10`, tus servidores se quedarán aislados o expuestos en la red pública por defecto. ¡Verifica siempre esta tabla!

### 2_El comportamiento de `show running-config` y `show users` en el Switch

```c
Switch# show running-config

Switch# sh run
---
Switch# show users

Switch# sh users

```

⚡ **Explicación rápida:** Funcionan exactamente igual que en el router. Al ejecutarlos en el Switch verás los mismos resultados adaptados a su hardware.

🧠 **La Lógica de Fondo:**

- **En el Switch (`show running-config`):** Te mostrará los mismos usuarios locales que creaste y tus líneas VTY blindadas con SSH. La única diferencia visual es que en lugar de ver interfaces de Router (como `GigabitEthernet0/0`), verás la enorme lista de puertos del Switch (como `FastEthernet0/1` hasta la `0/24`).
- **En el Switch (`show users`):** Te delatará de inmediato si hay alguien conectado de forma remota administrando el Switch.

### 3_Crear la SVI (La interfaz virtual para darle IP al Switch)

```c
! Así se configura (en modo de configuración global):
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.254 255.255.255.0
Switch(config-if)# no shutdown

! Y así lo miras con tu comando favorito (en modo privilegiado):

Switch# show ip interface brief
```

⚡ **Explicación rápida y ejemplo:** Como los puertos físicos del Switch (donde conectas las computadoras) no aceptan IPs, el Switch crea un "puerto fantasma o virtual" dentro de su memoria llamado **SVI** (Switch Virtual Interface). Por defecto, usamos la **VLAN 1**. El comando para darle vida y el comando para verificarlo son estos:

🧠 **La Lógica de Fondo:** El Switch necesita una IP no para mandar los memes de una computadora a otra, sino para que **tú te puedas conectar a él de forma remota por SSH**. Al correr el comando `show ip interface brief`, verás la enorme lista de puertos físicos vacíos (`unassigned`), pero al final de la tabla aparecerá mágicamente una nueva línea que dice `Vlan1` con la IP que le acabas de poner. ¡Ahí es donde confirmas que tu Switch ya tiene identidad en la red!

⚠️ **LA ADVERTENCIA:** El error más común del universo (incluso le pasa a ingenieros con años de experiencia) es configurar la IP perfectamente pero olvidar escribir el comando **`no shutdown`** dentro de la `interface vlan 1`. Si lo olvidas, cuando corras el `show ip interface brief` verás que la IP está ahí, pero el estatus dirá **`administratively down`**. ¿El resultado? El Switch ignorará por completo la IP y tu SSH jamás va a conectar. ¡Siempre asegúrate de "encender" la interfaz virtual!

# **Construir las 16 salas de espera virtuales para los administradores**.

En lugar de usar el clásico `0 4`, escribió **`line vty 0 15`** en el modo de configuración global (`Router(config)#`).

🔍 El comando desarmado palabra por palabra

- **`line`**: Le avisa al router que vamos a configurar una de sus "líneas" o puertos de entrada.
- **`vty`**: Significa _Virtual Teletype_ (Teletipo Virtual). En cristiano: son los **puertos virtuales o invisibles** del router. El router no tiene un cable físico que se llame "VTY"; son entradas digitales para la gente que se conecta desde lejos a través de la red (usando SSH o Telnet).
- **`0 15`**: **Aquí está el gran truco.** Estos números definen **cuántas personas se pueden conectar al mismo tiempo** al router de forma remota.
    - En las redes se empieza a contar desde el número cero.
    - Si cuentas desde el **0 hasta el 15**, hay exactamente **16 líneas disponibles**.
    - Eso significa que el router abrirá 16 "salas de espera virtual" en su memoria. Si hay una emergencia gigante, 16 ingenieros diferentes de la empresa podrían iniciar sesión al mismo tiempo desde sus casas para trabajar juntos en el router. Si llega el ingeniero número 17, el router le dirá "Líneas ocupadas, intente más tarde".

🕵️‍♂️ ¿Por qué  usó `0 15` en lugar de `0 4`?

Tradicionalmente, los routers viejos solo soportaban 5 conexiones simultáneas (`0 4`). Pero hoy en día, los equipos Cisco modernos (y los que simula Packet Tracer por defecto) vienen con soporte para 16 líneas (`0 15`) o incluso más.

Tu profe usó `0 15` por **seguridad total**. Si tú solo configuras el rango `0 4`, dejas las líneas de la 5 a la 15 "libres y sin contraseña". Un hacker listo podría intentar colarse por la línea 5 aprovechando que te dio pereza configurarla. Al poner `0 15`, te aseguras de aplicarle el candado a **todas las puertas virtuales del router al mismo tiempo**.

⚠️ LA ADVERTENCIA DE "EL COMANDO EN BLANCO"

> **Al darle _Enter_ a `line vty 0 15`, el prompt cambiará a `Router(config-line)#`. ¡Cuidado! Estar ahí adentro no significa que SSH ya esté funcionando. Si te sales en este momento sin poner los comandos de activación, las 16 puertas se quedarán cerradas con llave y nadie podrá entrar.**

Es como si hubieras construido los 16 pasillos en el hotel, pero todavía no le has dicho al recepcionista qué tipo de clientes puede dejar pasar, ni qué identificación pedirles.

## 1_Activar los guardias y ponerle temporizador a las salas de espera

### 1_🔍 Los 4 comandos desarmados paso a paso

#### 1_password vty

- **¿Qué hace?** Le asigna la contraseña "vty" a las líneas virtuales.
- **La realidad técnica:** Como aprendimos en nuestra investigación de las versiones, las líneas físicas y virtuales de Cisco **te obligan** a usar la palabra `password` (aquí no puedes usar `secret`). Al poner `password vty`, le dejas una clave base a la línea. Sin embargo, no te preocupes por la seguridad, porque más abajo verás cómo el comando `login local` anula esta contraseña débil y activa algo mucho mejor.

#### 2_session-limit 120`

- **¿Qué hace?** Cambia las reglas del hotel que vimos antes. Recuerdas que al poner `0 15` abrimos 16 salas de espera virtuales. Con `session-limit 120`, tu profe cometió un pequeño error de tipeo o usó un comando de límite de tiempo modificado. En los equipos Cisco reales, este comando sirve para limitar cuántas sesiones máximas se permiten simultáneamente (y el rango suele ser menor) o para configurar el tiempo de espera si se combina con `exec-timeout`

- _Asumiendo la lógica de seguridad de tu profe:_ Lo que está haciendo aquí es **ponerle un temporizador de expulsión**. Si un ingeniero entra al router por SSH, se va a almorzar y deja la sesión abierta en su laptop, el router cuenta el tiempo. Al pasar el límite, el router le da una patada virtual y cierra la sesión automáticamente para que nadie más use esa computadora descuidada.

#### 3_transport input ssh

- **¿Qué hace?** **Este es el filtro definitivo.** Le dice al router: _"A estas 16 líneas virtuales SOLO se puede entrar usando el protocolo seguro SSH"_.
- **Por qué es vital:** De fábrica, las líneas `vty` aceptan tanto SSH como el inseguro **Telnet** (el que mandaba las contraseñas desnudas por el cable). Al escribir este comando, bloqueas y apagas Telnet para siempre. Si un hacker intenta conectarse usando Telnet, el router le cerrará la puerta en la cara inmediatamente sin siquiera pedirle contraseña.

#### 4_login local

- **¿Qué hace?** Le dice al router: _"Oye, cuando alguien intente entrar por SSH, no le pidas una contraseña genérica de línea. Ve a tu memoria interna, busca la lista de usuarios locales que creamos antes, y exígele su nombre de usuario y su contraseña encriptada"_.
- **La magia final:** Este comando es el que activa al usuario **`admin` con nivel 15 y clave `1234`** (o `secret`) que configuramos al principio. Borra la necesidad de usar el comando `password vty` del paso 1 y obliga a que cada ingeniero use su propia cuenta con su respectivo nivel de privilegio.

#### 5_🎨 El dibujo mental de lo que acabas de hacer:

Imagina que un ingeniero de la empresa intenta entrar al router desde su computadora:

1. Intenta entrar por Telnet -> **Rechazado** por el comando `transport input ssh`.
2. Intenta entrar por SSH -> **Aceptado**, la puerta se abre.
3. El router le dice: _"¿Quién eres?"_ -> Activado por el comando `login local`.
4. El ingeniero escribe: Usuario `admin`, clave `1234` -> Entra directo al **Nivel 15 (Modo Dios)** porque su usuario ya venía con ese poder.
5. El ingeniero se distrae viendo TikTok y deja la sesión abierta -> El router cuenta el tiempo y a los minutos lo expulsa gracias al `session-limit`.

#### 6_⚠️ LA ADVERTENCIA DE "EL CANDADO SIN LLAVE"

> **¡NUNCA escribas el comando `login local` si antes no has creado al menos un usuario con `username` en el modo de configuración global!**

Si cometes el error de poner `login local` en un router vacío, el router guardará el comando felizmente. Pero cuando intentes conectarte por SSH, el router te pedirá usuario y contraseña. Como no creaste ninguno antes, **ninguna clave va a funcionar** y te habrás quedado encerrado fuera de tu propio router de [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html). Te tocará borrar el laboratorio y empezar desde cero.

# Subirle el nivel al escudo del router (Pasar de madera a vibranium)

Justo después de fabricar las llaves de seguridad con el comando `crypto key generate rsa`

, el paso obligatorio para blindar el router es escribir **`ip ssh version 2`** en el modo de configuración global (`Router(config)#`).

⚡ Explicación rápida

Cuando tú creas las llaves RSA con el comando anterior, el router activa por defecto la **Versión 1 de SSH**. El problema es que la Versión 1 se inventó hace muchísimos años y los hackers ya le encontraron agujeros de seguridad (vulnerabilidades). Un atacante con conocimientos intermedios podría interceptar tus datos en el cable y descifrar tu contraseña `1234`.

Al escribir `ip ssh version 2`, le estás diciendo al router: _"Oye, apágame por completo el sistema viejo e inseguro. A partir de ahora, solo vas a aceptar conexiones que usen el protocolo SSH Versión 2"_, que utiliza algoritmos de encriptación modernos, ultra seguros y que nadie ha podido romper hasta el día de hoy.


⚠️ LA ADVERTENCIA DEL "TAMAÑO IMPORTA" (¡Mucho ojo con esto!)

> **Si intentas escribir `ip ssh version 2` pero cuando creaste las llaves RSA (en el comando anterior) le pusiste un tamaño de 512 bits, el router te va a escupir un error en la pantalla y NO te va a dejar activar la versión 2.**

La versión 2 de SSH es tan estricta con la seguridad que **exige como mínimo una llave de 768 bits** para poder funcionar (aunque, como ya aprendiste, los ingenieros de verdad usan **1024 o 2048 bits** por estándar profesional). Si tu llave es muy pequeña y débil, el router preferirá quedarse en la versión 1 antes que dejarte activar la versión 2 con un escudo mediocre.

🕵️‍♂️ Un detalle de Packet Tracer:

En las computadoras de [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html), cuando entras a la sección de "Command Prompt" (la terminal negra) para conectarte al router de forma remota, el comando se escribe así:

```c
PC> ssh -l admin 192.168.10.254
```


_(La `-l` significa "login" y luego pones el usuario `admin` que creaste con tu profe)._ Gracias a que activaste la versión 2, esa conexión será un túnel impenetrable.

## 1_**SOLO EXISTEN DOS VERSIONES:** SSHv1 **y** [SSHv2](https://www.emtec.com/ssh/ssh-v2.html)

Todos los fabricantes de tecnología del planeta (Cisco, Huawei, Juniper, HP, e incluso tu propia computadora con Windows, Mac o Linux) se pusieron de acuerdo y usan exactamente las mismas dos versiones universales para que todo pueda conectarse entre sí de forma estandarizada.

Para que lo tengas súper claro y escaneable para tus notas, aquí tienes la tabla con las versiones de SSH que existen, sus capacidades y los tamaños de llave que soportan:

### 📊 Comparativa de Versiones Universales de SSH

|Versión de SSH|Estado Actual|Seguridad|Tamaño de Llave RSA Mínimo|Tamaño Recomendado (Vida Real)|Explicación Técnica Rápida|
|---|---|---|---|---|---|
|**SSHv1** (Versión 1)|**Obsoleto / Prohibido**|❌ Muy Inseguro (Tiene fallas graves)|512 bits|_No usar jamás_|Se inventó en 1995. Es vulnerable a hackeos donde interceptan el cable y te roban la contraseña.|
|**SSHv2** (Versión 2)|**Estándar Actual (El Rey)**|**Ultra Seguro (Impenetrable)**|768 bits _(Cisco exige ≥ 768 para activarlo)_|**2048 bits** o más|Se inventó en 2006. Rediseñó por completo la seguridad. Es el que activaste con el comando `ip ssh version 2`.|


### 🔍 ¿Qué significan esos números de bits (512, 1024, 2048)?

Para entenderlo de forma sencilla: el número de bits es **el tamaño del candado digital**.

- Una llave de **512 bits** es como un candado de combinación de 3 números: una computadora moderna probando combinaciones súper rápido lo puede abrir en minutos.
    
- Una llave de **2048 bits** genera una combinación matemática tan ridículamente gigante que, si pusiéramos a todas las computadoras del mundo a intentar adivinarla juntas al mismo tiempo, **tardarían miles de años en romperla**. Por eso el estándar profesional actual en las empresas es configurar **2048 bits**.

### ⚠️ LA ADVERTENCIA DE COMPATIBILIDAD

> **Si dejas el router por defecto (que acepta SSHv1 y v2 al mismo tiempo), un hacker sofisticado puede usar un truco llamado "Ataque de degradación" (_Downgrade Attack_). Obliga a tu router a hablar en SSHv1 para poder romper la seguridad.**

Al escribir el comando específico de tu profe `ip ssh version 2`, estás borrando a SSHv1 del mapa. El router se vuelve sordo a la versión 1 y la seguridad queda blindada al 100%.

# Fabricar los escudos criptográficos del router
**si no tienes configurados el `hostname` y el** ==**`ip domain-name`, el router te va a rebotar el comando en la cara** con un mensaje de error==.

## 1_Encriptar
### 1_🧠 ¿Por qué el router te obliga a tener Hostname y Dominio?

El comando `crypto key generate rsa` sirve para **crear un par de llaves digitales (una pública y una privada)**. Imagina que el router va a fabricar una caja fuerte digital para que cuando tú te conectes por SSH, tus contraseñas viajen protegidas por el cable y ningún hacker las pueda interceptar.

Para firmar y crear ese certificado de seguridad, el algoritmo matemático (llamado **RSA**) necesita obligatoriamente un **Nombre Completo (Fichado)** del dispositivo.

Si dejas los datos de fábrica, pasaría esto:

- **Nombre de fábrica:** `Router`
- **Dominio de fábrica:** No tiene.
- **Nombre completo:** `Router.` (No sirve).

Si el router intentara crear la llave con el nombre "Router", y en tu empresa hay 20 routers llamados igual, las llaves digitales serían idénticas y un hacker podría abrir todas las cajas fuertes de la red usando la misma llave. Por seguridad, Cisco bloquea el comando.

Cuando tú sigues los pasos de tu profe y pones:

1. `hostname R1-MEDELLIN`
2. `ip domain-name nexo.com`

El router junta las dos piezas y dice: _"¡Perfecto! Mi nombre único en el mundo es_ [nexo.com](http://nexo.com/)_"_. Con ese dato, el algoritmo matemático ya puede mezclar las letras y los números para crear una llave digital que sea **única en todo el planeta**.


### 2_🛠️ ¿Qué pasa cuando le das Enter a ese comando?

En cuanto escribes `crypto key generate rsa` y le das _Enter_, [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html) te va a hacer una pregunta en inglés:

> _“How many bits in the modulus [512]:”_ (¿De qué tamaño quieres la llave?)

Ahí tú tienes que escribir un número (los profes suelen usar **1024** o **2048**):

- **512 bits:** Es una llave pequeña. Es rápida de crear, pero hoy en día es tan débil que un celular moderno la puede hackear en minutos.
- **1024 bits:** Es el mínimo que te acepta el router para activar la versión segura de SSH (SSH v2).
- **2048 bits:** Es el estándar profesional actual. Es una caja fuerte digital ultra pesada y segura.

### 3_⚠️ LA ADVERTENCIA DE "EL EFECTO BORRADOR"

> **Si en el futuro se te ocurre cambiar el `hostname` o el `domain-name` del router, las llaves RSA que creaste hoy se VAN A BORRAR automáticamente.**

El router es tan estricto que si nota que cambiaste de nombre (por ejemplo, de `R1` a `RouterJefe`), pensará: _"Este ya no es mi certificado oficial"_, destruirá la llave RSA y el acceso remoto (SSH) se va a apagar de inmediato hasta que vuelvas a escribir el comando `crypto key generate rsa`. ¡Ten mucho cuidado con cambiar nombres a mitad de un proyecto!

## 2_Ocultar llaves visibles por comandos
### 1_El Camuflaje de Contraseñas Desnudas

```c
Router(config)# service password-encryption
```

⚡ **Explicación rápida y ejemplo:** Te metes al modo de configuración global y ejecutas el comando. A partir de ese milisegundo, cualquier contraseña vieja o nueva que use la palabra clave `password` (texto plano) se transformará en un montón de caracteres raros e ilegibles.

🧠 **La Lógica de Fondo:** Desarmemos qué pasa dentro del procesador y la memoria RAM:

- **`service`**: Le dice al sistema operativo: _"Voy a activar o modificar un servicio del sistema en segundo plano"_.
- **`password-encryption`**: Especifica exactamente qué servicio es: _"El motor de encriptación de contraseñas de texto plano"_.  
    Cuando el procesador ve este comando, activa un algoritmo matemático llamado **Cisco Tipo 7**. Su único trabajo es escanear la configuración (la _running-config_) y buscar palabras como la contraseña de `enable password` o las contraseñas de la línea de consola, y triturarlas visualmente. Si antes en tu `show running-config` se leía `password 1234`, ahora se leerá algo como `password 08314F5D0A`. ¡Nadie podrá leerla por encima de tu hombro!

⚠️ **LA ADVERTENCIA (El secreto que los Hackers no quieren que sepas):** ¡Mucho ojo aquí, futuro experto! La encriptación Tipo 7 que genera este comando **es extremadamente DEBIL**. No es un algoritmo de seguridad real, es solo un "camuflaje visual" para evitar que la gente chismosa vea tu pantalla (lo que en ciberseguridad llamamos _Shoulder Surfing_). Existen miles de páginas web gratis donde pegas ese código raro (`08314F5D0A`) y en menos de un segundo te dice que la clave era `1234`.

Por eso, la regla de oro en NexoSoft Labs es:

1. Usa siempre **`secret`** en lugar de `password` al crear usuarios o contraseñas de enable (ya que `secret` usa algoritmos ultra fuertes como MD5 o SHA-256 que no se pueden revertir).
2. Deja el comando `service password-encryption` activo como una capa extra de protección obligatoria para las líneas viejas (como la línea de consola), tal como lo dictan las buenas prácticas de ITIL.
# Seguridad en las claves importante 

## 1_Encriptar
```c
username admin privilege 15 secret 1234
```

El router va a agarrar el "1234", lo va a triturar usando un algoritmo matemático (encriptación), y cuando alguien intente mirar la configuración, en lugar de "1234" verá algo horrible como `$1$mERr$hx5gH...`

## 2_El truco de "ocultar" con `service password-encryption`

Cisco inventó un punto medio. Si por alguna razón de la vida estás obligado a usar el comando `password 1234`, puedes activar un comando global llamado

```c
Router(config)# service password-encryption
```

Este comando funciona como un "maquillaje barato". Agarra todas las contraseñas que creaste con la palabra `password` y las transforma visualmente en números raros (por ejemplo, el `1234` se convierte en `08314927`).

No es tan seguro como `secret` (porque un hacker experto puede romper ese maquillaje en dos segundos usando páginas web gratis), pero al menos evita que el compañero que se pare detrás de ti te lea la clave en la pantalla.


⚠️ LA ADVERTENCIA DE ORO PARA TUS EXÁMENES

> **En la vida real y en los exámenes de certificación de Cisco (CCNA), usar `password` en lugar de `secret` para un usuario de nivel 15 te costará puntos negativos inmediatos.**

Tu profe probablemente usó `password 1234` en la pizarra porque es más rápido de escribir o para ver si ustedes estaban despiertos y pillaban el riesgo de seguridad. 

## 3_Que pasa si cambio 

Si cambias la palabra `password` por `secret` en los comandos que has visto con tu profe (como **`username`** o **`enable`**), el router **NO te dará ningún error de comando no encontrado**. El comando va a funcionar a la perfección y tu contraseña quedará encriptada y protegida de inmediato.
Sin embargo, hay **un lugar específico** en donde Cisco te obliga a usar la palabra `password` sí o sí. Aquí te dejo la lista exacta para que no vayas a cometer un error en tus prácticas de [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html)
✅ Los comandos donde SÍ puedes (y debes) cambiar `password` por `secret`

En estos dos casos, la palabra `secret` es perfectamente válida, no arroja error y es la mejor práctica de seguridad del mundo: [[1](https://community.cisco.com/t5/networking-knowledge-base/understanding-the-differences-between-the-cisco-password-secret/ta-p/3163238)]

1. **Creación de usuarios locales (El comando de tu profe):**
    - _Inseguro:_ `username admin privilege 15 password 1234`
    - _Seguro y válido:_ `username admin privilege 15 **secret** 1234` [[1](https://networklessons.com/system-management/user-mode-and-privileged-mode-security), [2](https://study-ccna.com/cisco-privilege-levels/)]
    
2. **Contraseña para entrar al Modo Dios (`enable`):**
    - _Inseguro:_ `enable password cisco`
    - _Seguro y válido:_ `enable **secret** cisco` [[1](https://www.ciscomadesimple.be/2012/03/04/ios-enable-password-vs-enable-secret/), [2](https://www.informaticar.net/cisco-ios-basics-passwords/)]

Si los escribes con `secret`, el router se pondrá feliz y triturará la clave usando algoritmos matemáticos avanzados (como MD5 o SHA-256) para que nadie te la pueda robar. [[1](https://community.cisco.com/t5/networking-knowledge-base/understanding-the-differences-between-the-cisco-password-secret/ta-p/3163238), [2](https://thisbridgeistheroot.com/blog/upgrading-your-cisco-ios-passwords)]

---

❌ El único comando donde NO se puede usar `secret` (¡Te dará error!)

Existe una sección del router llamada **Líneas de acceso** (que sirve para ponerle contraseña al cable de consola o a las conexiones virtuales de Telnet/SSH). La ruta se ve más o menos así: [[1](https://www.informaticar.net/cisco-ios-basics-passwords/)]


```c
Router(config)# line console 0
Router(config-line)# password 1234
```


> ⚠️ **La Advertencia de Error:** Si dentro de `Router(config-line)#` intentas escribir `secret 1234`, el router **SÍ te arrojará un error inmediato** de comando no reconocido. [[1](https://www.reddit.com/r/ccna/comments/z1cdft/what_is_the_difference_between_enable_secret/)]

Cisco diseñó las líneas físicas para que **únicamente** acepten la palabra clave `password`. Para proteger esa contraseña de miradas chismosas en la configuración, estás obligado a usar el truco del que hablamos antes: el comando global `service password-encryption`. [[1](https://www.reddit.com/r/ccna/comments/z1cdft/what_is_the_difference_between_enable_secret/), [2](https://www.informaticar.net/cisco-ios-basics-passwords/), [3](https://geek-university.com/service-password-encryption-command/)]

## 4_Diferencia de passwor y secret

Cisco inventó sus sistemas operativos de red (llamados IOS) hace más de **30 años** (en los años 80 y 90). En esa época, los routers tenían procesadores súper débiles, parecidos al cerebro de una calculadora vieja.

- El comando `password` no consume nada de energía porque guarda el texto tal cual lo escribes.
    
- El comando `secret` tiene que hacer matemáticas complejas (encriptación) para triturar la contraseña.

En los años 90, si le ponías `secret` a todo, el router se ponía lento solo por procesar la contraseña. Por eso nació primero `password`. Con los años, los routers se volvieron súper potentes y se inventó `secret`, pero Cisco no podía borrar `password` del mapa de la noche a la mañana porque rompería el sistema de millones de equipos antiguos.



# Darle una cédula de identidad digital al router

**lo hizo porque en este punto lo está configurando para el ROUTER mismo, no para las computadoras.** El dominio del DHCP era un regalo para los usuarios; el dominio en `Router(config)#` es un requisito obligatorio para activar los superpoderes del router.

## 1_🔑 El objetivo oculto: Activar SSH (Control remoto seguro)

Tu profe ya creó un usuario administrador (`username admin privilege 15 password 1234`). El siguiente paso lógico en una red real es hacer que el ingeniero pueda entrar a controlar el router desde su oficina, sin tener que caminar hasta el cuarto de servidores con un cable físico. Eso se hace con un protocolo llamado **SSH (Secure Shell)**.

Para que [SSH](https://www.cloudflare.com/es-es/learning/access-management/what-is-ssh/) funcione, el router tiene que encriptar (proteger) la conexión con una **clave matemática ultra secreta**. Y para fabricar esa clave, el router te exige obligatoriamente dos datos para firmar el contrato:

1. Un nombre (que ya lo tiene: `Router`).
2. Un apellido o dominio (que se lo das con `ip domain-name` miempresa.com).

## 2_🧠 ¿Por qué no sirvió el del DHCP?

Imagina que tú trabajas en una empresa y compras teléfonos corporativos para los clientes (las PC) y en la configuración les pones que el código de la empresa es _Nexo_. Eso no significa que **tú** automáticamente tengas un correo con ese dominio; tú tienes que ir a registrar tu propia cuenta.

- El comando dentro del DHCP (`domain-name`) era solo un papelito que el router le entregaba a las PC para que **ellas** supieran su apellido. El router no se lo quedaba para sí mismo.
- El comando en configuración global (`ip domain-name`) cambia el nombre interno del router. Si el router se llamaba `Router`, ahora su nombre oficial para el mundo de la seguridad informática será **`://`**miempresa.com.

Sin ese "apellido oficial" en el modo global, el router se negará rotundamente a generar las llaves de seguridad para SSH y no te dejará administrarlo a distanci

## 3_⚠️ LA ADVERTENCIA DE LAS LLAVES DE SEGURIDAD

> **Si pones `ip domain-name` pero el router todavía tiene el nombre de fábrica (`Router`), el sistema te dejará avanzar, pero es una mala práctica. Lo primero que debes hacer antes del dominio es cambiar el nombre del aparato con el comando `hostname`.**

Si dejas el nombre como `Router` y le pones el dominio, la llave de seguridad se creará con el nombre `://`miempresa.com. Si tienes 10 routers en la empresa y todos se llaman igual, los sistemas de seguridad se van a confundir y bloquearán las conexiones. Por eso la regla de oro es: 1) `hostname`, 2) `username`, 3) `ip domain-name`.

# Crear la cuenta de un usuario con las manos amarradas

## 1_Crear la cuenta del Administrador
```C
username admin privilege 0 secret [contraseña] (o a veces usan password en lugar de secret
```

- **`username admin`**: Le dice al router: _"Crea un usuario local en tu memoria que se llame 'admin'"_. Es exactamente igual a cuando creas un perfil de usuario en Windows o Netflix.

- **`privilege 0`**: **Aquí está el gran truco.** Los routers de Cisco tienen niveles de poder del **0 al 15**.
    - El nivel **15** es el de Dios (puedes borrar configuraciones, apagar interfaces, cambiar contraseñas).
    - El nivel **1** es el de usuario normal (puedes mirar algunas cositas, pero no cambiar nada).
    - El nivel **0** es... **el calabozo**. Es el nivel de privilegio más bajo que existe. Al ponerle `privilege 0`, ese usuario `admin` prácticamente no puede hacer nada. Solo tiene permiso para tres comandos tontos: iniciar sesión (`login`), cerrar sesión (`logout`) y habilitar el modo avanzado si se sabe la contraseña de administrador (`enable`).

## 2_**Crear la cuenta del Administrador Supremo (Modo Dios)**.

```c
username admin privilege 15 password 1234
```

Aquí es donde ves la diferencia real con el comando de nivel 0:

- **`username admin`**: Crea el usuario con el nombre "admin" en la memoria del router.
- **`privilege 15`**: **¡Aquí está el poder total!** Como ya te habías aprendido, el nivel 15 es el máximo nivel de autoridad en un equipo Cisco. Al ponerle `15`, le estás diciendo al router: _"Cuando este usuario inicie sesión, no lo dejes en la salita de espera (`Router>`), mételo directamente hasta la cocina con el signo de número (`Router#`)"_
    
    . Este usuario no necesita usar el comando `enable` ni ascensores; ya nace en el piso más alto.
- **`password 1234`**: Esta es la llave secreta para entrar a esa cuenta.


🕵️‍♂️ hacer seguimientos (El secreto de la auditoría)

Imagina que trabajas en una empresa grande con 20 ingenieros de redes. Si todos entran al router usando la misma contraseña genérica, y de repente un viernes por la tarde la red se cae porque alguien borró la configuración... ¿A quién le echas la culpa? Todos van a decir: _"Yo no fui"_.

Para solucionar eso, se crean **usuarios locales**

para cada persona (ej: `username carlos`, `username andrea`).

Cuando activas un sistema de seguimiento en el router (llamado **AAA** o registros de _Syslog_), el router empieza a escribir una bitácora en secreto:

> _"A las 4:15 PM, el usuario 'carlos' inició sesión. A las 4:17 PM, el usuario 'carlos' borró la VLAN 10"_.

¡Boom! Atrapado. A eso se refería tu profe con hacer seguimientos. Puedes saber exactamente **quién, cuándo y qué** comando escribió cada persona dentro del router.

### 1_Privilegios

En [Cisco](https://www.cisco.com/site/es/es/index.html), que es el rey de las redes y  en [Packet Tracer](https://www.packettracernetwork.com/download/download-packet-tracer.html), existen exactamente **16 niveles de privilegio**, que van numerados del **0 al 15**.

Para responder tu segunda duda rápidamente: **No estás obligado a crear un solo nivel por persona**, pero en la práctica real sí se le asigna un único nivel a cada usuario basado en su puesto de trabajo para mantener el orden. Lo que sí puedes hacer es crear **muchos usuarios diferentes en el mismo nivel** (por ejemplo, tener 5 ingenieros y que los 5 sean nivel 15).

A este sistema de niveles los ingenieros lo llaman **RBAC** (Control de Acceso Basado en Roles), pero para que te lo aprendas al pie de la letra, imagínalo como **los niveles de acceso de una base militar secreta**.

#### 🏛️ Los 3 Niveles "Por Defecto" (Los que ya vienen configurados de fábrica)

Cisco creó la mayoría de sus sistemas pensando en tres niveles principales. Si tú no inventas nada raro, el router viene con estos tres listos:

- **Nivel 0 (El Calabozo / Acceso de Visitante):**  
    Es el nivel más bajo. No puedes ver absolutamente nada de la configuración del router. El usuario solo puede escribir `login` (iniciar sesión), `logout` (salir) y `enable` (intentar subir de nivel). Sirve para que alguien compruebe si el equipo responde, pero sin tocar nada.
- **Nivel 1 (Modo Usuario Normal / Soporte Básico):**  
    Cuando entras a un router por primera vez y el prompt se ve así: `Router>`, estás en el nivel 1. Aquí puedes usar comandos básicos como `ping` (para ver si hay conexión) o `show clock` (para ver la hora). No puedes ver la configuración guardada (`show running-config`) ni puedes cambiar nada. Es el nivel para un técnico junior que solo va a revisar si los cables están bien.
- **Nivel 15 (Modo Dios / Administrador Total):**  
    Cuando escribes el comando `enable` y entras al prompt con el signo de número `Router#`, saltas automáticamente al nivel 15. Aquí tienes **control absoluto** del hardware. Puedes borrar la memoria, cambiar las contraseñas, apagar interfaces y crear los DHCP pools de los que hablamos antes. Un solo error en este nivel y dejas a toda la empresa sin internet.

---

🏗️ Los Niveles del 2 al 14 (Los Niveles Personalizados)

**¿Qué pasa con todos los números del medio?** De fábrica, los niveles del 2 al 14 vienen **completamente vacíos** y se comportan exactamente igual al nivel 1.

Cisco los dejó en blanco para que **tú los diseñes a tu antojo** usando comandos avanzados en el router. Tú puedes decidir qué comandos específicos puede usar cada número.

Aquí te pongo un ejemplo de cómo los organiza una empresa real:

- **Niveles 2 al 5 (Equipo de Monitoreo):** Les das permiso exclusivo para ver mapas y estadísticas de tráfico con comandos `show`, pero nada más.
- **Niveles 6 al 10 (Técnicos de Campo):** Les das permiso de usar comandos `show` y además comandos para reiniciar interfaces (`shutdown` / `no shutdown`) por si un cable falla, pero les prohíbes cambiar contraseñas o borrar redes.
- **Niveles 11 al 14 (Supervisores de Seguridad):** Les das permisos casi totales para crear usuarios y ver logs de seguimiento, pero no pueden tocar el enrutamiento de internet.

---

⚠️ LA ADVERTENCIA DE "LA TRAMPA DEL ENABLE"

> **Cualquier usuario que esté en los niveles del 1 al 14 puede saltar al Nivel 15 (Modo Dios) con solo escribir el comando `enable`, A MENOS que le pongas una contraseña específica a cada nivel.**

Si creas un usuario con `privilege 5` para que no rompa nada, pero no proteges el comando `enable` general con una contraseña fuerte (`enable secret`), ese usuario va a entrar a tu router, va a escribir `enable`, y el router lo va a subir al nivel 15 de inmediato. ¡Tu seguridad se caería a pedazos!

⚠️ LA ADVERTENCIA DEL "ADMINISTRADOR SIN PODER"

> **Llamar a un usuario "admin" pero ponerle `privilege 0` es una trampa mental. Cualquiera que vea el nombre pensará que es el jefe, pero cuando intente configurar algo, el router le dirá "Comando rechazado".**

Normalmente, si quieres crear un administrador de verdad, debes usar `privilege 15`.


# Para configurar DHCP y HSRP

## 1_Crear el contenedor

**este comando sirve para crear el contenedor y ponerle el nombre**, y a partir de ahí empiezas a meter la configuración real del DHCP

En los equipos Cisco, el router no te permite configurar cosas al aire. Primero tienes que decirle **en dónde** quieres meter esa configuración.

Imagina que entras a una bodega gigante llena de herramientas. No puedes llegar y gritar: _"¡Quiero pintar las paredes de color azul!"_ si ni siquiera has abierto la puerta del cuarto que quieres pintar.

Al escribir `ip dhcp pool 10-GESTION`, le estás diciendo al router: **"Oye, ábreme una carpeta nueva con este nombre"**. En ese instante, el router te mete dentro de esa carpeta (el prompt cambia a `Router(dhcp-config)#`) y todo lo que escribas a partir de ahí (como la red, las IPs a excluir, etc.) **se guardará exclusivamente dentro de esa carpeta llamada 10-GESTION**.

```c
ip dhcp pool {numero de la VLAN solo ejemplo }-{nombre de esa VLAN solo ejemplo}
```
- **`ip`**: Le avisa al router que vamos a configurar algo relacionado con el protocolo de internet (las reglas del juego de la red).
- **`dhcp`**: Son las siglas de _Dynamic Host Configuration Protocol_ (Protocolo de Configuración Dinámica de Anfitriones). En cristiano: el sistema que asigna datos automáticos para que no tengas que ir aparato por aparato escribiendo los números a mano.
- **`pool`**: En inglés significa piscina o estanque, pero en redes significa **"un grupo" o "un saco"** de cosas guardadas.
- **`[10]-{GESTION}`**: Este es el **nombre personalizado** que tú le diste a ese saco. Le pudiste haber puesto `Internet-Sistemas` o `Wifi-Invitados`, pero elegiste `10-GESTION` (probablemente porque pertenece a una sección de la red que se encarga de administrar o gestionar los equipos).

## 2_Definir el territorio del contenedor 
Si el primer comando creó la carpeta vacía llamada `10-GESTION`, el comando `network` es el que **llena esa carpeta con los números de IP permitidos**
Le dice al router: _"Oye, en este contenedor solo vas a fabricar y regalar IPs que pertenezcan a esta calle específica_

Si la red del comando `network` no coincide EXACTAMENTE con la IP que le pusiste a la puerta de la VLAN (la subinterfaz del router), el sistema se rompe no es que salga error sino que se rompe su logia y habra mucho caos 

```c
network {nuemro de ip de esa VLAN}{la mascara de esa VLAN}
```

## 3_Bautizar la red con el apellido de la empresa
magina que las computadoras que se van a conectar a la VLAN 10 son como empleados nuevos que entran a trabajar. Con el comando `network` ya les diste su número de identificación (su IP). Ahora, con el comando `domain-name`, el robot DHCP les va a tatuar automáticamente en la frente **el nombre de la empresa a la que pertenecen** (por ejemplo: `miempresa.com` o `colegio.local`).

Si una computadora se quiere comunicar con un servidor interno que se llama "Servidor1", gracias a este comando, la computadora sabe que su nombre completo en el mundo de internet es `://miempresa.com`. Es, literalmente, **el apellido oficial de tu red**.

a tener en cuenta => > **Este comando no acepta espacios y solo sirve si pones texto en formato de internet (letras separadas por un punto, como `empresa.com`). Si pones espacios o te equivocas en una sola letra, podrías romper servicios avanzados que verás más adelante.**

```c
domain-name {nombre de la empresa o del dominio}
```

## 4_Configurar la puerta de escape de emergencia HSRP

El comando `default-router` sirve para decirle a todas las computadoras de tu VLAN: _"Oye, cuando quieras ir a una página de internet como YouTube o Discord, tienes que mandarle tus paquetes a esta dirección IP"_. Es, literalmente, **el mapa hacia la salida del edificio**.

```c
default-router {ip y finaliza en 254}
```

🧠 ¿Por qué el se usó la IP que termina en .254 para HSRP?

Aquí está el secreto mejor guardado de los ingenieros de redes. **HSRP** (Hot Standby Router Protocol) es un truco de magia que se usa para que el internet nunca se caiga.

Imagina que en una empresa importante no se pueden dar el lujo de que un router se queme o se apague y deje a todos sin internet. ¿Qué hacen? Ponen **dos routers reales** físicos cuidando la misma VLAN.

- El Router A tiene la IP real `192.168.10.1`
- El Router B tiene la IP real `192.168.10.2`

Si tú le pones a las computadoras como salida la IP del Router A (`.1`), y ese router se quema, ¡todos se quedan sin internet!

Para solucionar esto, HSRP crea un **"Router Fantasma" (o Router Virtual)** que no existe físicamente, pero que los dos routers reales controlan. A ese router fantasma tu profe le asignó la última IP utilizable de la red: **la `.254`**.

Con el comando `default-router 192.168.10.254`, tú le dices a las computadoras que apunten al fantasma. Si el Router A se muere, el Router B se da cuenta al instante, se disfraza del fantasma `.254` y sigue recibiendo el internet de todos. ¡Los usuarios ni se enteran de que un router explotó!

> **En el comando `default-router` de tu DHCP tienes que poner la IP del router virtual (fantasma) de HSRP, NUNCA las IPs reales de los routers físicos (ni la .1 ni la .2).**

## 5_Ponerle candado a las IPs de los jefes

⚡ Explicación rápida

Imagina que ya creaste todas las habitaciones en tu hotel (tus pools de DHCP). Ya tienes el contenedor de la VLAN 10, el de la VLAN 20, el de la VLAN 30, etc. El robot DHCP ya está listo con sus bolsas llenas de llaves para repartir.

Antes de abrir las puertas del hotel al público, usas el comando **`ip dhcp excluded-address`** para decirle al robot: _"¡Quieto ahí! Antes de que empieces a regalar llaves como loco a los clientes, sácame de la bolsa las llaves de la `.1` a la `.10` (que son para mis servidores) y la `.254` (que es nuestro router virtual de HSRP). Esas llaves no se le entregan a nadie, van guardadas bajo llave en mi oficina"_.

Se escribe fuera de los contenedores (en el modo de configuración global) y se ve así:

```
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
Router(config)# ip dhcp excluded-address 192.168.10.254
```

⚠️ LA ADVERTENCIA DE "LLEGASTE TARDE" (Por qué el profe lo hace al final)

> **En Packet Tracer, si conectas las computadoras antes de poner las exclusiones, el DHCP empezará a regalar IPs de inmediato. Si le regala la `.254` a una PC antes de que crees HSRP, generarás un conflicto de IPs y la red se volverá loca.**

Por eso tu profe espera a tener todo bien estructurado en su cabeza y en la pizarra para lanzar las exclusiones. Así se asegura de bloquear todas las IPs prohibidas al mismo tiempo antes de encender las interfaces y dejar pasar el tráfico real.

## 6_Ponerle un guardaespaldas a las IPs VIP (Muy Importantes)

Como vimos antes, el robot DHCP es súper generoso y empieza a regalar IPs a lo loco a cualquiera que se conecte. Si no lo detienes, le podría dar la IP `.254` (tu puerta de enlace/HSRP) o la `.1` a un celular viejo que entró a la oficina. Cuando el router intente usar su propia IP, chocará con el celular, habrá un **conflicto de IPs** y esa sección de la red se va a apagar por completo.

Para evitar eso, usas este comando **fuera** de los contenedores. Le das una lista de números prohibidos al robot para que los saque de su bolsa de regalos.

Hay dos formas de usarlo (con rango o una sola):

- **Ejemplo 1: Bloquear un rango completo (de la 1 a la 10):**
    
    ```C
    Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
    ```
    

    _Aquí le dices: "Desde la .1 hasta la .10 están prohibidas. Guárdamelas para servidores, impresoras o routers reales"._
- **Ejemplo 2: Bloquear una sola IP específica (la de HSRP):**
    
    ```C
    Router(config)# ip dhcp excluded-address 192.168.10.254 <= esta es la del HSRP
    ```

    _Al poner un solo número, el router entiende que solo debe proteger esa IP en específico._



⚠️ LA ADVERTENCIA DEL "REPARTIDOR VELOZ"

> **¡Escribe las exclusiones ANTES de activar las interfaces del router o conectar las PC!**

En Packet Tracer, en cuanto una computadora se conecta y ve un puerto encendido, dispara una solicitud DHCP en microsegundos. Si no has escrito el comando `excluded-address`, el robot le entregará las primeras IPs disponibles (la `.1`, la `.2`, etc.). Si pones el comando de exclusión _después_, el router te dirá que la IP ya fue entregada y tendrás que ir PC por PC reiniciando la tarjeta de red para arreglar el desastre. ¡Primero se bloquea, luego se prende la red!

## 7_
# Para ver el registro interno del equipo 

```C
show arp
```
# Para seguridad en el router o switch 
## line console 0 
Este comando sirve para **entrar a la configuración del puerto físico de administración del switch o router**.

Imagina que el switch es una fortaleza: `line console 0` es el comando que te permite pararte en la **puerta de entrada física** para ponerle un candado (una contraseña).

Aquí te detallo qué significa cada palabra de ese comando:

- **`line` (Línea):** Le dice al equipo que vas a configurar una vía o canal de comunicación.
- **`console` (Consola):** Especifica que la vía que vas a configurar es el puerto físico trasero (el puerto de consola de color celeste donde conectas el cable hacia tu laptop para usar **PuTTY**).
- **`0` (Cero):** En informática se empieza a contar desde el cero. Como los switches y routers solo tienen **un único puerto de consola físico**, ese puerto es el número `0`.
- No olvidar que es para ponerle contraseña a penas uno se conecta 

```c
router/switch(config)# line console 0
router/switch(config-line)# password (clave)
router/switch(config-line)# login
```

## session-limits
limitar la sección  en la que esta uno dentro 
```c
session-limit {numero en segundos}
```
1. **no**: Para evitar el diálogo de configuración inicial esto sale apenas se ingresa al CLI del equipo.

2. **Ayudas para ver y guardar:**
	1. ==**Para Switch:**== 
		 - **`show running-config`**: Para ver qué has hecho hasta ahora saldrá la palabra **MORE** significa que hay mas información y solo es presionar **ENTER** para seguir bajando y cuando ya no haya mas información saldrá ***END**. 
	
		- **`show vlan brief`**: El mejor para ver la tabla de VLANs, su nombre y qué puertos están en modo acceso en cada una.
		- **`show vlan id [Numero de VLANS]`**: Muestra información específica solo de la VLAN .
		- **`show interfaces trunk`**: Dice qué puertos son **Trunk**, cuál es la **VLAN Nativa** y qué VLANs tienen permiso para pasar por el cable
	
	2. ==**Para router:**==
		- - **`show ip interface brief`**: Te da una lista rápida de todas las interfaces, si están encendidas (`up/up`) y qué IP tienen (incluyendo la `Vlan 1` o `Vlan 99`).
		- **`show ip route`**: Si usas un switch multicapa (como el 3560 o 3650), este comando te dirá si el switch está enrutando paquetes.
		
	3.  **Para saber qué está pasando físicamente en los cables:**

		- **`show interfaces status`**: ==Switch==Te dice de un vistazo qué puertos tienen algo conectado, a qué velocidad van y en qué VLAN están.
		- **`show interfaces fa0/3 switchport`**: ==Router, Switch== Este es un comando de "Rayos X". Te dice TODO sobre ese puerto: si es trunk o access, la vlan nativa, si tiene negociación activa, etc.
		- **`show mac address-table`**: ==Switch== Te muestra qué equipos (por su dirección MAC) ha aprendido el switch y en qué puerto están conectados. ¡Es magia para rastrear intrusos!
		
	4. **Sobre Seguridad y ACLs**

		- Como estás usando listas de acceso:
		
			- **`show access-lists`**: ==Router, Switch== Te muestra todas las ACLs creadas y, lo mejor, cuántas veces han bloqueado o permitido un paquete (el contador de _matches_).
			- **`show ip access-group`**: ==Switch== Te dice en qué interfaz está aplicada cada ACL.
			- **`show port-security`**: ==Switch==Si configuraste seguridad de puertos (para que solo una PC se conecte), este comando te dice si hay violaciones o bloqueos. 
		
	5. **`no shutdown`**: ¡El más importante! Por defecto, los puertos de los routers Cisco vienen "apagados" (en rojo). Este comando los **enciende** esto se ejecuta en el nivel **`Router(config-if)#`**
	
	6. `write memory`: para guardar en memoria siempre aplicar los dos  
	
	7. ==**`copy running-config startup-config`**:== Guarda los cambios realizados en la memoria persistente del router  le dice al equipo: _"Toma todo lo que acabo de configurar en la RAM y cópialo en el disco duro para que no se borre"_ pero también se puede evitar  escribir todo eso es muy largo. En Cisco (y en Packet Tracer) anivel **Switch ># copy**
	8. puedes usar la versión ultra resumida que hace exactamente lo mismo: Solo escribe: **`wr`** (que significa _write_) y dale a **Enter** también tener encuentra que este código se puede ejecutar en el nivel de **(#)** también se puede usar **`(config-if)# do write o (config-line)# do write`** en caso que no estemos en el nivel de **(#)**

3. **nivel de privilegio:** cuando se abre la terminal aparece el nivel de estado con unos símbolos después del nombre ejemplo => Router >, Router#.

	1. **El símbolo `>` (Modo Usuario)**
		Cuando abres el CLI y ves `Router>`, estás en la puerta de la casa. Solo puedes mirar un poco, pero no puedes tocar nada.
		
		- **Qué debes hacer:** Escribir el comando `enable` y presionar **Enter**.
		
	2. **El símbolo `#` (Modo Privilegiado)**
		
		Al darle **`enable`**, verás que el símbolo cambia: ahora dice `Router#`.
		
		- **Significado:** Ya entraste a la sala. Tienes "llaves", puedes ver toda la configuración, pero aún no puedes "remodelar" (cambiar cosas).
		- **Qué debes hacer para cambiar algo:** Escribir `configure terminal` y presionar **Enter**.
		
	2. **El paréntesis `(config)#` (Modo Configuración)**
		
		Ahora el indicador cambiará a `Router(config)#`.
		
		- **Significado:** Estás en el modo "maestro". Aquí es donde escribes los comandos para cambiar nombres, poner IPs o encender puertos.
		
4. **Comandos mas utilizados:**
	1. **`enable`**: Para entrar al modo privilegiado (pasar de `>` a `#`).
	2. **`configure terminal` o  `conf t`**: Para entrar al modo de configuración (donde haces los cambios).
	3. **`hostname [Nombre]`**: Para ponerle un nombre al equipo (ej: `hostname Switch_Piso1`) y en el CLI se vera así **`Switch_Piso1(config)# hostname Sala_Internet`**.
	
	4. **interface gigabitEthernet 0/0**: Selecciona la interfaz específica para configurar
		- **`interface [nombre del puerto]`**: Para entrar a un cable específico y darle órdenes (ej: `interface gigabitEthernet 0/0`) el nombre del puerto varia según el equipo que estemos configurando tener en cuenta que `Router>` es solo un ejemplo de como se ve en este equipo y varia dependiendo el equipo que estemos configurando.
			- Primero se tiene que estar en el modo de **Configuración Global**.
				- Si ves `Router>`, escribe: **`enable`**
				- Si ves `Router#`, escribe: **`configure terminal`**
				
			- Ahora que ves `Router(config)#`, ya puedes poner el comando:  
			    **`interface gigabitEthernet 0/0`** (puedes escribir solo **`int gig 0/0`** para ahorrar tiempo) básicamente esto es para entrar a la configuración de el puerto osea donde esta conectado el cable con el equipo si lo hiciste bien, verás que el indicador cambia a:  
				`Router(config-if)#`  
				Ese **"-if"** significa _Interface_, confirmando que ya estás "dentro" del puerto.
		
			- ¡Encendamos la luz roja! **=>** lo mas profesional es usarlo después de configurar 
				Como vimos en tu imagen, el cable está en rojo porque el puerto está "dormido". Una vez estés dentro de la interfaz (`config-if#`), escribe este comando :  
				**`Router(config-if)# no shutdown`**
				
	5. **`ip address [IP] [Máscara]`**: Para darle una dirección de Internet al puerto por lo general esta configuración es para un **ROUTER** y se ejecuta a nivel **(config-if)#**.
	
		1. si no se quiere poner una IP se usa **`no ip address`**
		
		2. **ip address 192.168.1.1 255.255.255.0**: Asigna la dirección IP y la máscara de subred a la interfaz para que el comando este completo siempre tiene que asignarse 
			1. ¿Cómo confirmo qué IP puse?
	
				- Dos formas principales de mirar:
				
					- **La rápida (Resumen):** Escribe `show ip interface brief` o **`sh ip int br`** .Verás una tabla. Busca tu interfaz (ej. _GigabitEthernet0/0_). Ahí verás la IP y si el estado dice "up" (verde) esto se hace nivel **`Router#`** 
					
					- **La detallada:** Escribe `show running-config` (o `sh run`).
					    - Baja hasta encontrar la interfaz y verás la IP y la máscara exactas que están funcionando ahora mismo.
					- **`ping [numero de ip]`** esste comando nos sive para verificar si hay comunicacion entre los equipos y que todo este bien conectado esto se hace nivel **`Router#`** 
	
	6. **`exit`**: Para retroceder un nivel.
			1. `Router#` (Nivel 1)
			2. `Router(config)#` (Nivel 2)
			3. `Router(config-if)#` (Nivel 3)
			4. Si usas **`exit`**: Vas de 3 a 2, y luego de 2 a 1. (Vas escalón por escalón)
	7. **end**: nos devuelve al modo privilegio  casi al inicio
			1. `Router#` (Nivel 1)
			2. `Router(config)#` (Nivel 2)
			3. `Router(config-if)#` (Nivel 3)
			4. Si usas **`end`**: Vas de 3 a 1 de un solo salto. (Usas el ascensor).
			
	8. ==**Switch (VLANS):**== 
		- configuracion  
			- **Crear una VLANS**
				- Switch> enable
				- Switch# configure terminal
				- Switch(config)# vlan 10
				- Switch(config-vlan)# name VENTAS
				- Switch(config-vlan)# exit
				
			- **VLAN 666:**
				- Crear la **VLAN 666** es una práctica común para crear un **"Agujero Negro" (Black Hole VLAN)**.
					- No es una VLAN especial por software, es simplemente un número que los técnicos eligen (por el simbolismo del número) para agrupar todos los **puertos que no se están usando**.
						
						- **Segmentación:** El atacante queda en una "isla" virtual. Aunque logre saltarse el bloqueo físico (el `shutdown`), no podrá "ver" a las PCs de la VLAN 10 ni de la VLAN 20 porque están en redes lógicas distintas.
						- **Sin Salida (Black Hole):** Como esa VLAN 666 no tiene una dirección IP configurada en el router (no hay sub-interfaz), el hacker no tiene **Puerta de Enlace (Gateway)**. No puede salir a Internet ni saltar a otras redes.
						- **Confusión:** El atacante verá que tiene "link" (luz verde si el puerto está encendido), pero no recibirá una dirección IP, no podrá hacer ping a nadie y no encontrará ningún servicio. Estará atrapado en un lugar donde no hay nada que atacar.
						
						- interface range fa0/10 - 24
						- switchport access vlan 666  # Los mandas al agujero negro
						- shutdown                    # Apagas el puerto

			- **Asignar una VLANS a los puertos** 	
				- `Switch(config)# interface range fastEthernet 0/2 - 10`
				
				- `Switch(config-if-range)# switchport access vlan [numero de VLANS]` => el numero es la VLAN que ya configuramos puede ser la 20 o la 10 si no se crea la VLANS nos saldrá este paso al asignarle 
		
				- si nos equivocamos en un puerto lo corregimos de dos maneras
					1. lo sacamos de la VLANS que ya creamos
						- `Switch(config)# interface fastEthernet [numero del puerto]`
						- `Switch(config-if)#no switchport access vlan 10`
						
					2. le asignamos la VLANS 1 que por defectos todos están 
						- `Switch(config)# interface fastEthernet [numero del puerto]`
						- `Switch(config-if)#`switchport access vlan [numero de VLANS]``
						
				-  ==**Blindaje de Gestión y Cambio de VLAN Nativa (VLAN 99)**==
					- **VLAN Pruning y Segmentación de Nativa:** Separar el tráfico de administración del tráfico de los usuarios para evitar ataques de **VLAN Hopping** y permitir el acceso remoto seguro a los equipos de red desde un segmento aislado.
					

					- **Fase 1: Creación de la Identidad**
						- `S1(config)# vlan 99`
						- `S1(config-vlan)# name NATIVE
							- **¿Qué hace?**: Crea la "calle" número 99 y le pone nombre.
							- **Seguridad**: Al no usar la VLAN 1 (la de fábrica), ya estamos dejando de ser un blanco fácil para herramientas automáticas de hackeo.
							
					- **Fase 2: Asegurar la "Autopista" (Trunk)**
						- `S1(config)# interface fastEthernet [numero del puerto]`
						- `S1(config-if)# switchport trunk native vlan 99`
							- **¿Qué hace?**: Define que en ese cable troncal, todo el tráfico que **no tenga etiqueta** (tráfico huérfano o de control) se mueva por la VLAN 99.
							- **Seguridad**: Obliga a que cualquier dato que viaje por la troncal sea tratado bajo las reglas de la VLAN 99, anulando la vulnerabilidad de la VLAN 1.
							
					- **Fase 3: Para que el router reconozca la VLAN 99**
						- R1(config)#interface fastEthernet 0/0.99
						- R1(config-subif)#encapsulation dot1Q 99 `native`
						- R1(config-subif)#ip address 192.168.99.1 255.255.255.0  
						
			- ==Asignar una IP a un equipo (Switch, impresora, etc)==
		
				-  ==**Fase 1: Configuración del "Cerebro" del Switch (SVI)**==
						- S1(config)# interface vlan [numero de la ]
						- S1(config-if)# ip address 192.168.99.3 255.255.255.0
						- S1(config-if)# no shutdown
							- **¿Qué hace?**: Le asigna una "tarjeta de red virtual" al switch con una IP específica dentro de la red de gestión.
							- **Objetivo**: Es la dirección IP que escribirás en programas como `PuTTY` o para hacerle `ping` al switch desde tu oficina técnica.
					
				- **Fase 2: La Salida al Mundo (Puerta de Enlace)**
						- **S1(config)# ip default-gateway 192.168.99.1**
							- **¿Qué hace?**: Le dice al switch: "Si necesitas responderle a un técnico que está en otra red (ej. VLAN 10), envía la respuesta a través de esta IP (el Router)".
							- **Importancia**: Sin esto, el switch se queda "sordo" para cualquier conexión que no venga de su propia red local
						
			- ==Configurar aparatos para acceder a distancia==
				- Configurar las líneas virtuales (VTY)
				
					- **Switch(config)# line vty 0 4**
						- **¿Qué significa VTY?** Significa _Virtual Teletype_. Son puertos "invisibles" o lógicos. A diferencia del puerto físico de consola (donde conectas el cable celeste), estos puertos solo existen en la red.
						- **¿Para qué el `0 4`?** Le estás diciendo al switch: "Quiero configurar desde la línea 0 hasta la 4". Esto suma **5 sesiones simultáneas**. Si tú estás conectado (línea 0) y otros 4 compañeros de IT quieren entrar al mismo tiempo, podrán hacerlo. Si intentara entrar un sexto, el switch le diría que no hay espacio
						
					- **Switch(config-line)# password**  `password` + `un espacio` + `la clave que tú quieras`
						- **¿Qué hace?** Establece la "llave" de entrada para esas líneas virtuales.
						- **Detalle importante:** Esta contraseña es la **primera barrera**. Es lo primero que te pedirá el switch apenas pongas el comando `telnet`. Si no pones una contraseña aquí, por seguridad, Cisco bloquea el acceso remoto por defecto (no deja entrar a nadie "en blanco").
					
					- **Switch(config-line)# login**
						-  **¿Para qué sirve?** Este comando es el que le da la orden al switch de **pedir** la contraseña.
						- **El porqué:** Aunque hayas puesto una contraseña con el comando anterior, si no escribes `login`, el switch nunca te la preguntará y, por lo tanto, no te dejará pasar. Es como ponerle una cerradura a una puerta pero no cerrarla con llave; el comando `login` es el que "echa la llave".
						
					- Switch(config-line)# exit
				
				-  Poner una contraseña al modo privilegiado **(enable Password)**
					- **Switch(config)# enable password [Contraseña]** 
						- **¿Qué hace?** Configura la contraseña para pasar del modo usuario (`Switch>`) al modo privilegiado (`Switch#`).
						
						- **¿Por qué es vital?** Como te mencioné antes, si intentas entrar por Telnet y el equipo **no tiene** esta contraseña configurada, el switch te desconectará por seguridad. No permite que nadie administre el equipo remotamente si no hay una clave para el modo "jefe" (privilegiado
						
						-  `enable password`: Guarda la clave en texto plano. Si alguien mira tu pantalla con el comando `show run`, leerá "la clave".
						- `enable secret`: Encripta la clave. Si alguien mira tu configuración, verá una cadena de símbolos locos y no sabrá cuál es tu contraseña. **¡Es la que deberías usar en la vida real!**
								
				- El comando de transporte (Opcional pero recomendado)
					- Switch(config)# line vty 0 4
					- **Switch(config-line)# transport input [Nombre de protrocolo]**
						- **¿Qué hace?** Define el **idioma** o protocolo que el switch aceptará para que te conectes.
						- **Anatomía:**
						    - `transport`: Se refiere al transporte de datos
								
						    - `input`: Se refiere a lo que **entra** al switch.
					
							- `Nombre del protocolo` 
							    - `telnet`: Es el nombre del protocolo.
								    - - Si pones `telnet`, estás usando un protocolo **inseguro** (porque todo lo que escribes, incluidas las contraseñas, viaja en texto plano y un hacker podría leerlo).
								    - **Por qué se usa:** Es extremadamente ligero y fácil de configurar. No requiere nada especial, solo una IP y una contraseña solo de practica.
								    
								- `ssh:` SSH (Secure Shell - El estándar actual)
									- **Comando:** `transport input ssh`
									- **Por qué se usa:** Es el protocolo profesional. Encripta toda la sesión (usuario, contraseña y comandos).
									- **Seguridad:** **ALTA.** Aunque alguien robe los datos del cable, no podrá entender nada porque todo está cifrado.
									- **Uso:** Es obligatorio en cualquier empresa real. Para usarlo en Cisco, necesitas configurar antes un `hostname` y un `ip domain-name`.
									
									- ==**Manera correcta de usarlo**==
										- **Router(config)# hostname R1**
										- **Router(config)# ip domain-name miempresa.com**
											- El SSH cifra la información usando un "Certificado Digital". Para crear ese certificado, el router combina el **nombre del equipo** + el **nombre del dominio** para crear su identidad única.

											- **Sin esto:** El router no tiene un "nombre completo" y no puede generar la llave de cifrado.
											
										- **Router(config)# crypto key generate rsa  (aquí eliges 1024)**
											- Este es el comando que **fabrica la llave de cifrado**.

											- Imagina que vas a enviar un mensaje secreto. SSH es el candado, pero este comando es el que **fabrica la llave** del candado. Si no la generas, el router no tiene con qué "enredar" la información para que sea ilegible para los demás.
											
											- Al elegir **1024**, le das una fuerza suficiente para que sea segura.
											
										- **Router(config)# username admin secret 1234  (creas un usuario real)**	
										
											- A diferencia de Telnet (que solo te pide una contraseña), SSH está diseñado para ser más serio y requiere saber **quién** está entrando.

											- **`username...`**: Creas una cuenta real (como en Windows o Facebook).
											
											- **`login local`**: Le dices a las líneas VTY: _"No pidas una contraseña suelta, busca en la base de datos interna el usuario y la clave que acabo de crear"_.
										
										- **Router(config)# line vty 0 4**
										- **Router(config-line)# login local   (le dices que use el usuario de arriba)**
										
										- **Router(config-line)# transport input ssh  (bloqueas telnet y solo dejas entrar por SSH)**
										
											- Este es el único comando que es **opcional pero recomendado**.

												- Si solo pones este, le estás prohibiendo al router hablar por Telnet.
												- **La trampa:** Si pones este comando pero **no** hiciste los 3 pasos anteriores, ¡te quedarás fuera! Porque bloqueaste Telnet y el SSH no puede iniciar por falta de llaves.

								
								- `all:` 
									- **Por qué se usa:** Permite que el equipo acepte cualquier conexión (Telnet, SSH, e incluso otros menos comunes).
									- **Seguridad:** **VARIABLE.** Es cómodo porque no te bloquea, pero es arriesgado porque dejas abierta la puerta a protocolos inseguros (como Telnet).
									- **Uso:** Útil cuando estás migrando de Telnet a SSH y no quieres quedarte fuera del equipo por error.
									
								- `none:` (El "cerrojo total")
									- - **Comando:** `transport input none`
									- **Por qué se usa:** Bloquea **todas** las conexiones remotas a través de la red.
									- **Seguridad:** **MÁXIMA.** Nadie puede entrar al equipo por red.
									- **Uso:** Se usa en equipos críticos donde la única forma permitida de configurar es conectando físicamente el cable celeste de **Consola** en el cuarto de servidores.
							    
						- **¿Por qué ponerlo?** Le estás diciendo: _"Solo deja que la gente se conecte usando Telnet"_. Si en el futuro quieres más seguridad, usarás `transport input ssh`, que es una versión encriptada.

			- ==Acceder desde el PC al switch==
				- 1. Haz clic en la **PC**.
					1. Ve a la pestaña **Desktop**.
					2. Abre el **Command Prompt** (la terminal negra).
					3. Escribe el comando: `telnet [IP_DEL_SWITCH]` (ejemplo: `telnet 192.168.88.2`).
					4. **El ritual de entrada:**
					    - Te dirá: _User Access Verification_.
					    - Escribe la **Password del VTY** (la primera que creamos). Recuerda: **no se ve nada mientras escribes**, es por seguridad. Dale Enter.
					    - Verás el nombre del switch así: `Switch_Comedor>`.
					    - Escribe `enable`.
					    - Te pedirá la segunda contraseña (la de `enable password/secret`). Escríbela y dale Enter.
					    - ¡Listo! Verás el símbolo `#` (`Switch_Comedor#`). Ya estás "dentro" del cerebro del equipo.
					
					5. ¿Qué puedes hacer desde ahí? (Control Total)
						
						- Absolutamente **todo** lo que haces estando pegado al equipo con el cable de consola, lo puedes hacer por Telnet:
							
							- **Ver información:** Puedes usar todos los comandos `show` (`show ip interface brief`, `show vlan brief`, `show run`). Es genial para monitorear si un puerto se cayó o si hay errores desde tu oficina.
							- **Configurar:** Puedes entrar a `configure terminal`, crear nuevas VLANs, apagar puertos (`shutdown`), cambiar nombres, etc.
							- **Reiniciar o Guardar:** Puedes usar `write` para guardar cambios o `reload` para reiniciar el equipo a distancia.
						
					6. ¿Por qué usamos Telnet en tu pregunta?
						
						- Usaste **Telnet** porque es el protocolo que configuramos con el comando `transport input telnet`. Es como si hubieras abierto una "llamada telefónica" de datos entre tu PC y el Switch.
							
					7. ¿Es este el objetivo final?
						- **Sí.** El objetivo de un administrador de redes es tener una **Red Gestionable**. Una red donde puedas ver y arreglar todo desde un solo punto (tu PC de IT) sin tener que caminar por todo el edificio.
							
						- **Un pequeño aviso de realidad:**  
							Como entraste por **Telnet**, recuerda que si un hacker está "escuchando" el cable entre tu PC y el Switch, podrá ver tus contraseñas. Por eso, en cuanto domines esto, tu siguiente paso lógico será cambiar ese "idioma" de Telnet a **SSH** para que todo viaje encriptado.

			- ==Restricción de Acceso (Management Plane Protection), configurar el firewall==

				- Primero se aplica los comando a todos los Switch y aparatos como el Router.
				- **Debe decirles que solo acepten conexiones de la IP específica de tu PC de TI:** tiene como objetivo crear una **Lista de Invitados VIP**.
				
					- **Switch(config)# access-list 10 permit 192.168.88.10**
					
						- Explicacion del comando
							1. `access-list` (El comando principal)
	
								Es la instrucción para crear una **ACL (Access Control List)**
								Imagínalo como si estuvieras redactando una lista de reglas en un cuaderno. Por sí sola, la lista no hace nada; es solo una definición de quién tiene permiso y quién no.
								
							2. `10` (El número de identificación es como el nombre de la lista para su búsqueda )
								
								- Este número le dice al Switch qué **tipo** de lista es:
								
									- **Del 1 al 99:** Son "Estándar". Solo pueden filtrar basándose en la **dirección IP de origen** (de dónde viene el mensaje).
										- **1 al 99 (y del 1300 al 1999):** Son ACLs **Estándar**. Solo miran la IP de origen (quién envía).
										- **100 al 199 (y del 2000 al 2699):** Son ACLs **Extendidas**. Pueden mirar origen, destino, y si es un correo, una web, un ping, etc.
										
									- Es como un guardia que solo mira el nombre en el documento de identidad, pero no le importa a dónde vas ni qué llevas en la maleta.
								
							3. `permit` (La acción)
								
								Aquí defines qué hará el Switch cuando encuentre una coincidencia.
								
								- **Permit:** Deja pasar el tráfico.
								- **Deny:** Bloquea el tráfico.
								- **Ojo:** Al final de toda lista de acceso de Cisco hay un "Deny" invisible. Si no estás en la lista como "permitido", el Switch te bloquea por defecto.
								
							4. `192.168.88.10` (El objetivo específico)
								
								Esta es la dirección IP de tu **PC de Zona TI**.
								
								- Al poner la IP exacta, le estás diciendo al Switch: "Busca exactamente a este equipo".
								- Como no pusiste una máscara al final (wildcard), Cisco asume que te refieres únicamente a ese host (esa computadora específica).
				
				- **Asegurar las líneas VTY**
				
					- **Switch(config)# line vty 0 15** => para el router es otro rango o dependiendo el equipo
					
						- Entra a la configuración de las **VTY (Virtual Teletype)**.
							- **La idea**: Los switches no tienen un monitor y teclado físico pegados siempre; se administran por red. Estas "líneas" son los **puertos virtuales** que permiten conexiones por Telnet o SSH.
							- **El 0 15**: Significa que estás configurando las 16 sesiones simultáneas que soporta el switch (desde la sesión 0 hasta la 15). Al poner el rango completo, aseguras que no quede ninguna "puerta trasera" abierta sin protección.
							
					- **Switch(config-line)# access-class 10 in**
					
						- Este es el comando clave, el "guardia de seguridad".

							- **`access-class`**: Es el comando hermano de `access-list`, pero diseñado específicamente para aplicarse en las líneas de terminal (VTY).
							- **`10`**(nombre de la lista): Aquí es donde "llamas" a la lista que creamos antes. Le estás diciendo: _"Usa las reglas que escribí en la access-list 10"_.
							- **`in`**: Significa que el filtro se aplica al tráfico que **entra** al switch.
							
						- Que es lo que hace final mente estos comandos
							- **Filtro Inmediato**: Cuando alguien intenta conectar por Telnet/SSH, el switch mira la IP de quien llama.
							- **Verificación**: Si la IP es la **192.168.88.10** (tu PC de TI), el switch le dice: _"Adelante, pon tu contraseña"_.
							- **Bloqueo Silencioso**: Si la IP es de Ventas o Alumnos, el switch **rechaza la conexión de inmediato**. Ni siquiera les da la oportunidad de intentar adivinar la contraseña.
					
				- ==**Proteger un Router:** ==
					- El router es el que comunica a las VLANs, por lo que él puede bloquear el tráfico antes de que llegue a su destino.
				
					- **Paso A: Bloquear el acceso remoto al Router**  
						- Igual que en los switches, aplica la lista en el router para que solo tu PC pueda entrar a configurarlo:
						
							- **Router(config)# access-list 10 permit 192.168.88.10**
							
								- **El objetivo:** Identificar tu PC de la Zona TI como la **única** autorizada para administrar el router.
							
								- **Diferencia clave:** En el router, esta lista es vital porque el router tiene muchas IPs (una por cada subinterfaz de VLAN). Sin esta lista, alguien desde la VLAN 10 podría intentar entrar usando la IP `192.168.1.1`. Esta regla dice: "No importa a qué IP del router le hablen, solo la IP 88.10 tiene permiso".
								
							- **Router(config)# line vty 0 4**
							
								- **¿Por qué 0 4?**: A diferencia de los switches (que suelen tener 16 líneas, 0-15), los routers Cisco por defecto suelen habilitar **5 sesiones simultáneas** (de la 0 a la 4).
								
								- **La idea:** Estás entrando al "pasillo" por donde viaja el tráfico de Telnet y SSH para configurar el equipo.
								
							- **Router(config-line)# access-class 10 in**
							
								- **El comando "Escudo":** Aquí es donde el router activa el filtro.
								- **Lo que sucede en la práctica:** Cuando el router recibe un intento de conexión remota, antes de preguntar `User` o `Password`, chequea la IP de origen:
								    - **¿Viene de la 192.168.88.10?** -> _"Adelante, identifícate"_.
								    - **¿Viene de cualquier otra IP (Ventas, Alumnos)?** -> _"Conexión rechazada por el host"_. El router ni siquiera les contesta.
								    
					- Sin el `access-class`, el router le pediría la clave una y otra vez, gastando CPU y dándole oportunidad al atacante.
					- Con el `access-class`, el router **ignora** al atacante. Es como si el router fuera invisible para cualquiera que no seas tú en tu PC de TI.
						
						**Un detalle importante:**  
						
						Como ahora tienes este "filtro" puesto, si por alguna razón cambias la IP de tu computadora de TI (ejemplo, a la `.11`), **tú mismo te quedarás fuera** del router. Por eso, en la vida real, los administradores de TI siempre tienen IPs fijas.
					
					- **Paso B: Evitar que las otras VLANs "vean" a la Zona TI** 
					
						- Para que los PCs de Ventas o Alumnos ni siquiera puedan hacerle _ping_ a tus switches o a tu PC de TI, puedes crear una ACL extendida y aplicarla en las subinterfaces:
						
						- **Esta regla bloquea cualquier tráfico que vaya hacia la red de TI (88.0)** => esto solo específica las reglas no se levanta el muro osea no bloquea el pign aun o bloquea un paquete 
						
							- **Router(config)# ip access-list extended BLOQUEO_TI**
							
								- **`ip access-list extended`**: A diferencia de las anteriores, aquí no usamos un número (como el 10), sino un **nombre**. Las ACL nombradas son mejores porque puedes editarlas más fácil y el nombre ya te dice qué hace.
								
								- **`extended`**: Esto le dice al router: "Prepárate, porque te voy a dar detalles de origen, destino y protocolo". Es un filtro mucho más fino.
								
								- **`BLOQUEO_TI`**: Es el nombre de tu regla. ¡Ojo! Los routers distinguen entre mayúsculas y minúsculas.
								
							- **Router(config-ext-nacl)# deny ip any 192.168.88.0 0.0.0.255**
							
								-  **`deny`**: La acción. Prohibir el paso.
								- **`ip`**: El protocolo. Al poner `ip`, bloqueas **todo**: pings (ICMP), navegación web (HTTP), transferencias de archivos (FTP), etc.
								
								- **`any`**: El **Origen**. Significa "cualquier equipo del mundo". No importa de dónde venga el paquete.
								
								- **`192.168.88.0`**: El **Destino**. Es la red de tu Zona TI.
								- **`0.0.0.255`**: Se llama **Wildcard**. Es lo opuesto a la máscara de red. Le dice al router: "Solo fíjate en los primeros tres grupos (192.168.88) y no me importa el último número (.1, .2, .10, etc.)".
								    - **En resumen:** Bloquea a cualquiera que intente tocar a cualquier equipo de la red 88 osea cubre todas las ip por el rango de la mascara.
								
							- **Router(config-ext-nacl)# permit ip any any**
						
								- **Este es el comando más importante de esta lista.**

									- **¿Por qué?**: En Cisco, si creas una lista y no pones un `permit` al final, el router bloquea **absolutamente todo** por defecto (un "Deny" invisible).
									- **La lógica**: Sin esta línea, los PCs de Ventas no podrían entrar a la Zona TI (bien), ¡pero tampoco podrían salir a Internet ni hablarse entre ellos! (mal).
									- **El objetivo**: "Bloquea lo que te dije arriba, pero **permite todo lo demás**".
						
						- **Luego la aplicas en las interfaces de las otras VLANs (ejemplo VLAN 10):**
							- **Router(config)# interface g0/0.10**
							
								- `interface g0/0.10`
									- **¿Qué estás haciendo?**: Estás entrando específicamente a la **subinterfaz** que creaste para la VLAN 10 (Ventas o Alumnos).
									- **La lógica**: Las reglas de seguridad se aplican en las interfaces porque es por donde "entra" y "sale" el tráfico del router. Para que el router detenga a un usuario de la VLAN 10, tienes que pararte en su puerta.
									
							- **Router(config-subif)# ip access-group BLOQUEO_TI in**
							
								- Este es el comando que activa el filtro. Vamos a desmenuzarlo:
									- **`ip access-group`**: Es el comando que vincula una ACL extendida a una interfaz física o lógica.
									- **`BLOQUEO_TI`**: Es el nombre exacto de la lista que creamos antes. (Si te equivocas en una letra o mayúscula, no funcionará).
									- **`in` (Dirección del tráfico)**: ¡Este es el punto más importante!
									    - **`in` (Entrada)**: Significa que el router revisará el paquete **en cuanto llegue** desde el switch de la VLAN 10.
									    - **¿Por qué es mejor `in`?**: Porque si el paquete va hacia la Zona TI (la red 88), el router lo descarta **antes** de gastar recursos procesando a dónde enviarlo. Es como un guardia que te detiene en la puerta del edificio, no cuando ya estás en el ascensor.

			- ==**Asignar el trunk y VLANS nativa, el protocolo dot1Q**== 
				- el router no se le asigna una VLNS o por equivocación meter una VLNS la idea es donde se conecto el router hacer esto ==**_Inter-VLAN Routing_**==
				
					- `Switch(config)# interface fa0/1` => el 0/1 es el puerto al que vamos a asignar así que hay que tener en cuenta para no configurar mal

					- `Switch(config-if)# switchport mode trunk` => recordando que donde se conecta el router también es trunk 
					
				- **VLANs nativa** - Como todo el mundo sabe que la VLAN 1 es la de fábrica, los hackers suelen usarla para intentar entrar a los switches, - Al cambiarla a una que tú inventes (por ejemplo, la 99 o la 999) y que no se use para nada más, "escondes" ese tráfico sin etiqueta.
					- No olvidar que antes hacer el siguiente proceso toca toca crear la VLANS entes
			
					- `interface fastethernet [Numero de puerto]` (El puerto que une los switches).
					- `switchport mode trunk`
					- `switchport trunk native vlan 99`=> Cualquier basura o tráfico sin etiqueta que llegue por aquí, mándalo a la oficina de TI (VLAN 99), no lo dejes en la red común
					
				- cuando ya se configure el puerto ahora hay que hacerlo en el router esto se llama **_Router-on-a-Stick_** un solo cable (el "stick") lleva todo el tráfico de muchas VLANs.
				- como no se puede poner dos IP al mismo puerto físico se crea **sub-interfaces** 
		
					- ==Router(config)# **interface gig0/0.10**==
						- se crea  **sub-interfaces** el numero **/0.10** es el numero que varia y donde decimos vamos a entrar a este fragmento del puerto original **gig0/0** se crean las de las VLANS **0/0.10, 0/0.20, 0/0.30**
					
					- Router(config-subif)# **encapsulation dot1Q 10** 
						- el 10 es el de la VLANS que ya configuramos 
						-  traductor de etiquetas o de VLANS, **dot1Q:** Es el estándar universal (802.1Q)
						
					- Router(config-subif)# **ip address 192.168.10.1 255.255.255.0**
						- Ahora que el router ya sabe separar los paquetes de la VLAN 10, necesita una identidad en esa red.
						- Esta IP será la **Puerta de Enlace (Default Gateway)** que debes configurar en las PCs que pertenecen a la VLAN 10.
						- Sin esta IP, las computadoras de la VLAN 10 pueden hablar entre ellas (gracias al switch), pero nunca podrían salir hacia la VLAN 20 o a Internet.
						
						
					- Por ultimo encendemos el equipo
						- Router(config)# interface gig0/0
						- Router(config-if)# no shutdown
						- Router(config-if)# exit

					
				- también hay que tener en cuenta que si las IP de los computadores no esta bien configurada esto generara problemas 
						**IPs de los equipos (PCs):** Asegúrate de que tus computadoras tengan IPs que coincidan con sus subinterfaces:
						- **PC en VLAN 10:** IP `192.168.10.x` / Gateway: `192.168.10.1`
						- **Laptop en VLAN 20:** IP `192.168.20.x` / Gateway: `192.168.20.1`
	
	2. **ACL (Access Control Lists o Listas de Control de Acceso:** Al tener varias VLAS no controlamos el acceso a aras importantes esto evita el acceso de VENTAS a GERENCIA
			- `access-list 101 permit ip host 192.168.50.10 192.168.30.0 0.0.0.255`
			
		1. El Origen (`host 192.168.50.10`)

			- Aquí dices **quién** quiere pasar. Al usar la palabra `host`, le indicas al router: _"Solo esta computadora exacta"_. Si quisieras que fuera todo el departamento de Ventas, pondrías la red completa (`192.168.50.0 0.0.0.255`).
			
			- El Destino (`192.168.30.0 0.0.0.255`)
		
		2. Aquí dices **a dónde** intenta ir.
		
			- Si solo pones la IP de origen, el router bloquearía a esa PC para **toda la red**, ¡incluyendo internet!
			- Al poner el destino, le dices: _"Esta PC de Ventas tiene permiso de entrar **solo** a la calle de IT (la 192.168.30.x)"_.
		
		3. La Wildcard (`0.0.0.255`)
		
			- Esa "máscara al revés" es la **Wildcard**. No es la máscara de red normal, es una instrucción para el router:
			
			- **0** significa: "Este número tiene que ser igual".
			- **255** significa: "Este número no me importa, puede ser cualquiera".
			
			- Entonces, `192.168.30.0 0.0.0.255` significa: _"Cualquier IP que empiece con 192.168.30, no importa si termina en .5, .10 o .200"_.
			- 
     3. **Configuración de EtherChannel:** para cuando tenemos un switch con puertos libre  podemos mejorar la velocidad de los datos usando el comando de unos de esto protocolos **PAgP**, **LACP** y **Vía Modo ON** y por ultimo se ponen como trunk
		 
		 - **PAgP**, **LACP** y **Vía Modo ON** son los protocolos (los "idiomas") que usa para ponerse de acuerdo
			 1. **PAgP (Port Aggregation Protocol)**
				 - Es el protocolo **dueño de Cisco**. Solo funciona si ambos switches son de la marca Cisco.
				- **Modo Desirable (Deseado):** El switch toma la iniciativa y pregunta al otro: "¿Quieres formar un grupo?".
				- **Modo Auto:** El switch es tímido; espera a que el otro le pregunte para unirse.
				- **Comando**
					- interface range fa0/10-11
					 - channel-group 1 mode desirable

						- `interface range`: Seleccionas los cables físicos.
						- `channel-group 1`: Creas la interfaz lógica "Port-Channel 1".
						- `mode desirable`: Activa PAgP en modo activo.
				
			2. **LACP (Link Aggregation Control Protocol)**
			
				- Es el estándar **Universal (IEEE 802.3ad)**. Es el que más se usa en la vida real porque permite unir un switch Cisco con uno HP, Dell o un servidor Linux.

				- **Modo Active:** El switch busca activamente formar el grupo.
				- **Modo Passive:** El switch espera a que el otro inicie la negociación.
				- **Comando**
					- interface range fa0/10-11
					- channel-group 1 mode active
						- `mode active`: Activa LACP (es el equivalente al "desirable" de Cisco pero universal)	
						
			3. **Modo ON (Sin protocolo)**

				Aquí no hay negociación. Tú obligas a los puertos a agruparse sin preguntar nada.
				
				- **Peligro:** Si hay un error de cableado, puede generar bucles (loops) que tumben la red porque no hay un protocolo "inteligente" revisando la conexión. 
				- **Comando**
					- interface range fa0/10-11
					 - channel-group 1 mode on
					 
			4. **Poner trunk**
				 - channel-group 1 mode desirable => como ya están agrupados con este comando selecciono el grupo y despues aplico trunk 
				 - switchport mode trunk
	 - ![[1_ethernChanel.png]]

# 2_ayudas 
1. cuando se escribe el comando mal el quipo empieza a buscar otra cosa y para evitar demoras se usa el comando **`Ctrl` + `Shift` + `6`**
		La solución definitiva (Comando mágico)
			Para que no te vuelva a pasar eso cada vez que te equivoques al escribir, vamos a desactivar esa búsqueda. Haz esto en orden:
			1. Escribe: **`enable`** (asegúrate de que esté bien escrito).
			2. Escribe: **`configure terminal`**.
			3. Escribe este comando: **`no ip domain-lookup`**.
			4. Presiona **Enter**.
			A partir de ahora, si escribes algo mal, el Router simplemente te dirá "comando desconocido" al instante, sin quedarse trabado buscando en internet.
		
2.  A veces lo que quieres es "limpiar" porque te salen mensajes del sistema justo cuando estás escribiendo. Para que los mensajes no se mezclen con lo que escribes, usa este comando:

	1. Entra a: `configure terminal`
	2. Escribe: `line con 0`
	3. Escribe: **`logging synchronous`**
	
	- **Para qué sirve:** No limpia la pantalla, pero hace que si sale un mensaje del sistema, tu comando se mueva a una línea nueva limpia para que no te confundas.

3. **Probar conexión:**
	1. **Desde el Reuter** en la terminal del Reuter de pone **`ping [ip asignada o ip ]`** si sale **!!!!!** es por que hay conexión pero si sale **(.....)**   algo salió mal 
	
	2. **Equipos:** ir al PC o equipo dar click y no vamos a **Desktop** y damos click en **Command Prompt**  y nos sale el CLI del equipo y volvemos a poner **`ping [ip asignada o ip a probar]`**