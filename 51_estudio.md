specíficamente, todo el contenido de tus preguntas está alineado con los programas de certificación oficiales más reconocidos de la industria, principalmente **Cisco CCNA (Cisco Certified Network Associate)** y los fundamentos de **CompTIA Network+ / Security+**.

Los bloques de conocimientos previos clave que debiste tener en cuenta se agrupan en cuatro grandes pilares:

1. Modelos de Referencia e Infraestructura Física (Fundamentos)

- **El Modelo OSI:** Conocer a la perfección qué ocurre en cada una de las 7 capas, especialmente la diferencia entre las capas bajas: **Capa 1 (Física)**, **Capa 2 (Enlace de Datos)**, **Capa 3 (Red)** y **Capa 4 (Transporte)**.
- **Unidades de Datos (PDUs):** Memorizar los nombres de los datos según la capa donde se procesan (Bits \(\rightarrow \) Tramas \(\rightarrow \) **Paquetes** \(\rightarrow \) Segmentos).
- **Medios de Transmisión:** Clasificación de medios guiados (cobre, par trenzado, fibra) y no guiados (Wi-Fi, satelital).

2. Operación de Conmutación y Enrutamiento (Routing & Switching)

- **Dispositivos de Capa 2 vs. Capa 3:** Entender cómo opera un Switch tradicional mediante la tabla CAM (direcciones MAC) frente a cómo un **Switch de Capa 3 (Multicapa)** puede realizar enrutamiento Inter-VLAN por hardware de forma veloz.
- **Determinación de Rutas:** Comprender cómo los routers o switches capa 3 eligen el camino óptimo hacia redes remotas.
- **Protocolos de Enrutamiento Dinámico:** Conceptos de enrutamiento y sus familias principales, como los de **Estado de Enlace (OSPF)** frente a los de Vector Distancia (RIP).
- **Topologías y Cobertura:** Clasificación de redes según su alcance geográfico, diferenciando una red local (LAN) de una red de área amplia **(WAN)** para conectar sedes dispersas.

3. Seguridad y Mitigación de Riesgos en Infraestructura

- **Seguridad en el Plano de Gestión:** Priorizar siempre el uso de protocolos cifrados como **SSH** sobre alternativas inseguras en texto plano como Telnet para administrar equipos.
- **Seguridad de Capa 2 (Switching Security):** Implementación de funciones críticas como:
    - **DHCP Snooping:** Para validar servidores DHCP legítimos y evitar ataques de suplantación.
    - **BPDU Guard:** Para proteger la topología de Spanning Tree Protocol (STP) y evitar bucles accidentales o maliciosos en la red.

4. Gestión, Monitoreo y Arquitectura Empresarial (Enfoque Estratégico)

- **Protocolos de Descubrimiento de Vecinos:** Uso de herramientas para mapear la red de forma automática, distinguiendo entre estándares propietarios como **CDP (Cisco)** y estándares abiertos multi-fabricante como **LLDP**.
- **Gestión Segura (SNMP):** Conocer la evolución del protocolo simple de administración de red, sabiendo que **SNMPv3** es el estándar mandatorio en corporativos por incluir capacidades nativas de autenticación y cifrado.
- **Diseño Jerárquico de Red:** Entender los modelos de diseño de tres capas (Acceso, Distribución y Núcleo) para estructurar redes empresariales escalables y estables.