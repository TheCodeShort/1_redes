# 1_Definición: El salto de LAN a WAN

- **¿Qué es?** Mientras que una LAN (==**L**ocal **A**rea **R**etwork - Red de Área Local==) conecta computadoras dentro de un mismo edificio bajo tu control, una WAN (==**W**ide **A**rea **R**etwork - Red de Área Amplia==) conecta redes geográficamente separadas cruzando ciudades, países o continentes.
    
- **La gran diferencia:** En la LAN (==**L**ocal **A**rea **R**etwork - Red de Área Local==) tú eres el dueño de los cables. En la WAN (==**W**ide **A**rea **R**etwork - Red de Área Amplia==), tienes que **alquilarle** los enlaces a un proveedor de servicios (como Claro, Movistar o AT&T) y configurar protocolos especiales de Capa 2 y Capa 3 para que tu información viaje segura y encapsulada por su infraestructura.

# 2_ MPLS (Multi-Protocol Label Switching)

1. Es el "estándar de oro" de las redes corporativas tradicionales.

	- **¿Qué es?**: Es una técnica de transporte de datos que usa **etiquetas** en lugar de direcciones IP para mover el tráfico.
	
	- **¿Cómo funciona?**: Imagina que el router de la WAN es un clasificador de correo. En lugar de leer toda la dirección (IP) en cada parada, simplemente le pega una etiqueta de color al paquete: _"Los paquetes con etiqueta roja van por el camino rápido a la oficina central"_.

	- **El Gran Beneficio**: Permite crear lo que llamamos **VPN de Capa 3**. La red del proveedor se vuelve invisible para ti; tus oficinas en diferentes ciudades se ven como si estuvieran conectadas directamente, y el proveedor garantiza que tu tráfico de voz (llamadas) tenga prioridad sobre los correos.
	
2. **Metro Ethernet (Ethernet de Ciudad)**

	Es llevar la tecnología que usas en tu oficina ==(Ethernet/Capa 2 **802.3/802.1Q**==) a toda la ciudad.
	
	- **¿Qué es?**: Es una red de alta velocidad (Fibra Óptica) que conecta diferentes puntos de una zona metropolitana.
	- **¿Cómo funciona?**: El proveedor te entrega un cable que parece un cable de red normal (RJ-45 o Fibra). Tú lo conectas a tu switch y, mágicamente, ese cable llega hasta tu otra oficina a 20 km de distancia.
	- **El Gran Beneficio**: Es **extremadamente simple**. Como es Ethernet, no necesitas protocolos complejos de WAN. Tus switches de la oficina A y la oficina B pueden hablar directamente, compartir VLANs y usar el mismo direccionamiento como si estuvieran en el mismo edificio.

3. **Túneles (El concepto general)**

	Es la técnica de **Encapsulamiento** que mencionamos antes.
	
	- **¿Qué es?**: Es meter un paquete de datos dentro de otro paquete para transportarlo a través de una red que "no lo entiende" o que es pública.
	- **¿Cómo funciona?**: Imagina que quieres enviar una carta privada (tus datos) por el correo público (Internet). Metes tu carta en un sobre más grande con una dirección de destino pública. El Internet solo ve el sobre grande y lo entrega. Al llegar al otro lado, el router rompe el sobre grande y saca la carta privada original.
	- **Tipos comunes**:
	    - **GRE**: Crea el túnel pero no cifra (es rápido pero inseguro).
	    - **IPsec**: Crea el túnel y además **cifra** todo (es seguro pero consume más procesador).


| Tecnología         | Distancia       | ¿Quién la controla?   | Ventaja Principal                             |
| ------------------ | --------------- | --------------------- | --------------------------------------------- |
| **Metro Ethernet** | Local/Ciudad    | Proveedor (ISP)       | Simplicidad y velocidad extrema.              |
| **MPLS**           | Global/País     | Proveedor (ISP)       | Garantía de calidad (QoS) y orden.            |
| **Túneles (VPN)**  | Global/Internet | **TÚ (El Ingeniero)** | Privacidad total y bajo costo (usa Internet). |
4. **VPN ==(Virtual Private Network** (Red Privada Virtual).)== 

	- **Virtual:** Significa que no hay un cable físico de 500 km uniendo tus dos oficinas. La conexión se crea mediante software.
	- **Privada:** Aunque tus datos viajen por el Internet público (donde hay hackers, gobiernos y otros usuarios), nadie puede ver lo que hay dentro.
	- **Red:** Porque al final, tus dispositivos se sienten como si estuvieran conectados al mismo switch de la oficina.
	
