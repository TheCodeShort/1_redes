
# 1_Formatos y prefijos de direcciones IPv6: 

- Representadas en hexadecimal y separadas por dos puntos (:), con ocho grupos de cuatro dígitos. 
- Ejemplo: 2001:0db8:85a3:0000:0000:8a2e:0370:7334 
	- **Longitud total:** 128 bits.
		- Cada dígito hexadecimal representa **4 bits** (un _nibble_).
		- Por lo tanto, cada grupo de 4 dígitos hexadecimales suma **16 bits** (4x4=16)
		- 16bits x 8 grupos = 128 bits
		
	- **Cada hexteto:** 16 bits (4 dígitos hexadecimales X 4 bits por dígito).
	- **Representación:** Al ser hexadecimal, usamos números del **0 al 9** y letras de la **A a la F**.
	- **Ejemplo: `2001`**
		1. El **2** en binario es: `0010`
		2. El **0** en binario es: `0000`
		3. El **0** en binario es: `0000`
		4. El **1** en binario es: `0001`
		**Resultado Real:** El router no ve "2001", ve: `0010000000000001` (**16 bits**)

|Binario (4 bits)|Decimal|**Hexadecimal**|
|---|---|---|
|0000|0|**0**|
|0001|1|**1**|
|0010|2|**2**|
|0011|3|**3**|
|0100|4|**4**|
|0101|5|**5**|
|0110|6|**6**|
|0111|7|**7**|
|1000|8|**8**|
|1001|9|**9**|
|1010|10|**A**|
|1011|11|**B**|
|1100|12|**C**|
|1101|13|**D**|
|1110|14|**E**|
|1111|15|**F**|

	
- reglas del estándar **RFC 5952**:
	- **Omitir ceros a la izquierda:** En cualquier hexteto, los ceros iniciales desaparecen. `0db8` se convierte en `db8`.
		- **Dirección Original:**  `2001 : 0DB8 : 0123 : 0005 : 0000 : 0000 : 0000 : 0001`
			1. **Grupo 2 (`0DB8`):** Quitamos el cero inicial => `DB8`.
			2. **Grupo 3 (`0123`):** Quitamos el cero inicial => `123`.
			3. **Grupo 4 (`0005`):** Quitamos los dos ceros iniciales  =>`5`.
			4. **Grupo 5, 6 y 7 (`0000`):** Cada grupo se reduce a un solo `0`.
			5. **Grupo 8 (`0001`):** Se reduce a `1`.
			6. **Resultado Intermedio:**  `2001 : DB8 : 123 : 5 : 0 : 0 : 0 : 1`
			7. **Resultado Final (Usando la compresión `::`):**  Como tenemos tres grupos de ceros seguidos, los reemplazamos por el "doble dos puntos":  `2001 : DB8 : 123 : 5 :: 1`
		
		- **La regla del doble dos puntos (::):** Se puede sustituir una serie continua de hextetos con ceros por `::` y nunca debe haber dos `::, ::`. seguidos 
	    - _Ojo de experto:_ Solo se puede usar **una vez** en toda la dirección para evitar ambigüedad.
		    -  **Transformación del ejemplo:**  `2001:db8:85a3::8a2e:370:7334` (Mucho más limpia y profesional).

---

🧬 ¿Cómo se forma un "Hexteto" en IPv6?

