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
	
	6. ==**`copy running-config startup-config`**:== Guarda los cambios realizados en la memoria persistente del router  le dice al equipo: _"Toma todo lo que acabo de configurar en la RAM y cópialo en el disco duro para que no se borre"_ pero también se puede evitar  escribir todo eso es muy largo. En Cisco (y en Packet Tracer) anivel **Switch ># copy**
	7. puedes usar la versión ultra resumida que hace exactamente lo mismo: Solo escribe: **`wr`** (que significa _write_) y dale a **Enter** también tener encuentra que este código se puede ejecutar en el nivel de **(#)** también se puede usar **`(config-if)# do write o (config-line)# do write`** en caso que no estemos en el nivel de **(#)**

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