- **¿Cómo funciona una VPN a fondo? (El concepto del Túnel Cifrado)**

	Imagina que quieres enviar un lingote de oro (tus datos) desde tu casa a tu oficina, pero la única forma de enviarlo es a través de una calle pública llena de ladrones (Internet).
	
	1. **Cifrado (La Caja Fuerte):** Antes de que el dato salga de tu router, la VPN lo mete dentro de una "caja fuerte" matemática. Si un hacker captura el paquete en medio del camino, solo verá basura digital ilegible.
	2. **Túnel (El Camión Blindado):** La VPN crea un camino lógico directo entre el origen y el destino. A esto le llamamos "Tunelización".
	3. **Autenticación (El Guardia):** La VPN se asegura de que solo los dispositivos autorizados (con la llave correcta) puedan abrir esa caja fuerte al llegar al destino.
	
- **Existen dos formas de usar esto:**

	1. **VPN de Sitio a Sitio (Site-to-Site):**
	    - Se usa para conectar **dos oficinas completas**.
	    - Los routers de cada oficina crean el túnel entre ellos. Los empleados de la oficina A pueden imprimir en la impresora de la oficina B como si estuvieran ahí mismo. Ellos ni siquiera saben que hay una VPN; el router hace todo el trabajo.
	2. **VPN de Acceso Remoto (Client-to-Site):**
	    - Es la que usas tú cuando trabajas desde un café o desde tu casa.
	    - Instalas un software (como Cisco AnyConnect) en tu laptop. Este software crea el túnel seguro desde tu computadora hasta el router de la empresa. 
# 3_Topologías (¿Cómo dibujamos la red gigante?)
Aunque la capa física se encarga de los cables y señales, la topología define **cómo se organiza la comunicación** entre los nodos

La topología de red es la **disposición física o lógica** en la cual los dispositivos (nodos) de una red (computadoras, impresoras, servidores) se interconectan entre sí para intercambiar datos.

1. **Existen dos formas de ver una topología:**

	- **Física:** Es el diseño real del cableado y la ubicación de los dispositivos.
	- **Lógica:** Es la forma en que los datos viajan por la red, independientemente de cómo estén conectados los cables.

2. Tipos de Topologías (Basado en la evolución de las redes)

	* ***A. Topología de Bus (La Histórica)**
	
		- **Cómo funciona:** Todos los dispositivos están conectados a un único cable central (backbone).
		- **El riesgo:** Si el cable principal falla, toda la red cae. Era común en las primeras redes LAN con cable coaxial.
	
	* **B. Topología de Estrella (La Estándar Actual)Hub and Spoke**
	
		- **Cómo funciona:** Todos los nodos se conectan a un dispositivo central, usualmente un **Switch** o un **Hub** (p. 5).
		- **Ventaja de experto:** Es la más usada en redes empresariales (LAN). Si un cable de una computadora se rompe, el resto de la red sigue funcionando perfectamente.
	
	* **C. Topología de Anillo**
	
		- **Cómo funciona:** Cada dispositivo tiene exactamente dos vecinos. Los datos viajan en una sola dirección.
		- **Uso:** Protocolos como _Token Ring_ (mencionados en la pág. 17 de tu doc) utilizaban esta lógica para evitar colisiones de datos.
	
	* **D. Topología de Malla (Mesh)**
	
		- **Cómo funciona:** Todos los nodos están conectados entre sí.
		- **Relación con las WAN:** Es la base de las **Redes WAN** y de Internet. Al tener múltiples caminos, si un nodo falla, el paquete busca otra ruta (conmutación de paquetes), lo que la hace altamente tolerante a fallos.


3. ¿Por qué es importante para un Ingeniero?

	Como bien dice la conclusión de tu documento, elegir la topología adecuada impacta directamente en la **escalabilidad, velocidad y eficiencia** de la infraestructura.

