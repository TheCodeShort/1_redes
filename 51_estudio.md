
# 1_pendientes por aprender 

specíficamente, todo el contenido de tus preguntas está alineado con los programas de certificación oficiales más reconocidos de la industria, principalmente **Cisco CCNA (Cisco Certified Network Associate)** y los fundamentos de **CompTIA Network+ / Security+**.

Los bloques de conocimientos previos clave que debiste tener en cuenta se agrupan en cuatro grandes pilares:

## 1_Modelos de Referencia e Infraestructura Física (Fundamentos)

- **El Modelo OSI:** Conocer a la perfección qué ocurre en cada una de las 7 capas, especialmente la diferencia entre las capas bajas: **Capa 1 (Física)**, **Capa 2 (Enlace de Datos)**, **Capa 3 (Red)** y **Capa 4 (Transporte)**.
- **Unidades de Datos (PDUs):** Memorizar los nombres de los datos según la capa donde se procesan (Bits \(\rightarrow \) Tramas \(\rightarrow \) **Paquetes** \(\rightarrow \) Segmentos).
- **Medios de Transmisión:** Clasificación de medios guiados (cobre, par trenzado, fibra) y no guiados (Wi-Fi, satelital).

## 2_Operación de Conmutación y Enrutamiento (Routing & Switching)

- **Dispositivos de Capa 2 vs. Capa 3:** Entender cómo opera un Switch tradicional mediante la tabla CAM (direcciones MAC) frente a cómo un **Switch de Capa 3 (Multicapa)** puede realizar enrutamiento Inter-VLAN por hardware de forma veloz.
- **Determinación de Rutas:** Comprender cómo los routers o switches capa 3 eligen el camino óptimo hacia redes remotas.
- **Protocolos de Enrutamiento Dinámico:** Conceptos de enrutamiento y sus familias principales, como los de **Estado de Enlace (OSPF)** frente a los de Vector Distancia (RIP).
- **Topologías y Cobertura:** Clasificación de redes según su alcance geográfico, diferenciando una red local (LAN) de una red de área amplia **(WAN)** para conectar sedes dispersas.

## 3_Seguridad y Mitigación de Riesgos en Infraestructura

- **Seguridad en el Plano de Gestión:** Priorizar siempre el uso de protocolos cifrados como **SSH** sobre alternativas inseguras en texto plano como Telnet para administrar equipos.
- **Seguridad de Capa 2 (Switching Security):** Implementación de funciones críticas como:
    - **DHCP Snooping:** Para validar servidores DHCP legítimos y evitar ataques de suplantación.
    - **BPDU Guard:** Para proteger la topología de Spanning Tree Protocol (STP) y evitar bucles accidentales o maliciosos en la red.

## 4_Gestión, Monitoreo y Arquitectura Empresarial (Enfoque Estratégico)

- **Protocolos de Descubrimiento de Vecinos:** Uso de herramientas para mapear la red de forma automática, distinguiendo entre estándares propietarios como **CDP (Cisco)** y estándares abiertos multi-fabricante como **LLDP**.
- **Gestión Segura (SNMP):** Conocer la evolución del protocolo simple de administración de red, sabiendo que **SNMPv3** es el estándar mandatorio en corporativos por incluir capacidades nativas de autenticación y cifrado.
- **Diseño Jerárquico de Red:** Entender los modelos de diseño de tres capas (Acceso, Distribución y Núcleo) para estructurar redes empresariales escalables y estables.

# 2_🗺️ Plan de Estudio Detallado: Infraestructura y Redes de Datos

## 1_Modelos de Referencia y Arquitectura de Red

- **Lo que debes dominar:** Cómo viajan los datos divididos en capas desde el software hasta el cable físico.
- **Conceptos clave:**
    - **Modelo OSI (7 capas) vs Modelo TCP/IP (4 capas):** Comprender que OSI es teórico e internacional (creado por ISO), pero TCP/IP es el que realmente implementan los sistemas operativos.
    - **Unidades de Datos (PDUs):** Saber nombrar la información según la capa en la que esté: _Segmentos_ (Capa 4 - Transporte), _Paquetes_ (Capa 3 - Red), e _Ittamas/Frames_ (Capa 2 - Enlace).
- **Términos técnicos:** _OSI Reference Model_, _TCP/IP Stack_, _Encapsulación de datos_, _Protocol Data Units (PDU)_.

## 2_Medios de Transmisión y Conectividad Física (Capa 1 y 2)

- **Lo que debes dominar:** La infraestructura física que soporta los datos en una oficina o centro de datos.
- **Conceptos clave:**
    - **Medios Guiados vs No Guiados:** Cables físicos (Par Trenzado/UTP, Fibra Óptica, Coaxial) frente a ondas electromagnéticas en el aire (Wi-Fi, radioenlaces).
    - **El Switch como rey de la LAN:** Comprender que el Switch conecta equipos localmente dentro de una misma red usando tablas de direcciones físicas (MAC).
- **Términos técnicos:** _Unshielded Twisted Pair (UTP)_, _Ethernet Standards_, _MAC Address Table_, _LAN Topology_.

## 3_Direccionamiento lógico y Subnetting (Capa 3)

- **Lo que debes dominar:** El diseño, cálculo matemático y distribución de direcciones en una empresa.
- **Conceptos clave:**
    - **IPs Públicas vs Privadas:** Identificar rangos privados como `192.168.X.X` o `10.X.X.X` que se usan a nivel interno y no son rastreables en internet.
    - **Subnetting y Máscaras:** Dominar la notación binaria (operación lógica **AND** entre la IP y la Máscara para hallar la dirección de red) y la equivalencia CIDR (saber que `/24` equivale a `255.255.255.0`).
- **Términos técnicos:** _IPv4 Addressing_, _Subnet Mask_, _CIDR Notation_, _Private IP Ranges (RFC 1918)_, _Binary ANDing_.

## 4_Enrutamiento e Interconexión Global (Routing)

- **Lo que debes dominar:** Cómo se comunican redes distintas y cómo viajan los paquetes a grandes distancias (WAN).
- **Conceptos clave:**
    - **El Router como puente:** El dispositivo encargado de conectar la red local (LAN) con el mundo exterior (WAN).
    - **Rutas Estáticas vs Dinámicas:** Configurar caminos manuales eficientes frente a dejar que protocolos inteligentes como **OSPF** descubran el mapa de la red mediante el "Estado de Enlace".
    - **Estrategias de Disponibilidad:** Crear rutas flotantes (respaldos) basadas en la Distancia Administrativa.
- **Términos técnicos:** _Static Routing_, _Dynamic Routing Protocols_, _Link-State (OSPF)_, _Administrative Distance (AD)_, _Floating Static Routes_.

## 5_Administración, Segmentación Avanzada y Seguridad de Redes

- **Lo que debes dominar:** Herramientas y protocolos para operar de forma segura los equipos de una empresa.
- **Conceptos clave:**
    - **VLANs (Redes Locales Virtuales):** Comando `vlan` en Cisco para segmentar lógicamente un switch físico por áreas (ej: separar la red de Contabilidad de la de Invitados).
    - **Monitoreo Seguro con SNMPv3:** Administrar y vigilar equipos usando cifrado y autenticación para evitar que intercepten las contraseñas.
        
    - **Ahorro y Seguridad con NAT/PAT:** Traducir direcciones de red para ocultar las IPs internas de la empresa y navegar con una sola IP pública.
- **Términos técnicos:** _VLAN Segmentation_, _Cisco IOS Commands_, _SNMPv3 Criptografía_, _NAT/PAT (Network Address Translation)_.