- **Tipos de prefijos**
	- Normalmente, una dirección IPv6 se divide en dos partes iguales de 64 bits:
		1. **Prefijo de Red (64 bits):** Indica la red global y la subred (identifica "dónde" estás).
		2. **Interface ID (64 bits):** Identifica al dispositivo específico (el "quién" eres)****
	
	- Los prefijos más comunes que verás en la vida real son:
		- **Unicast:** dirección única hacia un nodo,  **`2000::/3`**: Direcciones Unicast Globales (las que navegan por Internet).
			- Es el tipo más común y el que **más se utiliza**. Es una comunicación directa entre dos dispositivos únicos.

				- **Para qué sirve:** Para navegar por una web, enviar un correo o hacer una videollamada.
				- **El objetivo:** Identificar a una interfaz específica de forma única en el mundo (Global Unicast) o en un enlace local (Link-Local).
				- **Dato de experto:** Si una IP empieza por `2000::`, es **Unicast Global** (navega por Internet). Si empieza por `fe80::`, es **Link-Local** (solo para hablar con los que están en tu mismo switch).
				
		- **Multicast:** envío a múltiples nodos, **`fe80::/10`**: Link-Local (direcciones automáticas para hablar con vecinos en el mismo cable).
			- Aquí es donde IPv6 brilla y donde **muere el Broadcast**. En lugar de molestar a todos los computadores de la red, el paquete solo va a los que "se suscribieron" a un grupo.
			- **Para qué sirve:** Para actualizaciones de routers, envío de streaming de video o para que los equipos encuentren su puerta de enlace.
			- **El objetivo:** Ahorrar recursos. Los computadores que no pertenecen al grupo ignoran el paquete a nivel de hardware, sin gastar procesador.
			- **Cómo reconocerlo:** Siempre empiezan por el prefijo **`ff00::/8`**.


		- **Anycast:** enviada al nodo más cercano entre un grupo, **`ff00::/8`**: Direcciones de Multicast.
			- Este es el concepto más "loco" y nuevo. Varias máquinas (servidores) en diferentes partes del mundo tienen **la misma dirección IP**.
			- **Para qué sirve:** Para servicios globales como el DNS o redes de contenido (CDN) como Netflix o Google.
			- **El objetivo:** Rapidez. Cuando tú pides algo a una dirección Anycast, los routers te envían al servidor que esté **físicamente más cerca de ti** (con menos saltos).
			- **Cómo reconocerlo:** No tiene un prefijo especial; usa el mismo rango que las Unicast. La diferencia está en cómo el administrador configura los routers.
		
-  Cuadro Comparativo de Uso

| Tipo          | ¿Quién recibe?                    | Uso Frecuente                       | ¿Qué tanto se usa?                                |
| ------------- | --------------------------------- | ----------------------------------- | ------------------------------------------------- |
| **Unicast**   | El destino específico.            | Navegación, SSH, Email.             | **90% del tráfico.** Es la base de todo.          |
| **Multicast** | Solo los miembros del grupo.      | Protocolos de enrutamiento, DHCPv6. | Muy frecuente para **control interno** de la red. |
| **Anycast**   | El miembro del grupo más cercano. | DNS (como el 8.8.8.8), Cloudflare.  | Solo para **servicios de alta disponibilidad.**   |


# 2_Métodos de asignación de direcciones
- **Manual (estática):** el administrador define la dirección completa. 
	- Es el método tradicional donde tú, como administrador, digitas cada bit.
	- **Cómo funciona:** Entras a la configuración de red del PC o del Router y escribes la dirección completa, el prefijo (/64) y el Gateway.
	- **Cuándo se usa:** **Solo en servidores y routers.** No quieres que la IP de tu servidor de archivos cambie nunca, porque todos los usuarios se perderían.
	- **Riesgo:** Es propenso a errores humanos (dedazos) y es agotador si tienes 500 computadores.

- **EUI-64:** se genera automáticamente a partir de la dirección MAC del dispositivo. 
	- Este es el concepto más innovador de IPv6. Permite que un host se asigne su propia IP sin necesidad de un servidor DHCP. Se basa en el proceso llamado **SLAAC** (Stateless Address Autoconfiguration).

	- **El proceso técnico tiene dos partes:**
		
		1. **El Prefijo (64 bits):** El computador le pregunta al router: _"¿En qué red estoy?"_. El router responde: _"Estás en la `2001:db8:acad:1::/64`"_.
		2. **El Interface ID (64 bits):** Aquí el PC usa su **Dirección MAC** (que es única en el mundo y tiene 48 bits) para completar la IP. Pero como faltan 16 bits para llegar a 64, hace un truco:
		    - Toma la MAC (ej: `00:AA:BB:CC:DD:EE`).
		    - La parte a la mitad y le mete el valor hexadecimal **`FFFE`**.
		    - Invierte el 7mo bit (el bit de "Universal/Local").
		    - **Resultado:** Una IP única basada en su hardware.
		- **Ventaja:** Conectas el cable y ¡listo!, ya tienes Internet. Cero configuración.
	