> **Ejemplo de Experto:** En una red WAN de una multinacional (como el ejemplo de la página 2 [[2_redes.pdf#page=139&selection=107,3,107,20|2_redes, página 139]]), no se usa una estrella simple, sino una **topología de malla parcial** mediante enlaces cifrados para asegurar que la comunicación nunca se interrumpa entre sedes (p. 2).

# 4_Protocolo capa 2 (WAN)
**Redes WAN** y la **Conmutación**, ==conecta directamente con la **Capa 2 (Enlace de Datos)**==. En una WAN no basta con tener el cable (Capa 1); necesitamos reglas para que los paquetes viajen de un nodo a otro de forma organizada.

Según tu documento (especialmente en las páginas 10, 17 y 20), aquí están los protocolos y conceptos clave de la Capa 2 que debes dominar:

1. **El Rol de la Capa 2 en WAN**

	Mientras que en una red local (LAN) usamos principalmente Ethernet, en las redes WAN de largo alcance, la Capa 2 se encarga de empaquetar los datos en **Tramas** (su unidad de medida o PDU) para que puedan saltar entre los nodos de los proveedores de servicios.

2. Protocolos de Capa 2 mencionados en tu material

	En la página 20, tu documento lista protocolos específicos que operan en este nivel. Aquí te los desgloso para tu investigación:
	
	1. **Ethernet (IEEE 802.3):** Aunque nació para LAN, hoy es la base de las conexiones WAN modernas **(Metro-Ethernet)** .
		- Esto es lo que hace Ethernet/VLAN en el mundo WAN
			1. El "Túnel" de Capa 2 (Extensión de la LAN)

				Imagina que tienes una oficina en Bogotá y otra en Medellín. Quieres que la **VLAN 10** de Bogotá se hable con la **VLAN 10** de Medellín como si estuvieran conectadas al mismo switch físico.
				
				- En la WAN, el proveedor usa **enlaces troncales extendidos** para transportar tus etiquetas de VLAN a través de la ciudad o el país.
				- Esto permite que un servidor en una ciudad y un usuario en otra compartan la misma red lógica sin necesidad de pasar por un router (Capa 3) en el medio.
				
			2. Metro Ethernet: El estándar de oro actual
				
				 **Metro Ethernet** es la expansión de Ethernet sobre infraestructura metropolitana .
				
				- **¿Qué hace?** El proveedor te entrega una interfaz Ethernet de alta velocidad.
				- **¿Cómo lo gestiona?** Usa las VLANs y la **QoS (Calidad de Servicio)** para garantizar que tu tráfico de voz o video llegue primero, incluso viajando por la red de la ciudad.
				
			3. Integración con SD-WAN y Túneles
				
				Cuando la Ethernet WAN no es suficiente, tu documento explica que usamos **Túneles GRE**.
				
				- Si el proveedor no te permite pasar etiquetas VLAN directamente, el router "encapsula" toda tu trama de Ethernet (con su etiqueta de VLAN) dentro de un paquete IP.
				- Esto hace que el tráfico interno de tus VLANs viaje de forma invisible a través de la red del proveedor (Internet o MPLS).
				
	2. **PPP (Point-to-Point Protocol):** Fundamental para conexiones directas entre dos nodos WAN (como el enlace de tu casa al ISP).
		1. Es como un **"túnel de seguridad y control"** que se construye justo encima del cable físico para que dos routers puedan hablar un mismo idioma, sin importar si el cable es de cobre, fibra o una línea telefónica vieja.
		
			1. La Idea: ¿Para qué sirve?
			
				Cuando conectas dos routers directamente (enlace punto a punto), necesitas un protocolo que no solo mande bits, sino que también:
				
				- **Se identifique:** "¿Eres realmente el router de la sucursal o un impostor?" (**Autenticación**).
				- **Revise la calidad:** "¿El cable está funcionando bien o hay mucho ruido?" (**Monitoreo**).
				- **Hable varios idiomas:** ¿Vamos a pasar tráfico IPv4, IPv6 o ambos? (**Multicapa**).
				
			2. ¿Cómo funciona? (Los dos "cerebros" de PPP)

				PPP no es un solo protocolo, sino la unión de dos sub-protocolos que trabajan en la **Capa 2 (Enlace de Datos)**:
				
				- **A. LCP (Link Control Protocol) - "El Establecedor"**
				
					Es el encargado de la parte física y el enlace. Sus tareas son:
					
					1. **Establecer:** Levanta la conexión.
					2. **Configurar:** Acuerda el tamaño de los paquetes.
					3. **Autenticar (Opcional pero clave):** Aquí es donde entran **PAP** (básico) o **CHAP** (seguro).
					4. **Terminar:** Cierra el enlace de forma ordenada cuando ya no se necesita.
				
				- **B. NCP (Network Control Protocol) - "El Traductor"**
				
					Una vez que el enlace es seguro, NCP permite que PPP transporte diferentes protocolos de la **Capa 3 (Red)**. Hay un NCP para cada uno:
					
					- Si envías tráfico **IPv4**, se usa **IPCP**.
					- Si envías tráfico **IPv6**, se usa **IPv6CP**.
					- Esto permite que por el mismo cable viajen datos de distintos tipos simultáneamente.
					
				
			1. Ejemplo 
				
				Imagina que tienes un Router en **Bogotá** y otro en **Medellín** conectados por un cable serial.
				
				1. **Sin PPP (HDLC):** Conectas el cable, pones las IPs y listo. Si alguien corta el cable y pone su propio router en medio, podría robar datos fácilmente porque no hay validación.
				2. **Con PPP y CHAP:**
					- **Paso 1:** El Router de Bogotá le dice al de Medellín: _"Oye, quiero conectar, ¿quién eres?"_.
					- **Paso 2:** Medellín responde con un código secreto (Challenge).
					- **Paso 3:** Bogotá procesa ese código con su contraseña y se lo devuelve.
					- **Paso 4:** Si la contraseña coincide, el **LCP** dice "¡Adelante!" y el **NCP** empieza a pasar tus paquetes de internet (IP).

		**HDLC** (el protocolo por defecto de Cisco) es como un túnel de cristal: ves lo que pasa pero no tiene seguridad. **PPP** es un túnel blindado con guardias en la entrada.
		
	3. **Frame Relay y ATM:** Son tecnologías de conmutación de paquetes/celdas que mencionas como ejemplos de cómo se transportan datos sobre el nivel físico.
		1. **Frame Relay: El estándar eficiente**

			Surgió para conectar oficinas (LAN a LAN) a través de una red WAN de forma económica. 
			
			- **La Idea Principal:** A diferencia de una línea dedicada que siempre pagas aunque no la uses, Frame Relay es una red de "acceso compartido". El proveedor te garantiza una velocidad mínima (**CIR - Committed Information Rate**), pero si la red está libre, te permite tener "ráfagas" de mayor velocidad.
			- **Identificador Clave (DLCI):** En Ethernet usamos MAC, en Frame Relay usamos el **DLCI (Data Link Connection Identifier)**. Es un número que le dice al router por cuál "pasillo virtual" debe enviar el paquete dentro de la nube del proveedor.
			- **En Packet Tracer:** Se representa con la **Nube (Cloud)**. Entras a la configuración de la nube, vas a la pestaña "Frame Relay" y mapeas los DLCI (ej: del puerto Serial 0 al Serial 1). 

		2. **ATM (Asynchronous Transfer Mode): El perfeccionista**
		
			ATM fue diseñado para ser la "superautopista" que transportara voz, video y datos al mismo tiempo con total precisión. 
			
			- **La Idea Principal:** Mientras Frame Relay usa paquetes de tamaño variable (tramas), ATM usa **celdas de tamaño fijo** de exactamente 53 bytes.
			- **¿Por qué celdas fijas?:** Imagina un tren donde todos los vagones miden lo mismo. Es mucho más fácil y rápido de procesar para los equipos, lo que reduce el retraso (jitter), ideal para llamadas de voz o video en tiempo real.
			- **Identificadores (VPI/VCI):** Usa un sistema de etiquetas de dos niveles (**Virtual Path** y **Virtual Channel**) para guiar las celdas por la red. 

Comparativa: Frame Relay vs. ATM

|Característica|Frame Relay|ATM|
|---|---|---|
|**Unidad de datos**|Tramas (Variable)|Celdas (Fijo: 53 bytes)|
|**Velocidad**|Moderada (hasta 45 Mbps)|Muy alta (Gbps)|
|**Costo**|Más económico|Más costoso|
|**Uso ideal**|Datos de oficina (LAN)|Voz, video y backbones|



1. **HDLC (High-Level Data Link Control):** Un protocolo clásico para enlaces punto a punto en routers Cisco.

2. Conceptos "Maestros" de Capa 2 (Tu documento resalta)
	
	Para que hables como un ingeniero, graba estos dos términos que el PDF menciona como funciones de esta capa (p. 10):
	
	- **Direccionamiento Físico (MAC):** La tarjeta de red (NIC) utiliza la dirección MAC para que el switch sepa exactamente a qué dispositivo entregar la trama.
	- **Subcapas LLC y MAC:**
	    - **LLC (Control de Enlace Lógico):** Identifica el protocolo de capa superior (como IP) que va dentro de la trama.
	    - **MAC (Control de Acceso al Medio):** Gestiona el acceso al cable para evitar que dos dispositivos hablen al mismo tiempo.

---

> **Investigación Extra de Profesor:** En la **Conmutación de Paquetes** que menciona tu tema 3, la Capa 2 es la que añade los **delimitadores** (patrones de bits que marcan el inicio y fin de una trama) para que el receptor sepa dónde termina un mensaje y empieza el siguiente (p. 17).

# 5_Túneles GRE (Generic Routing Encapsulation)
. La Idea Central: "Encapsulamiento"

GRE es un protocolo de **Capa 3 (Red)**. Su trabajo es envolver un paquete (el pasajero) dentro de otro paquete (el transportador).

- **El problema:** Tienes dos oficinas con IPs privadas (ej. `192.168.1.0`). Internet no sabe qué hacer con esas IPs porque son privadas.
- **La solución GRE:** El router de la Sede A toma el paquete privado, le pone una "mochila" (encabezado GRE) y luego lo mete en un paquete con IPs públicas que sí pueden viajar por Internet. Al llegar a la Sede B, el router quita la mochila y entrega el paquete original.

---

2. Anatomía de un Paquete GRE (Capa por Capa)

Si usas el **Modo Simulación** en Packet Tracer y abres un PDU que viaja por un túnel GRE, verás algo sorprendente: **¡Dos capas 3!**

1. **Capa 3 Interna (Pasajero):** Contiene la IP privada de tu PC (ej. `192.168.1.5` enviando a `192.168.2.10`).
2. **Encabezado GRE:** Es una pequeña etiqueta que dice "Lo que hay dentro es un paquete IP".
3. **Capa 3 Externa (Transporte):** Contiene las IPs públicas de los Routers (las que te da el proveedor de internet). **Esta es la que ve Internet.**

---

3. Características clave que debes saber:

- **Es "Genérico":** Puede transportar casi cualquier cosa (IPv4, IPv6, tráfico de ruteo como OSPF o EIGRP). Por eso es el favorito para conectar sedes.
- **Sin Cifrado (¡Cuidado!):** GRE por sí solo **no es seguro**. Es como un sobre de cristal; cualquiera en Internet puede ver lo que hay dentro. Por eso, en la vida real, casi siempre se usa **GRE sobre IPsec** (IPsec pone el candado y GRE pone el túnel).
- **Crea una Interfaz Virtual:** En el router, el túnel aparece como una interfaz física más (`interface Tunnel0`). ¡Puedes hasta ponerle una IP a ese túnel!

# 6_NAT (Network Address Translation)

El **NAT (Network Address Translation)** ==es el "traductor de identidad" de tu red==. Su función principal es tomar las **direcciones IP privadas** de tus dispositivos internos (que no pueden viajar por Internet) y cambiarlas por una **dirección IP pública** que sí puede navegar.

Imagina que tu empresa es un edificio de oficinas:

- **IPs Privadas:** Son las extensiones telefónicas internas (Ej. Ext 101, 102). Puedes llamar de una oficina a otra, pero nadie fuera del edificio puede marcar "101" directamente.
- **NAT (El Router):** Es la recepcionista. Cuando quieres llamar afuera, ella usa la única línea telefónica pública del edificio para hacer la conexión por ti.
- **IP Pública:** Es el número de teléfono oficial del edificio que el mundo exterior sí conoce

Tipos de NAT (Cómo "vive" en Packet Tracer)

En el simulador, configurarás NAT en el **Router de Borde** (el que conecta tu LAN con el ISP). Estos son los tres sabores principales:

1. **NAT Estático (Uno a Uno):**
    - **La Idea:** Asignas una IP pública fija a un servidor interno específico (como tu servidor Web).
    - **Uso:** Para que gente desde Internet pueda entrar a ver tu página web.
    - **En Packet Tracer:** Usas el comando `ip nat inside source static [IP_Privada] [IP_Publica]`.
2. **NAT Dinámico (Muchos a Muchos):**
    - **La Idea:** Tienes un grupo (pool) de IPs públicas. Cuando una PC quiere salir, el router le "presta" una que esté libre.
    - **Uso:** Para empresas que tienen varias IPs públicas y quieren repartirlas según la demanda.
3. **PAT / NAT con Sobrecarga (Muchos a Uno):**
    - **La Idea:** ¡El más común! Miles de dispositivos salen a Internet usando **una sola IP pública**.
    - **El Truco:** El router diferencia a cada PC usando **puertos** (ej: PC1 usa el puerto 5001, PC2 el 5002).
    - **Uso:** Lo que tienes en tu casa ahora mismo. 

---

# 7_Tecnologías WAN avanzadas

Cuando hablamos de **Tecnologías WAN Avanzadas**, nos referimos a cómo las empresas modernas han dejado de depender de simples cables "tirados" de un punto A a un punto B para pasar a redes inteligentes, virtuales y altamente automatizadas.

En el contexto de tu documento y del modelo OSI, esto se sitúa principalmente en las **Capas 2 y 3**, pero con una fuerte inyección de **software**. Aquí tienes el desglose a fondo:

1. MPLS (Multi-Protocol Label Switching) - "El sistema de etiquetas"

Es la tecnología reina de las WAN corporativas desde hace años.

- **La Idea:** En lugar de que cada router en el camino tenga que leer toda la dirección IP de destino (Capa 3) y buscar en una tabla gigante, MPLS le pone una **"etiqueta"** al paquete al entrar a la red del proveedor (Capa 2.5).
- **Por qué es avanzado:** Los routers internos del proveedor solo leen la etiqueta (que es mucho más rápido) y "disparan" el paquete al siguiente punto.
- **En Packet Tracer:** Aunque el simulador es limitado para configurar el "núcleo" de MPLS, puedes ver su efecto: permite crear **VPNs de Capa 3**, donde diferentes empresas usan la misma red del proveedor pero están totalmente aisladas entre sí.

2. SD-WAN (Software-Defined WAN) - "La inteligencia"

Esta es la evolución actual. Antes, si tenías un enlace de fibra (caro) y uno de internet barato, el router no sabía muy bien cómo aprovecharlos.

- **La Idea:** Un cerebro central (controlador) decide por dónde enviar el tráfico basándose en la aplicación.
- **Ejemplo Masivo:**
    - Si detecta que la **Llamada de Zoom** (tráfico crítico) tiene retraso en el enlace A, la mueve instantáneamente al enlace B sin que se corte.
    - El **tráfico de Facebook** de los empleados lo manda por el internet más barato.
- **Modelo OSI:** Aquí la Capa 7 (Aplicación) le dice a la Capa 3 (Red) qué camino tomar.

3. Banda Ancha por Fibra y Satélite (GPON y Starlink)

Las WAN avanzadas ya no solo usan cables seriales viejos.

- **GPON (Gigabit Passive Optical Network):** Es lo que lleva fibra a la oficina. Usa una sola fibra para muchos usuarios mediante división de tiempo y luz.
- **Satélite de baja órbita (LEO):** Como Starlink. En el modelo OSI, la **Capa 1 (Física)** cambia de cables a ondas de radio espaciales, pero manteniendo latencias bajas que permiten usar VPNs y VoIP sin problemas.

4. Resumen de Diferencias Clave

| Tecnología         | Enfoque Principal      | Ventaja de "Experto"                                                    |
| ------------------ | ---------------------- | ----------------------------------------------------------------------- |
| **MPLS**           | Velocidad y Privacidad | El proveedor garantiza el camino (SLA).                                 |
| **SD-WAN**         | Agilidad y Costo       | Puedes usar internet común como si fuera una red privada cara.          |
| **Metro Ethernet** | Simplicidad            | Conectas tus sedes como si estuvieran en el mismo switch de la oficina. |

# 8_QoS
#### El "Tráfico VIP" de la red

La Calidad de Servicio (QoS) es un conjunto de mecanismos técnicos que permiten gestionar, priorizar y garantizar el rendimiento del tráfico de red en función de sus requerimientos específicos. Su objetivo principal es asegurar la entrega adecuada de servicios críticos (como voz, video y datos sensibles a la latencia), incluso en condiciones de congestión.

QoS clasifica, marca, filtra y programa el tráfico, controlando así el uso del ancho de banda y reduciendo problemas graves como el retardo, la variación del retardo (jitter) y la pérdida de paquetes

QoS no crea más ancho de banda (no hace el "tubo" más grande), sino que **administra el que ya tienes**. Su objetivo es dar un trato preferencial a ciertos tipos de datos.

**Ejemplo masivo:** Imagina una autopista con una ambulancia (Voz sobre IP) y un camión de basura (descarga de archivos). Sin QoS, la ambulancia puede quedar atrapada detrás del camión. Con QoS, creas un **carril preferencial** para la ambulancia.

1. **Los enemigos de la red**
	- **Ancho de banda (Bandwidth):** Es la capacidad de transmisión del canal.
    
	- **Retardo (Latency):** Es el tiempo que tarda un paquete en llegar al destino.
	    
	- **Jitter:** Es la variación en el retardo de paquetes sucesivos. _(Ejemplo: Si un paquete de tu llamada de voz tarda 10ms en llegar y el siguiente tarda 80ms, esa inestabilidad robótica en el audio es el Jitter)._
	    
	- **Pérdida de paquetes:** Es el porcentaje de paquetes que no llegan correctamente.

#### **Modelos de QoS (¿Cómo aplicamos las reglas?):**

- **Best-Effort:** Es el modelo por defecto donde no hay QoS; todos los paquetes tienen el mismo tratamiento e intentan llegar "haciendo su mejor esfuerzo".
    
- **IntServ (Integrated Services):** Requiere una reserva explícita de recursos utilizando el protocolo RSVP. Proporciona garantías firmes de calidad, pero tiene un defecto: es poco escalable.
    
- **DiffServ (Differentiated Services):** Es el rey actual. Clasifica y marca los paquetes con etiquetas DSCP. Permite dar un tratamiento preferencial sin tener que reservar recursos por flujo de datos. Al ser muy eficiente, es escalable y usado ampliamente en redes empresariales y proveedoras.

**Clasificación y Marcado (Poniendo las etiquetas):** Para que los routers sepan qué paquete es más importante, hay que etiquetarlos.

- **En Capa 2 (Switches):** Se usan los campos **CoS (Class of Service)** en las tramas Ethernet mediante el estándar 802.1p.
    
- **En Capa 3 (Routers):** Se usa el campo **DSCP** directamente en el encabezado IP para la priorización.
    
- **Técnicas de apoyo:** Para identificar el tráfico se usan ACLs (Listas de Control de Acceso), NBAR (análisis profundo de aplicaciones) y MQC (Modular QoS CLI).

### **Administración de Congestión (Gestión de las colas):** 
Cuando el router se llena, debe decidir a quién atiende primero.

- **FIFO (First In, First Out):** Sin prioridad, los paquetes se atienden por estricto orden de llegada.
    
- **WFQ (Weighted Fair Queuing):** Asigna un "peso" a las colas por tipo de tráfico.
    
- **CBWFQ (Class-Based Weighted Fair Queuing):** Es más avanzado, asigna un ancho de banda fijo a diferentes clases de tráfico.
    
- **Policing y Shaping:** Son dos técnicas vitales que limitan o suavizan el tráfico excedente para evitar la congestión.