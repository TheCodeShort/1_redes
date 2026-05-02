![[1_tcp-ip.png|697]]
# ¿Por que se agruparon ?

El modelo TCP/IP fue creado por el Departamento de Defensa de EE. UU. Su objetivo principal no era crear un estándar académico para fabricar cables, sino crear un sistema de comunicación que sobreviviera a una guerra nuclear (literalmente, así nació ARPANET).

A los creadores de TCP/IP **no les importaba el medio físico**. Su mentalidad era: _"Mi protocolo IP (Capa de Internet) solo necesita saber a qué dirección lógica ir. Me da exactamente lo mismo si debajo usan cables de cobre, satélites, fibra óptica o palomas mensajeras. Todo lo que esté por debajo de la IP, métanlo en una 'Caja Negra' y llámenlo 'Acceso a la Red'"_.

Al agruparlas, TCP/IP logró una **independencia total del hardware**. Si mañana se inventa una tecnología "7G" o telecomunicaciones cuánticas, los protocolos TCP e IP no tienen que reescribirse; simplemente se conectan a esa nueva Capa de Acceso a la Red.

---
# 1_**Switching Básico**
Se limita simplemente a leer las direcciones MAC (la dirección física de la tarjeta de red) y enviar la información (las tramas) de un puerto a otro. [[2_redes.pdf#page=132&selection=58,31,58,48|2_redes, página 132]]

# 2_**Switching Avanzado**
Es el conjunto de técnicas que se aplican en los switches de Capa 2 (del modelo OSI) para hacer tres cosas críticas: segmentar la red, optimizar el tráfico y mejorar la seguridad. Para lograr esto, incorpora funciones tecnológicas complejas como VLANs, enlaces troncales (Trunks), EtherChannel y protocolos dinámicos como DTP.[[2_redes.pdf#page=132&selection=50,0,53,1|2_redes, página 132]]

En este punto seguimos en la capa 2, el switch no mira IPs, solo mira etiquetas y direcciones físicas pero a diferencia de el Switching básico es el funcionamiento normal en el avanzado lo realiza de forma inteligente

	
| Técnica            | Función Principal                                   | Capa OSI |
| ------------------ | --------------------------------------------------- | -------- |
| **VLAN**           | Divide un switch físico en varios lógicos.          | Capa 2   |
| **Trunk (802.1Q)** | Permite que varias VLANs viajen por un solo cable.  | Capa 2   |
| **DTP**            | Negocia automáticamente si un puerto es Trunk o no. | Capa 2   |
| **EtherChannel**   | Agrupa varios cables para sumar su velocidad.       | Capa 2   |

 
# 3_switch
toma sus decisiones basándose exclusivamente en las direcciones MAC. A medida que los equipos se conectan y "hablan", el switch va aprendiendo dinámicamente qué dirección MAC pertenece a qué cable (puerto), creando así su tabla de enrutamiento interno.

# 4_ VLANs (Virtual LANs o Redes de Área Local Virtuales)
sta es la técnica de segmentación por excelencia.

- **¿Qué hacen?** Toman un solo switch físico y lo dividen lógicamente como si fueran varios switches separados. Permiten dividir la red en diferentes "dominios de broadcast" lógicos.
    
- **El resultado:** Cada VLAN funciona de forma totalmente independiente; el tráfico de una VLAN no se cruza con el de otra a menos que un **router** lo permita.

	 1. **El Muro de la Capa 2**

		Las **VLANs viven en la Capa 2 (Enlace de Datos)**. Cuando tú separas la red en VLAN 10 y VLAN 20, estás creando dos "islas" lógicas totalmente aisladas.
		
		- Los equipos de la VLAN 10 no pueden ver las direcciones MAC de la VLAN 20.
		    
		- El switch, por seguridad y diseño, tiene prohibido pasar una trama (Capa 2) de un puerto de la VLAN 10 a un puerto de la VLAN 20.
		    
		
	 2. **El papel de la IP (Capa 3)**
		
		Aquí es donde entra tu sospecha sobre la IP. Para que la red esté bien organizada, se sigue esta regla de oro: **Cada VLAN debe tener su propia Subred IP.**
		
		- **VLAN 10 (Facturación):** Usa la red `192.168.10.0`
		    
		- **VLAN 20 (Ventas):** Usa la red `192.168.20.0`
		    
		
		En el mundo de las redes, **dos redes IP diferentes no pueden hablarse directamente**. Necesitan un "traductor" o un "guía" que sepa cómo saltar de una red a otra. Ese guía es el **Router**.
		
	 3. **¿Cómo ocurre la comunicación? (Inter-VLAN Routing)**
		
		Cuando una PC de la VLAN 10 quiere enviarle un dato a una PC de la VLAN 20, sucede lo siguiente:
		
		1. La PC nota que la IP de destino está en **otra red**.
		    
		2. En lugar de intentar buscarla en el switch, le envía el paquete a su **Default Gateway** (que es la dirección IP del Router).
		    
		3. El paquete viaja por el **Enlace Troncal**  hasta el Router.
		    
		4. El Router recibe el paquete en la Capa 3, mira la IP de destino, ve que pertenece a la VLAN 20, y lo "enruta" de regreso por el mismo cable hacia la otra VLAN.
		    
		
	 4. ¿Hay otra forma? (El Switch de Capa 3)
		
		En las empresas modernas, a veces no usan un router físico para esto. Usan un **Switch Multicapa (o Switch de Capa 3)**.
		
		- Este equipo es un "híbrido": tiene la velocidad de un switch para mover cables, pero tiene el cerebro de un router para entender las IPs y comunicar las VLANs internamente sin que el tráfico tenga que salir a un router externo.
		
		**En resumen:**
		
		- **Switch (Capa 2):** Une computadoras en la misma VLAN (Misma red IP).
		    
		- **Router (Capa 3):** Une las VLANs entre sí (Diferentes redes IP).
    
- **Asignación:** Se le puede decir al switch qué computadora pertenece a qué VLAN mediante configuración por puerto (estático), por dirección MAC, o usando protocolos dinámicos como VMPS.
 - Un switch básico viene con todos los puertos en la **VLAN 1**. Esto significa que si una PC envía un mensaje de "búsqueda" (broadcast), le llega a **todos** los dispositivos, saturando el ancho de banda.
    **La solución:** Crear VLANs para segmentar la red en redes independientes
    
- **La VLAN Nativa:** Es una VLAN especial cuyo tráfico viaja por el cable troncal _sin_ la etiqueta del 802.1Q. **Dato crítico de seguridad:** El documento advierte que la VLAN nativa debe estar configurada exactamente igual en ambos switches conectados para evitar fallos e inconsistencias

-  **VLANs: Segmentar la red (El ascensor con llave)**
	   imagina que en tu edificio (Switch) trabajan los de **Ventas** y los de **Recursos Humanos (RRHH)**.

	- **Switch Básico:** Todos están en el mismo piso. Si alguien de Ventas grita (hace un _Broadcast_), los de RRHH no pueden trabajar por el ruido. Además, cualquiera puede entrar a la oficina del otro.
	- **Switch Avanzado (VLANs):** Creas "pisos virtuales". Los de Ventas están en la **VLAN 10** y los de RRHH en la **VLAN 20**. Aunque usen el mismo switch físico, lógicamente están en edificios separados. **Mejoras seguridad** (no se ven entre sí) y **optimizas tráfico** (el ruido de uno no molesta al otro).
	-  **¿Qué divide en realidad?** Divide el **Broadcast**. Imagina que el Broadcast es un grito. En un switch normal, si uno grita, todos escuchan. Con VLANs, si alguien de la VLAN 10 grita, **solo** los de la VLAN 10 lo escuchan. Los de la 20 ni se enteran.
	- **¿Los números (10, 20)?** **No son puertos**. Son "IDs" o etiquetas. Puedes usar cualquier número del 1 al 4094. Usamos 10, 20, 30 por orden (como saltar de 10 en 10), pero podrías usar la VLAN 2 y la 3 sin problema.
	- **¿Quién es el mensajero?** El mensajero es el **Switch**. Él mira la "puerta" (puerto físico) por donde entra el paquete. Si la PC está conectada al puerto 5 y tú configuraste que el puerto 5 es de la **VLAN 10**, el switch le pega mentalmente esa etiqueta al paquete.
	
## **Segmentación de Red mediante VLANs y Enrutamiento Inter-VLAN (Router-on-a-Stick)**

![[2_vlans.png]]
# 5_Enlaces Troncales (Trunks) y VLAN Nativa
Si tienes múltiples VLANs en varios switches, necesitas una forma de conectarlos.

- **Troncales (Trunks):** Es un puerto configurado para permitir que el tráfico de _múltiples_ VLANs viaje a través de un único cable físico entre dos switches.
    
- **Estándar 802.1Q:** Es el protocolo universal que se encarga de ponerle una "etiqueta" a cada paquete de datos antes de enviarlo por el cable troncal, para que el switch receptor sepa a qué VLAN pertenece esa información.
    
- **La VLAN Nativa:** Es una VLAN especial cuyo tráfico viaja por el cable troncal _sin_ la etiqueta del 802.1Q. **Dato crítico de seguridad:** El documento advierte que la VLAN nativa debe estar configurada exactamente igual en ambos switches conectados para evitar fallos e inconsistencias.

- **Enlaces Troncales y DTP: El túnel entre edificios**
	Ahora imagina que tienes **dos edificios** (dos Switches). Tienes gente de Ventas en ambos.
	
	- **El Troncal (Trunk):** Es un cable que actúa como un túnel de alta velocidad que conecta los dos switches. Por ese único cable pasan los datos de Ventas, RRHH y Finanzas. Para no mezclarlos, el switch le pega una "etiqueta" (llamada **802.1Q**) a cada paquete que dice: _"Esto es de la VLAN 10"_.
		- - **¿Ocupa un espacio físico?** **Sí**, usa los mismos puertos y cables de siempre (RJ-45, fibra, etc.). No es un cable "mágico" por fuera, es un cable normal que tú "programas" por dentro para que se comporte como un Troncal.
		- **¿Se pierde un puerto para una PC?** Sí, ese puerto ya no sirve para una PC, ahora sirve para unir dos switches.
		- **¿Quién pone la etiqueta 802.1Q?** **El Switch, nunca la PC**. La PC envía datos normales. Cuando el dato llega al Switch A y este ve que debe ir al Switch B, el Switch A le pone un "sticker" (la etiqueta 802.1Q) que dice "VLAN 10". Cuando llega al Switch B, este lee el sticker, lo arranca y entrega el paquete limpio a la PC de destino.
		- la etiqueta que es un estándar  802.1Q  le inserta una VLAN ID  a la trama trama
		- ordenas al switch que por ese puerto van a pasar varias VLANs usando el estándar **802.1Q**. Es como decir: "Este cable ya no es para una PC, es un túnel para departamentos".
		
# 6_EtherChannel (Agregación de Enlaces)
- **Qué es?** Es una técnica que permite tomar varios cables físicos separados (ej. 3 o 4 cables) y fusionarlos mediante configuración para que el switch crea que son un solo "cable lógico" gigante.
    
- **Beneficio:** Multiplica el ancho de banda y otorga redundancia (si un cable físico se rompe, la red sigue funcionando por los demás sin interrumpirse).
    
- **Protocolos:** Existen dos formas de negociar esta unión:
    
    - **PAgP:** El protocolo propietario y exclusivo de la marca Cisco (usa modos _auto_ y _desirable_).
        
    - **LACP (IEEE 802.3ad):** El protocolo estándar internacional (usa modos _active_ y _passive_).
    
**EtherChannel: Optimizar el tráfico (La Autopista)**	
	¿Qué pasa si el cable que une tus dos switches se llena porque hay mucho tráfico de Ventas?
				
Al usar un troncal y usar  EtherChannel significa si con troncal usamos dos cables para mayor transito de datos en diferentes VLANs  sacrificamos dos puestos osea si tenemos 6 puertos  solo podriamos conectar 4 PC para que se puedan comunicar 
- 2 PCs para **Ventas** (VLAN 10).
- 2 PCs para **RRHH** (VLAN 20).

- Si solo usas 1 cable (Trunk simple) y un ratón lo muerde o alguien lo patea, las dos áreas quedan incomunicadas. Si tienes un **EtherChannel** de 2 cables y uno falla, la red sigue funcionando por el otro sin que nadie lo note.
- Si las 4 PCs están enviando archivos pesados al mismo tiempo, un solo cable se saturaría. El EtherChannel reparte la carga.
	- En los switches de verdad, suele haber unos puertos a la derecha llamados **SFP o Uplinks**. Son puertos adicionales, a veces más rápidos (de fibra óptica), diseñados específicamente para hacer estos "puentes" (Trunks o EtherChannels) y así **no quitarle espacio a las computadoras**.
- **EtherChannel:** Es para unir **Switches con Switches** (o Servidores muy potentes con Switches). Es para crear "tuberías maestras".
- **VLANs:** Son para **separar**. Si no te importa que los de Ventas vean la carpeta compartida de RRHH, no necesitas VLANs.
# 7_DTP (Dynamic Trunking Protocol)
- Es un protocolo de Cisco diseñado para que dos switches negocien de forma automática si un puerto debe funcionar como acceso (para un PC) o como troncal (para otro switch).
    
- **Regla de oro de seguridad:** El documento es enfático en recomendar **deshabilitar DTP**. Dejarlo encendido en redes no controladas representa un riesgo de seguridad grave, por lo que la buena práctica es configurar los puertos manualmente de forma explícita.

- un ataque informático real y muy famoso llamado **"VLAN Hopping" (Salto de VLAN)**. Si dejas el DTP encendido en el puerto donde se conecta una computadora normal, un atacante o empleado malintencionado puede instalar un software (como Yersinia) que le hace creer al switch que su computadora es otro switch.
	
- **DTP (Dynamic Trunking Protocol):** Es como un conserje que intenta adivinar. Si conectas un cable, DTP le pregunta al otro switch: _"Oye, ¿quieres que seamos un túnel (Trunk) o una oficina normal (Access)?"_.
	    - **Punto Crítico:** Los expertos lo desactivan (`switchport nonegotiate`) porque un hacker podría fingir que es un switch, negociar un Trunk con DTP y robarse el tráfico de todas las VLANs.
	    - **¿Es un cable diferente?** **No**. DTP es un **protocolo (software)** que corre por el cable del Troncal.
		- **¿Cuál es el peligro?** El peligro no es el modo _Access_, el peligro es el modo _**Dynamic**_. Si dejas el puerto en "negociación automática", un hacker conecta su laptop, le envía un mensaje DTP al switch diciendo: _"Hola, soy otro switch, hagamos un Troncal"_. El switch le cree, abre el Troncal y ahora el hacker puede ver el tráfico de **todas** las VLANs de tu empresa. Por eso, el experto lo pone en "Manual" y apaga el DTP.
# 8_Casos de Uso (Ejemplos del mundo
}{ñlkm bnm896+¿' p´+ 78/real)
Para aterrizar la teoría, la unidad plantea dos escenarios:

- **El caso de la Privacidad:** Un administrador crea la VLAN 10 específicamente para el departamento financiero. Al aislar esos puertos del dominio general, nadie de otros departamentos podrá interceptar ni interactuar con el tráfico financiero.
    
- **El caso de la Potencia:** En un Data Center, se necesitan velocidades enormes. Se conectan dos switches mediante 3 cables físicos diferentes y se configuran usando el protocolo estándar **LACP** (EtherChannel). El resultado es una "súper autopista" lógica de 3 Gbps que no genera bucles y soporta fallos.
    

# 9_STP (Protocolos Spanning Tree) original (802.1D)
- Spanning Tree Protocol (STP) como un protocolo de Capa 2 (estándar IEEE 802.1D) creado para solucionar un problema gravísimo: **los bucles (loops)**.

- **Los Estados de los Puertos**
	 1. **Blocking (Bloqueado):** Solo escucha BPDUs.
		 - **Qué hace:** El puerto está "congelado". No deja pasar datos de ninguna PC para evitar un bucle.
		- **Qué escucha:** Solo escucha las **BPDU** (los mensajes de otros switches que dicen "yo soy el jefe").
		- **MAC Table:** No anota nada. Está en modo "ver y oír, pero no tocar"
	
	2. **Listening (Escuchando):** Procesa BPDUs para asegurarse de que no hay bucles.
		- **Qué hace:** El switch ha decidido que quizás este puerto deba abrirse. Empieza a limpiar su tabla de MACs viejas.
		- **Qué hace el semáforo:** Parpadea. Está decidiendo si el camino es seguro. No envía datos todavía.
	
	3. **Learning (Aprendiendo):** Empieza a anotar las MACs en su tabla, pero **no deja pasar datos todavía**.
		 - **Qué hace:** ¡Ya casi! El switch ahora sí empieza a anotar en su tabla: "La PC de Juan está en el puerto 3".
		- **Tráfico:** Todavía **NO** deja pasar los datos de las PCs, solo construye su mapa mental (MAC Table).
		
	4. **Forwarding (Enviando):** ¡Luz verde! El puerto ya es seguro y envía datos

- **Para entender STP, primero hay que entender el desastre que previene:**

	- Si conectas varios switches en un círculo o triángulo para tener "cables de respaldo" por si uno se rompe, las tramas de _Broadcast_ (el "grito" del que hablamos antes) empiezan a dar vueltas infinitas a la velocidad de la luz.
	    
	- Esto genera una **tormenta de broadcast**, duplicación de datos y hace que la red colapse por completo en cuestión de segundos.
	    
	- **¿Qué hace STP?** Crea un "árbol" lógico libre de bucles. Escanea la red y **bloquea lógicamente** (como si cortara el cable por software) los enlaces redundantes. Si el cable principal se rompe, STP se da cuenta y activa el cable de respaldo automáticamente
	- **Ejemplo**: recordando que EtherChannel es => [[1_TCP-IP_y_más#6_EtherChannel (Agregación de Enlaces)]]
		1. **Puente Lógico 1 (EtherChannel 1):** Usas el Puerto 1 y 2.
		2. **Puente Lógico 2 (EtherChannel 2):** Usas el Puerto 3 y 4.
		- ¿Qué hace el STP en este caso?

			Aquí es donde el STP se pone el uniforme de guardia de tráfico:
			
			- **Detección de Bucle:** El STP mirará al Switch B y dirá: _"Un momento... tengo dos caminos lógicos (Puente 1 y Puente 2) para llegar al mismo sitio"_.
			- **El Bloqueo:** Como el objetivo de STP es que **NUNCA** haya dos caminos abiertos al mismo tiempo entre los mismos puntos (para evitar el bucle), el STP va a **BLOQUEAR completamente uno de los dos EtherChannels**.
			- **Resultado:** Tendrás el **Puente 1** funcionando al 100% (velocidad de 2 cables) y el **Puente 2** estará en modo "espera" (luces naranjas).
			
	- **¿Cómo toma decisiones STP? (Los mensajes BPDU)**
		- STP elige un "Jefe" en la red llamado **Switch Raíz (Root Bridge)**. A partir de él, calcula cuál es el camino más corto para llegar a cualquier otro switch. Los caminos más largos o redundantes los bloquea.
			- **El "DNI" del Switch: El Bridge ID (BID)**
				- Para que haya un jefe, cada switch necesita una identificación única. El **Bridge ID** se compone de dos partes:
				
					- **Prioridad:** Un número que tú, como administrador, puedes cambiar (por defecto es 32,768),  hay una regla: **La prioridad en los switches Cisco solo se puede cambiar en saltos de 4096** (ejemplo: 0, 4096, 8192... hasta 32768). No puedes poner un "1".
					
					- **Dirección MAC:** El identificador físico de la tarjeta del switch.
						- **¿Para qué sirve la MAC entonces?** Imagina que tienes 10 switches nuevos y a todos se les olvida configurar la prioridad (todos tienen la de defecto: 32768). Como hay un empate técnico, el STP necesita un **desempate matemático**.
						- **La función de la MAC:** Como la MAC es **única en el mundo** y no se puede cambiar (viene grabada de fábrica), el STP la usa como el "número de serie" para desempatar. Si todos tienen prioridad 32768, el switch con la MAC más pequeña gana.
				
				**La regla de oro de STP:** En esta elección, **el número más bajo GANA**. Si todos tienen la misma prioridad, el switch con la dirección MAC más baja será el jefe.
    
		- Para hablar entre ellos, los switches no usan IPs, usan unos mensajes especiales de Capa 2 llamados **BPDU** (Bridge Protocol Data Unit)
		
		- **¿Qué son los mensajes BPDU? (La diplomacia de la red)**
			- Imagina que sacas tres switches nuevos de su caja y los conectas entre sí. En ese momento, **ninguno sabe quién es el jefe** , el **BPDU** es un mensaje de **Multicast** especial que solo entienden los switches..
			
			1. Cada switch empieza a enviar **BPDUs** por todos sus puertos cada 2 segundos.
			2. El mensaje dice: _"Hola, soy el Switch C, mi ID es 32768:AA... y yo soy el Root Bridge (el jefe)"_.
			3. Cuando el Switch B  y A recibe el mensaje del C, compara los IDs.
			    - Si el ID de C es **menor** que el suyo, el Switch B agacha la cabeza, deja de decir que él es el jefe lo mismo el Switch A y empieza a propagar el mensaje de C: _"Atención todos, el jefe es el Switch C"_.
			4. Al final, todos los switches de la red se ponen de acuerdo en quién es el **Root Bridge**.
		
			- **El Cálculo del Camino más Corto (El Costo)**
				- Una vez que ya saben quién es el "Jefe" (Root Bridge), cada switch mira sus cables y se pregunta: _"¿Cuánto me cuesta llegar hasta el jefe?"_.
				
				-  **La Topología Física (El Triángulo)**
					
					Para que el ejemplo que te di funcione, la conexión física sería así:
					
					- Switch **A** tiene un cable conectado al Switch **C**.
					- Switch **A** también tiene un cable conectado al Switch **B**.
					- Switch **B** tiene un cable conectado al Switch **C**.
					
					Físicamente, hay un triángulo. **¡Eso es un bucle!** Si no existiera el STP, un mensaje daría vueltas infinitas entre A, B y C.
	
					STP no mide la distancia en metros, sino en **Velocidad (Ancho de Banda)**. A esto lo llama **Costo**:
					
					- Un cable de 10 Mbps tiene un costo de **100**.
					- Un cable de 100 Mbps tiene un costo de **19**.
					- Un cable de 1 Gbps tiene un costo de **4**.
					- Un cable de 10 Gbps tiene un costo de **2**.
					
					**¿Cómo decide qué bloquear?**  
					El switch suma los costos de cada camino para llegar al jefe. El camino que sume el **número más bajo** es el ganador y se mantiene **Abierto**. El camino que sume más (el más "caro" o lento) es el que el STP **Bloquea**.
				¿Cómo sabe el Switch A cuál es el mejor camino?

			- **Conclusion**
			   - Aquí es donde entra la magia del **BPDU** y por qué se envía cada 2 segundos:
			
					- **Por el camino directo (A -> C):** El Switch A recibe un BPDU que dice: _"Soy el Jefe C, y para llegar a mí desde este puerto el costo es 0"_. Como el cable es de 100Mbps, el Switch A suma: 0+19=19
					- **Por el camino indirecto (A -> B -> C):** El Switch B recibe el mensaje del Jefe C. Como su cable es de 1Gbps, le suma 4 y le reenvía el mensaje al Switch A diciendo: _"Oye A, yo llego al Jefe con un costo de 4"_.
					- **La decisión de A:** Cuando ese mensaje llega al Switch A, este le suma el costo de su propio cable hacia B (otros 4). Entonces A dice: _"Para llegar al Jefe por aquí me cuesta 4+4=8
			
				**Resultado:** El Switch A compara: **¿19 o 8?** Como 8 es menor, el Switch A **bloquea** el puerto que va directo a C y prefiere enviar todo a través de B.
			
		    	1. ¿Por qué se grita cada 2 segundos si ya lo saben?
			
			    	- Esta es la clave de la **Resiliencia** (capacidad de recuperarse):
			
						1. **Vigilancia constante:** Los switches se mantienen "escuchando" ese latido cada 2 segundos. Mientras el Switch A reciba el mensaje del Jefe, sabe que la carretera está abierta.
						2. **¿Qué pasa si alguien corta el cable entre B y C?** El Switch B dejará de recibir el mensaje del Jefe. Al siguiente segundo, ya no podrá decirle al Switch A que tiene un camino de costo 4.
						3. **La Reacción:** El Switch A deja de recibir la opción "barata" (costo 8). Entonces dice: _"¡Emergencia! El camino rápido murió. Pero recuerdo que tengo un cable directo a C que antes bloqueé"_.
						4. **Recuperación:** STP cambia el estado de ese puerto de **Blocking** a **Forwarding** (pasando por las luces naranja que mencionamos). En unos segundos, la red vuelve a funcionar por el camino de 100Mbps.
# 10_RSTP (Rapid STP - 802.1w) y PVST+: La Velocidad es Clave
Como vimos antes, el STP clásico [[1_TCP-IP_y_más#9_STP (Protocolos Spanning Tree) original (802.1D)]] tarda entre **30 y 50 segundos** en poner un puerto en verde (pasando por Listening y Learning). En una empresa moderna, ¡30 segundos sin red es una eternidad!
- **Estados de los puertos RSTP:**
	- Como el STP clásico era muy lento (30 a 50 segundos para pasar a verde), el **RSTP** simplificó el semáforo para ser más rápido:
	- ![[3_rstp.png]]
		**¿Qué significa esto?** Que en el modo Rápido (RSTP), los tres estados de "no hacer nada" se fusionaron en uno solo llamado **Discarding**. El semáforo pasa de "Rojo" a "Verde" casi instantáneamente porque elimina la espera burocrática del estado _Listening_.

- **¿Qué hace el RSTP?**: Reduce ese tiempo a **menos de 6 segundos** (o incluso milisegundos).
- **¿Cómo lo logra?**: Elimina los estados aburridos. En lugar de esperar pasivamente a ver qué pasa, los switches RSTP se "dan la mano" activamente.
    - Un switch le dice al otro: _"Oye, ¿puedo ser tu camino al jefe?"_.
    - El otro responde: _"Sí, dale, yo bloqueo mis otros puertos un momento mientras nos ponemos de acuerdo"_.
    - A este proceso se le llama **Propuesta y Acuerdo** (Proposal and Agreement). Es una negociación instantánea en lugar de una espera basada en cronómetros.
    - Nuevos Roles de Puertos (El neumático de repuesto inflado)
    
	- El RSTP introdujo dos roles que el abuelo no tenía, y aquí está el secreto de su velocidad:
		
		- **Puerto Alternativo (Alternate Port):** Es un puerto que está bloqueado, pero **ya sabe** que es el segundo mejor camino al Jefe (Root Bridge). El abuelo lo bloqueaba y se olvidaba de él. El RSTP lo tiene "en la banca", listo para entrar. Si el camino principal muere, el Alternativo pasa a ser el principal **al instante** porque ya hizo los cálculos antes.
		- **Puerto de Respaldo (Backup Port):** Es un repuesto para cuando tienes dos cables al mismo segmento (menos común pero útil).
		- . Estados de Puerto Simplificados

		- El RSTP eliminó la burocracia de los estados:
			
			1. **Discarding (Descartando):** Mezcló el antiguo "Bloqueado" y "Escuchando". Aquí el puerto no pasa datos de usuario.
			2. **Learning (Aprendiendo):** Sigue anotando MACs.
			3. **Forwarding (Enviando):** ¡Luz verde!
			
			4. Edge Ports (Puertos de Borde)
			
			RSTP permite marcar los puertos donde hay computadoras (no switches) como **Edge Ports**.
			
			- **¿Cómo funciona?:** El switch sabe que una PC no puede crear un bucle de red (porque no es un switch). Entonces, en cuanto conectas la PC, el puerto pasa a **verde instantáneamente**. El abuelo te hacía esperar 30 segundos aunque solo conectaras una impresora.
			
			Mientras que el **STP clásico** es un sistema basado en **cronómetros** (esperar a que el reloj avance), el **RSTP** es un sistema basado en **eventos y mensajes**. Se hablan, se ponen de acuerdo y actúan.
## 11_ PVST y PVST+ (Per-VLAN Spanning Tree)

Este es un concepto **exclusivo de Cisco** y es vital para entender por qué pagamos por switches caros.

- Estado de los puertos => son los mismo que el STP pero mejor implementado y organizado 

- **El Problema del STP Normal**: Solo existe **UN solo árbol** para toda la red. Si el STP decide que un cable está bloqueado, ese cable no transporta datos de **ninguna** VLAN. Estás desperdiciando un cable que pagaste.
- **La Solución de PVST**: Crea una **instancia de STP por cada VLAN**.
    - Imagina que tienes la VLAN 10 (Ventas) y la VLAN 20 (Sistemas) en un triángulo de switches.
    - Con PVST, puedes decirle al Switch A: _"Tú eres el Jefe para la VLAN 10"_.
    - Y al Switch B: _"Tú eres el Jefe para la VLAN 20"_.
- **El Beneficio (Balanceo de Carga)**: El cable que está "bloqueado" para Ventas puede estar "activo" para Sistemas. Así, **todos tus cables están trabajando** al mismo tiempo, cada uno llevando el tráfico de diferentes departamentos.

- El Problema: El "Árbol Único"

	Imagina que tienes un triángulo de switches y un EtherChannel de respaldo.
	
	- En el **STP normal o RSTP estándar**, solo se crea un árbol para toda la oficina.
	- Si el protocolo decide bloquear un cable para evitar un bucle, ese cable queda **totalmente muerto**.
	- **El desperdicio:** Si tienes la VLAN 10 (Ventas) y la VLAN 20 (Sistemas), ambas están obligadas a usar el mismo camino. El cable de respaldo está ahí, cobrando polvo, mientras el principal está saturado.

- La Solución: PVST+ (Un árbol por cada VLAN)

	Aquí es donde entra la analogía de las **Ramas y Árboles**.
	
	- **PVST+** significa "Spanning Tree Por VLAN".
	- En lugar de un solo árbol para todos, el switch crea **un árbol independiente para cada VLAN**.

- **El Objetivo (Balanceo de Carga):**

	- **Para la VLAN 10:** Configuramos el Switch A como el "Jefe" (Root Bridge). El camino hacia él está despejado, pero el cable que va al Switch B se bloquea.
	- **Para la VLAN 20:** Configuramos el Switch B como el "Jefe". Ahora, el cable que antes estaba bloqueado para la VLAN 10, **¡se abre para la VLAN 20!** y se bloquea otro.
	- Imagina que el cable entre **A y C** es de Fibra Óptica (Súper rápido) y los otros son de cobre (Lentos).

	- **Sin PVST+ (Un solo árbol):** El STP diría: "El camino A-C es el mejor". Todos los datos de todas las VLANs irían por ahí. El cable entre **B y C** se bloquearía totalmente. **Problema:** Si el cable A-C se llena (cuello de botella), el cable B-C sigue bloqueado y desperdiciado.
	- **Con PVST+ (Inteligencia por VLAN):** Aquí es donde tú, como ingeniero, "engañas" al sistema para aprovechar todo:
	    - **Para la VLAN 10:** Configuras a **A** como jefe. El "mejor camino" es directo A-C. El cable B-C se bloquea para esta VLAN.
	    - **Para la VLAN 20:** Configuras a **B** como jefe. Ahora, el "mejor camino" para esta VLAN es el cable **B-C**. El cable A-C (que es el mejor para la otra) queda como respaldo para esta.

**Resultado final:** Tienes todos tus cables físicos trabajando. Unos llevan el tráfico de Ventas y otros el de Sistemas. Si un cable se rompe, el árbol de esa VLAN se redibuja y usa el camino de la otra. **No desperdicias ancho de banda.**

- Entonces, ¿cuál elijo? (La Gran Mezcla)
	
	Como te mencioné antes, lo ideal es combinar la **velocidad** del "Joven" con la **inteligencia** del "Cisco":
	
	1. **STP/RSTP estándar:** Un solo árbol (Lento/Rápido). Eficiente pero desperdicia cables.
	2. **PVST+:** Muchos árboles (uno por VLAN), pero usa el método lento del abuelo (espera 30-50s).
	3. **Rapid PVST+ (El favorito de los expertos):** Es un modo que combina ambos. Crea **un árbol por cada VLAN** y además usa la **negociación rápida** del RSTP.
---
![[2_stp-pvst_pre-vlan.png]]

# 12_PortFast y BPDU Guard
puertos donde conectas dispositivos finales (PCs, impresoras, servidores). Sirven para que el usuario no tenga que esperar al semáforo y para que nadie cause un desastre en la red.

1. **PortFast (El carril de alta velocidad)**

	Como vimos, el STP normal tarda 30-50 segundos en ponerse en verde (pasando por _Listening_ y _Learning_). Si conectas una PC, esta tiene que esperar todo ese tiempo para obtener una IP o iniciar sesión.
	
	- **¿Qué hace?**: Le dice al switch: "Este puerto va a una PC, no a otro switch. **Sáltate los estados intermedios y ponte en verde YA**".
	- **El beneficio**: La conexión es instantánea.
	- **El riesgo**: Si por error conectas un switch en un puerto con PortFast, podrías crear un bucle (loop) antes de que el STP se dé cuenta, y la red colapsaría.
	
2. BPDU Guard (El escudo protector)

	Este es el complemento obligatorio de PortFast. Recuerda que las **BPDU** son los "mensajes de jefe" que se envían los switches entre sí.
	
	- **¿Qué hace?**: Vigila el puerto. Si el puerto recibe una **BPDU** (lo cual significa que alguien conectó un switch donde no debía), el BPDU Guard dice: "¡Alto! Aquí solo debería haber una PC".
	- **La acción**: El switch **apaga el puerto inmediatamente** (lo pone en modo `err-disable`) para proteger al resto de la red.
	- **La analogía**: Es como un sensor de movimiento en una oficina: si detecta algo que no debería estar ahí, cierra la puerta con llave.
- **PortFast:** Es para que los usuarios no esperen (pasa a verde instantáneo).
- **BPDU Guard:** Es el guardaespaldas que vigila que ese puerto de usuario **siga siendo de usuario** y no se convierta en una entrada para otros switches.
# 13_Subneting
### **FLSM (Máscara de Longitud Fija):** calculo impreciso
el **Subnetting** es el cálculo para dividir esa "calle" (red) en "barrios" (segmentos) más pequeños.

primero tenemos

| Red     | **192.168.1.0**                         |
| ------- | --------------------------------------- |
| Mascara | **255.255.255.0**                       |
| binario | **11111111.11111111.11111111.00000000** |
ahora como es una clase C nos enfocamos en el hosts osea el ultimo octeto y sus posiciones 

| Octeto              | 0     0   0   0  0  0    0    0 |
| ------------------- | ------------------------------- |
| **bits del octeto** | 128 64 32 16 8 4  2   1         |

teniendo en cuenta la tabla si nos dicen crea 4 VLANS con esa cantidad buscamos cuantos **bits** tengo que pedir prestado a los **bits del octeto** para esto buscamos un numero que sea mayor o igual a la cantidad solicitada donde **`n`** es la cantidad de **bits** que vamos a quitar del **Octeto**,  si quitamos 2 bits nos daría => $$
2^n => 2^2 = 4
$$ 
Como resultado nos dio `4` que es igual a la cantidad de VLANS solicitadas pero también puedo ser mayor  ahora teniendo en cuenta los **bits del octeto** de izquierda a derecha vamos a pedir prestado esto dos números  que seria el **128  y 64** ahora tenemos que sumar esto dos numero para obtener la **nueva Máscara de Subred** (la máscara le dice a la computadora: _"Hasta aquí llega la red y desde aquí empiezan los equipos (hosts)"_)$$ 128 + 64 = 192$$
Ahora teniendo en cuenta el resultado se modifica la mascara así => 

| Primera Mascara   | `255.255.255.0`   |
| ----------------- | ----------------- |
| **Nueva Mascara** | `255.255.255.192` |
Con esto estamos diciendo que tan grande es la calle y cuantas casas pueden estar en esta calle 
- **Las Casas:** Son los **hosts** (PCs, celulares, impresoras).  Al cambiar la máscara de `.0` a `.192`, lo que hiciste fue poner "rejas" en una calle larga para convertirla en 4 callejones más pequeños (subredes).  y para saber el salto entre redes realizamos la siguiente operación $$ 256 - 192 = 64$$
- Ese número es clave porque te dice dónde empieza cada VLAN.
	- **Nuevas redes serían:**
		- **VLAN 1:** 192.168.1.**0**
		- **VLAN 2:** 192.168.1.**64**
		- **VLAN 3:** 192.168.1.**128**
		- **VLAN 4:** 192.168.1.**192**
		
- **¿Por qué 256 y no 255?**
	
	El **256** sí es una **constante** en estos cálculos y la razón es matemática:

	- **Combinaciones totales:** Un octeto tiene 8 bits. Si haces el cálculo de
	    $$2^8 = 256$$

	- **El rango:** Los números van del **0 al 255**, pero si cuentas el "0" como el primer número, hay un **total de 256 valores** posibles.
	
	**¿Para qué sirve restar 256-192?**  
	Se resta para hallar el **Número Mágico** o "Salto de Red".
	- Al darte **64**, te está diciendo que cada "callejón" tiene espacio para **64 números de casa** (direcciones IP en total) pero toca restarle 2 al 64.
	
- **¿Por qué 62 y no 64 equipos? (Tu segunda pregunta)**

	Imagina que cada VLAN es un **barrio cerrado**. Para que el barrio funcione, necesitas dos cosas obligatorias que ocupan un número de casa, pero donde **nadie puede vivir**:
	
	1. **La Dirección de Red (El Letrero del Barrio):** Es el primer número de cada rango (el `.0`, `.64`, `.128`, etc.). Si un router quiere enviar un paquete a tu VLAN 2, no pregunta por una computadora específica, pregunta por la red `192.168.1.64`. **Es el nombre de la calle, no una casa.**
	2. **La Dirección de Broadcast (El Megáfono):** Es el último número de cada rango. Si una computadora necesita gritarle algo a _todas_ las demás de su mismo barrio (por ejemplo: _"¿Alguien sabe dónde está la impresora?"_), envía el mensaje a esta dirección.
	    - Para la VLAN 1, el megáfono es el `.63` (justo antes de que empiece la siguiente).
	
	**Resultado:**
	
	- Tienes 64 direcciones totales.
	- Menos 1 (el nombre de la calle).
	- Menos 1 (el megáfono).
	- **Total:** 62 espacios libres para conectar computadoras reales. 
	
- ![[Pasted image 20260414152739.png]]
- **Datos técnicos que ahora ya sabes:**

	- **Nueva Máscara:** 255.255.255.**192** (o /26).
	- **Salto de Red:** 64.
	- **Hosts utilizables:** 62 por cada VLAN.
	
	**Un detalle importante:**  
	Normalmente, la **primera IP utilizable** (por ejemplo, la `192.168.1.1` en la VLAN 1) se reserva para el **Gateway** (la puerta de enlace o el router). No es obligatorio, pero es la buena práctica en redes.
    - La anatomía de la máscara
			- Una dirección IPv4 tiene **32 bits** en total (4 grupos de 8 bits).  
			Cuando escribes la máscara `255.255.255.192`, en binario se ve así:
				- **Primer 255:** `1 1 1 1 1 1 1 1` (8 bits encendidos)
				- **Segundo 255:** `1 1 1 1 1 1 1 1` (8 bits encendidos)
				- **Tercer 255:** `1 1 1 1 1 1 1 1` (8 bits encendidos)
				- **El 192:** `1 1 0 0 0 0 0 0` (**2 bits** encendidos, los que "pediste prestados")
			- La suma simple
				Ahora, solo suma todos los "1" (los bits encendidos):  
				**8 + 8 + 8 + 2 = 26**
				Por eso se pone **/26**. Le dice a cualquier técnico o equipo: _"De los 32 bits totales, los primeros 26 están bloqueados para la red y los 6 restantes son para los equipos"_.

### **VLSM (Variable Length Subnet Mask):**

- Esto te permite darle a Gerencia una red pequeña (ej. una `/29` para 6 personas) y a Ventas una red grande (una `/25`).

-**Cómo hacerlo "Perfecto" (La Regla del 20-80)**
- 
	Para que no sea impreciso, un experto hace esto:
	
	1. **Censo actual:** Cuenta los puertos físicos necesarios hoy (PCs, Impresoras, Teléfonos IP).
	2. **Proyección a 2 años:** Suma un **20% o 30%** de crecimiento.
	3. **Subneteo a medida:** Si el total te da 40 puertos, no calculas para 126, calculas para una red de **62 hosts (máscara `/26`)**.
	4. **Compra modular:** Compras un switch de 48 puertos. Tienes los 40 de hoy y te sobran 8 para crecer sin comprar otro switch de inmediato.
	
 - Si solo tienes **5 PCs por piso**, usar una red de 126 puertos (como la anterior) es un desperdicio total. Para que el cálculo sea "perfecto" y profesional, usaremos **VLSM** (Máscara de Longitud Variable).

### paso a paso 

Paso 1: Analizar la necesidad real

- **Piso 1 (VLAN 1):** 5 PCs + 1 Gateway (Router) = **6 IPs necesarias**.
- **Piso 2 (VLAN 2):** 5 PCs + 1 Gateway (Router) = **6 IPs necesarias**.

- **Paso 2: Buscar la potencia de 2 que encaje**

- Necesitamos un número que cubra 6 IPs.

	1. (Muy pequeño, no caben).$$2^2 =4$$

    2. (**¡Este es!** Nos da 8 IPs totales).$$2^3=8$$

- **Paso 3: Calcular la nueva máscara**
	- Si usamos **3 bits** para los equipos (porque 2³ = 8), nos sobran **5 bits** para la máscara (8 - 3 = 5bits de red).

	- Sumamos los valores de esos 5 bits: 128 + 64 + 32 + 16 + 8 = 248

| Octeto              | 0     0   0   0  0  0    0    0 |
| ------------------- | ------------------------------- |
| **bits del octeto** | 128 64 32 16 8 4  2   1         |

- Tu máscara perfecta es **255.255.255.248** (o en formato corto: **`/29`**).

- **Paso 4: Calcular el salto (El número mágico)**
- $$256-248=8$$

	Tus redes ahora saltarán de 8 en 8. ¡Súper ajustado y eficiente!

### Cuadro de Subnetting "Perfecto" (Ahorro Máximo)

| VLAN       | ID de Red       | Gateway         | Rango de PCs (5 equipos)            | Broadcast        |
| ---------- | --------------- | --------------- | ----------------------------------- | ---------------- |
| **Piso 1** | 192.168.1.**0** | 192.168.1.**1** | 192.168.1.**2** - 192.168.1.**6**   | 192.168.1.**7**  |
| **Piso 2** | 192.168.1.**8** | 192.168.1.**9** | 192.168.1.**10** - 192.168.1.**14** | 192.168.1.**15** |

- **ID de Red:** Es la primera IP del bloque (siempre es par o cero).
- **Gateway:** Por estándar, usamos la **primera IP útil** para el router.
- **Broadcast:** Es la **última IP** del bloque (siempre es impar).

1. Cuadro Naranja (Máscara e Infraestructura)

Este cuadro **determina el "terreno" o el rango máximo** disponible para todas tus subredes.

- Al decir que la máscara es `.192`, estás definiendo que el **salto de red es 64**.
- Esto significa que tus subredes irán fijas de 64 en 64 (Ej: VLAN 10 empieza en .0, VLAN 20 en .64, VLAN 88 en .128).
- **En resumen:** Te dice dónde empieza y dónde termina el espacio de cada VLAN.

2. Cuadro Azul (Capacidad por Subred)

Este cuadro **determina cuántos dispositivos caben** dentro de cada uno de esos trozos de 64.

- Aquí es donde ves la realidad de tus equipos: tienes **62 espacios libres** por cada VLAN.
- Como en tu proyecto solo tienes unas 3 PCs por VLAN más el router y los switches, este cuadro te confirma que **te sobra espacio** (tienes 62 y solo usas unos 5 o 6).
- **En resumen:** Te dice cuántas IPs puedes asignar a tus computadoras, routers y switches antes de que se llene la red.