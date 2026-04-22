1. **no**: Para evitar el diálogo de configuración inicial esto sale apenas se ingresa al CLI del equipo.

2. **nivel de privilegio:** cuando se abre la terminal aparece el nivel de estado con unos símbolos después del nombre ejemplo => Router >, Router#.

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
		
3. **Comandos mas utilizados:**
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
				
			- **Asignar una VLANS a los puertos** 	
				- Switch(config)# **interface range fastEthernet 0/2 - 10**
				
				- Switch(config-if-range)# **switchport access vlan 20**=> el numero 20 es la VLAN que ya configuramos puede ser la 20 o la 10 si no se crea la VLANS nos saldrá este paso al asignarle 
		
				- si nos equivocamos en un puerto lo corregimos de dos maneras
					1. lo sacamos de la VLANS que ya creamos
						- `Switch(config)# interface fastEthernet [numero del puerto]`
						- `Switch(config-if)#no switchport access vlan 10`
						
					2. le asignamos la VLANS 1 que por defectos todos están 
						- `Switch(config)# interface fastEthernet [numero del puerto]`
						- `Switch(config-if)#`switchport access vlan 1``
		
			- **Asignar el trunk y VLANS nativa, el protocolo dot1Q** 
				- el router no se le asigna una VLNS o por equivocación meter una VLNS la idea es donde se conecto el router hacer esto **_Inter-VLAN Routing_**
				
					- Switch(config)# **interface fa0/1** => el 0/1 es el puerto al que vamos a asignar así que hay que tener en cuenta para no configurar mal

					- Switch(config-if)# **switchport mode trunk**
					
				- **VLANs nativa** - Como todo el mundo sabe que la VLAN 1 es la de fábrica, los hackers suelen usarla para intentar entrar a los switches, - Al cambiarla a una que tú inventes (por ejemplo, la 99 o la 999) y que no se use para nada más, "escondes" ese tráfico sin etiqueta.
					- No olvidar que antes hacer el siguiente proceso toca toca crear la VLANS entes
			
					- `interface fastethernet 0/24` (El puerto que une los switches).
					- `switchport mode trunk`
					- `switchport trunk native vlan 99`
					
				- cuando ya se configure el puerto ahora hay que hacerlo en el router esto se llama **_Router-on-a-Stick_** un solo cable (el "stick") lleva todo el tráfico de muchas VLANs.
				- como no se puede poner dos IP al mismo puerto físico **sub-interfaces** 
		
					- Router(config)# **interface gig0/0.10**
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

					
				también hay que tener en cuenta que si las IP de los computadores no esta bien configurada esto generara problemas 
					**IPs de los equipos (PCs):** Asegúrate de que tus computadoras tengan IPs que coincidan con sus subinterfaces:
					- **PC en VLAN 10:** IP `192.168.10.x` / Gateway: `192.168.10.1`
					- **Laptop en VLAN 20:** IP `192.168.20.x` / Gateway: `192.168.20.1`
	
	9. **ACL (Access Control Lists o Listas de Control de Acceso:** Al tener varias VLAS no controlamos el acceso a aras importantes esto evita el acceso de VENTAS a GERENCIA
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
     10. **Configuración de EtherChannel:** para cuando tenemos un switch con puertos libre  podemos mejorar la velocidad de los datos usando el comando de unos de esto protocolos **PAgP**, **LACP** y **Vía Modo ON** y por ultimo se ponen como trunk
		 
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

	12. **`show running-config`**: Para ver qué has hecho hasta ahora saldrá la palabra **MORE** significa que hay mas información y solo es presionar **ENTER** para seguir bajando y cuando ya no haya mas información saldrá ***END**. 
	
	13. Para confirmar **`Switch# show vlan brief`** 
	
	14. **`no shutdown`**: ¡El más importante! Por defecto, los puertos de los routers Cisco vienen "apagados" (en rojo). Este comando los **enciende** esto se ejecuta en el nivel **`Router(config-if)#`**
	
	15. ==**`copy running-config startup-config`**:== Guarda los cambios realizados en la memoria persistente del router  le dice al equipo: _"Toma todo lo que acabo de configurar en la RAM y cópialo en el disco duro para que no se borre"_ pero también se puede evitar  escribir todo eso es muy largo. En Cisco (y en Packet Tracer) anivel **Switch ># copy**
	16. puedes usar la versión ultra resumida que hace exactamente lo mismo: Solo escribe: **`wr`** (que significa _write_) y dale a **Enter** también tener encuentra que este código se puede ejecutar en el nivel de **(#)** también se puede usar **`(config-if)# do write`** en caso que no estemos en el nivel de **(#)**
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