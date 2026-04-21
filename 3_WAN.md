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
				
	1. **PPP (Point-to-Point Protocol):** Fundamental para conexiones directas entre dos nodos WAN (como el enlace de tu casa al ISP).
	2. **Frame Relay y ATM:** Son tecnologías de conmutación de paquetes/celdas que mencionas como ejemplos de cómo se transportan datos sobre el nivel físico (p. 20).
	3. **HDLC (High-Level Data Link Control):** Un protocolo clásico para enlaces punto a punto en routers Cisco.

3. Conceptos "Maestros" de Capa 2 (Tu documento resalta)
	
	Para que hables como un ingeniero, graba estos dos términos que el PDF menciona como funciones de esta capa (p. 10):
	
	- **Direccionamiento Físico (MAC):** La tarjeta de red (NIC) utiliza la dirección MAC para que el switch sepa exactamente a qué dispositivo entregar la trama.
	- **Subcapas LLC y MAC:**
	    - **LLC (Control de Enlace Lógico):** Identifica el protocolo de capa superior (como IP) que va dentro de la trama.
	    - **MAC (Control de Acceso al Medio):** Gestiona el acceso al cable para evitar que dos dispositivos hablen al mismo tiempo.

---

> **Investigación Extra de Profesor:** En la **Conmutación de Paquetes** que menciona tu tema 3, la Capa 2 es la que añade los **delimitadores** (patrones de bits que marcan el inicio y fin de una trama) para que el receptor sepa dónde termina un mensaje y empieza el siguiente (p. 17).