- **DHCPv6 ((Dynamic Host Configuration Protocol v6):** asigna direcciones dinámicas desde un servidor, con posibilidad de delegación de prefijos.
	- Es el sucesor del DHCP de IPv4, pero en IPv6 tiene dos modalidades:
	- **DHCPv6 "Stateless" (Sin estado):** El PC usa EUI-64 para su IP, pero le pregunta al DHCP cosas extra que el router no sabe, como: _"¿Cuál es la dirección del servidor DNS?"_ o _"¿Cuál es el nombre del dominio?"_.
	- **DHCPv6 "Stateful" (Con estado):** Funciona igual que en IPv4. El servidor DHCP tiene el control total: él decide qué IP le da a cada quién y guarda una lista (estado) de quién tiene qué dirección.
	    - **Cuándo se usa:** En empresas que necesitan un control estricto de auditoría (saber exactamente qué empleado tuvo qué IP a las 10:00 AM).

# 3_Transición de IPv4 a IPv6: 

- **Dual Stack:** los dispositivos usan simultáneamente IPv4 e IPv6. 
	- **Cómo funciona:** El dispositivo (PC, Router, Servidor) tiene configuradas **ambas direcciones** al mismo tiempo: una IPv4 y una IPv6.
	- **La lógica:** El equipo es "bilingüe". Si quiere hablar con una web que solo entiende IPv4, usa su stack IPv4. Si la web entiende IPv6, prefiere usar IPv6.
	- **Ventaja:** Es la más limpia y no hay pérdida de velocidad.
	- **Desventaja:** Sigues necesitando direcciones IPv4 públicas, las cuales son caras y escasas.
	
- **Túneles (Tunneling):** encapsulan paquetes IPv6 dentro de paquetes IPv4 para su transporte. 
	- Ej.: 6to4, ISATAP, GRE. 
	- Se usa cuando tienes dos "islas" de IPv6 que quieren hablar entre sí, pero en medio hay un "océano" que solo entiende IPv4.
	- **Cómo funciona:** El router de origen toma el paquete IPv6 y lo mete dentro de un sobre (encapsulado) de IPv4. Para los routers del medio, es un paquete IPv4 normal. Al llegar al otro extremo, el router de destino abre el sobre y saca el paquete IPv6 original.
	- **Los tipos que menciona el documento:**
	    - **6to4:** Automático, conecta sitios IPv6 a través de IPv4.
	    - **ISATAP:** Conecta hosts individuales dentro de una red IPv4 empresarial.
	    - **GRE:** Un túnel genérico muy usado en routers Cisco para unir oficinas.
	
	- **Traducción (NAT64/DNS64):** convierte paquetes entre protocolos para permitir interoperabilidad.
		- Es el último recurso. Se usa cuando un equipo que **solo tiene IPv6** necesita hablar con un servidor antiguo que **solo tiene IPv4**.
		- **NAT64:** Funciona como un traductor simultáneo. Cambia las cabeceras del paquete de un protocolo a otro en tiempo real.
		- **DNS64:** Es el cómplice. Cuando el PC pregunta por una web, el DNS64 "sintetiza" (inventa) una dirección IPv6 falsa que apunta al traductor NAT64 para que la comunicación sea posible.
		- **Objetivo:** Permitir que las redes nuevas (Solo-IPv6) sigan accediendo al contenido viejo de Internet.
	 
# 5_subneting

Imagina que tu ISP te entrega este prefijo (tu "terreno" para construir):  
**`2001:A1B2:C3D4::/48`**

1. El Plan de Subneteo (Diseño)

	Tienes 3 departamentos y necesitas que cada uno tenga su propia red para que los computadores se comuniquen. Como somos expertos, usaremos el **cuarto hexteto** para crear las subredes.

| Departamento   | ID de Subred (en Hex) | Prefijo de Red Completo (/64) |
| -------------- | --------------------- | ----------------------------- |
| **Ventas**     | `0001`                | `2001:A1B2:C3D4:0001::/64`    |
| **Ingeniería** | `0002`                | `2001:A1B2:C3D4:0002::/64`    |
| **Invitados**  | `000A`                | `2001:A1B2:C3D4:000A::/64`    |
2. El Estándar Sagrado: `/64`

	En IPv6, para cualquier red donde haya computadores, celulares, impresoras o servidores (lo que llamamos "redes de acceso"), la máscara **SIEMPRE debe ser `/64`**.
	
	- **¿Por qué?** Porque funciones vitales de IPv6, como el **SLAAC** (que los dispositivos se pongan su propia IP solos) o el **Neighbor Discovery** (cómo los equipos se encuentran entre sí), están diseñadas matemáticamente para trabajar sobre 64 bits.
	- Si tú pones una máscara `/70` o `/80`, la red podría "funcionar" para navegar, pero romperías la autoconfiguración y podrías tener errores extraños en el sistema.

3. Asignación a los Computadores (Hosts)

	- Ahora, ¿qué IP le ponemos a los equipos? En IPv6, el "número de casa" (Host ID) tiene 64 bits. Para no complicarnos, usaremos números bajos.
	
		- En la Subred de Ventas (`:0001::/64`):
		
			- **Router (Puerta de enlace):** `2001:A1B2:C3D4:0001::1`
			- **PC 1:** `2001:A1B2:C3D4:0001::10`
			- **PC 2:** `2001:A1B2:C3D4:0001::11`
		
		- En la Subred de Ingeniería (`:0002::/64`):
			
			- **Router:** `2001:A1B2:C3D4:0002::1`
			- **Servidor:** `2001:A1B2:C3D4:0002::500`
			- **PC Jefe:** `2001:A1B2:C3D4:0002::AAA` (¡Podemos usar letras!)
			
4. ¿Qué significan entonces los números 48, 56 o 64?

	- No es que tú "elijas" cualquier número, sino que estos representan **jerarquías de propiedad**:
	
		- **`/32`**: Es el bloque gigante que recibe un **ISP**. (Alcanza para millones de empresas).
		- **`/48`**: Es el bloque estándar que el ISP te entrega a **TI** (la empresa). Te dan los primeros 3 hextetos fijos y tú tienes el cuarto hexteto libre para jugar.
		- **`/64`**: Es el tamaño de una **Sola Subred**. Es el "cable" o la "VLAN" donde viven los computadores.
		-  El Mapa de los 128 Bits (Jerarquía de Control)

Imagina que la dirección se divide así: `[ISP]:[TÚ (EMPRESA)]:[HOST]`

|Bloque|Bits|Nombre Técnico|¿Quién lo manda?|Analogía|
|---|---|---|---|---|
|**Primeros 3 Hextetos**|1 al 48|**Prefijo de Enrutamiento Global**|El **ISP**|El código de país y ciudad.|
|**Cuarto Hexteto**|49 al 64|**ID de Subred**|**TÚ (Administrador)**|El nombre de la calle dentro de tu terreno.|
|**Últimos 4 Hextetos**|65 al 128|**ID de Interfaz**|El **Dispositivo**|El número de apartamento o casa.|


---

3. ¿Por qué es más fácil que en IPv4?

Mira la diferencia en el proceso mental:

1. **En IPv4:** Para pasar de la subred `.0` a la siguiente, tenías que sumar quizás 32 o 64 (ej. `192.168.1.32`, `192.168.1.64`). Si te equivocabas en un bit, la red no funcionaba.
2. **En IPv6:** Solo cambias un número en el cuarto grupo. **Ventas es el 1, Ingeniería es el 2, Invitados es el A.** Es visual. No hay que calcular máscaras como `255.255.255.224`. Todas son `/64`.

3. ¿Cómo lo digitas en el computador?

Si fueras a configurar manualmente el **PC 1 de Ventas**, en la configuración de red pondrías:

- **Dirección IPv6:** `2001:A1B2:C3D4:1::10` (Recuerda que quitamos los ceros a la izquierda de `:0001:`)
- **Longitud de prefijo:** `64`
- **Puerta de enlace:** `2001:A1B2:C3D4:1::1`

---

🎓 El secreto del "SLAAC" (Autoconfiguración)

El documento menciona que IPv6 es eficiente. Si no quieres ir PC por PC poniendo IPs, activas el **SLAAC**.  
El Router de Ventas grita a la red: _"¡Atención! Todos los que estén en este cable, el prefijo es `2001:A1B2:C3D4:1::/64`"_.  
Entonces, los computadores escuchan eso, toman su propia **Dirección MAC** (su identidad física) y ellos mismos inventan su IP. Por ejemplo, el PC 1 diría: _"Ok, mi IP será `2001:A1B2:C3D4:1: [mi_mac_aqui]`"_.