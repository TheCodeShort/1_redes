```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```
# Hot Standby Router Protocol

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

🎚️ ¿Se pueden usar números bajos como 20, 15, 10 o 5?

**Sí, técnicamente funcionaría la misma lógica, PERO hay una trampa oculta.**

La regla de oro de HSRP es: **El número más alto gana**. Si pones al izquierdo en 20 y al derecho en 10, el de 20 gana y se vuelve el router principal (Activo). La lógica matemática se mantiene intacta.

⚠️ **LA ADVERTENCIA:** El gran peligro de usar números menores a 100 es que si agregas un tercer router a la red y olvidas configurarle la prioridad, ese router nuevo nacerá con la prioridad por defecto de **100**. Como 100 es mayor que tus prioridades de 20 y 10, ¡ese router nuevo le robará el trono a tus routers principales de inmediato! Por eso, en el mundo real, los ingenieros siempre usamos números **mayores a 100** (como 105, 110, 150) para asegurarnos de que nadie por defecto les gane. El rango real permitido en Cisco IOS va desde **0 hasta 255**.

---

## 🥊 Si el principal tiene 150, ¿puedo usar 149 para el otro?

**Sí, funciona perfectamente.** Para el router, un empate no es opción. 150 es matemáticamente mayor que 149, por lo tanto, el de 150 será el Rey (Activo) y el de 149 será el Guardia (Standby).

🧠 **La Lógica de Fondo:** Aunque funciona, en la ingeniería de redes preferimos dejar una "brecha" o espacio entre los números (por ejemplo, usar 150 y 100, o 120 y 110). Hacemos esto por comodidad visual para que cualquier ingeniero que revise la red note al instante quién es el principal y quién el respaldo, y también por si en el futuro queremos meter un router intermedio (que podría tener prioridad 125, por ejemplo).

---

## 🛡️ ¿Significa que los demás son backups? ¿Se pueden tener MUCHOS backups?

El mayor es el **Activo (el que trabaja)** y **TODOS los demás routers del grupo quedan como backups**.

Pero aquí hay un detalle técnico fascinante sobre cómo funciona HSRP (versión 1 y versión 2):

- En un grupo de HSRP, solo puede haber **un router Activo** (procesando datos) y **un solo router en estado Standby (el backup inmediato)**.
- Si decides conectar **3, 4 o 5 routers** al mismo grupo (por ejemplo, con prioridades 150, 140, 130, 120), el de 150 será el _Activo_ y el de 140 será el _Standby_.
- ¿Qué pasa con los routers de 130 y 120? Se quedan en un estado llamado **Listen (Escuchando)**. Son como soldados en la reserva militar de la base: están mudos, escuchando la red, esperando a que el Activo o el Standby mueran para subir de rango.

Si el de 150 explota, el de 140 toma el control de inmediato. Y automáticamente, el de 130 (que estaba escuchando) asciende al puesto de _Standby_. ¡Es una cadena de mando perfecta!

---

⚙️ ¿Cuándo se vuelve a ejecutar el comando?

Volverás a ejecutar estos comandos cada vez que agregues un router físico nuevo a la red que deba respaldar a esa misma VLAN, o cuando crees una **nueva VLAN**.

Por ejemplo, si tienes la **VLAN 20**, tendrás que entrar a las subinterfaces de la VLAN 20 en todos los routers y repetir el proceso, pero usando un grupo diferente (ej: `standby 20 ip ...`) para que no se mezcle con la VLAN 10.

## Como se vería la activación 
![[5_hsrp_vlan_10.png]]