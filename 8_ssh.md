```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```
# 1_SSH como funciona 
**tu PC no se quedaría incomunicada con el resto del mundo si le borras el SSH.** Tu PC podría seguir mandando mensajes, jugando en línea o navegando en internet, pero **lo que ya NO podría hacer jamás es entrar a la oficina de administración del router para cambiar su configuración desde lejos.**

Tu analogía del guardián es **perfecta**, pero con un pequeño ajuste para que te cuadre al 100%. Te explico exactamente cómo funciona esa conversación entre la PC y el router:

---

🚪 SSH es el guardia de la "Puerta de Administración", no de todo el internet

Imagínate que el router es un **banco gigante**:

- El banco tiene una **puerta principal** para los clientes normales (este es el tráfico común de internet, como ver videos o mandar mensajes). Para entrar por ahí no necesitas SSH, usas otros protocolos (como HTTP para páginas web).
- Pero el banco también tiene una **puerta trasera blindada** que dice: _"Solo personal autorizado - Oficina del Administrador"_
    
    . **Esa puerta trasera es SSH.**

Si tú le borras el programa de SSH a tu PC, lo único que pasa es que tu PC pierde la capacidad de tocar la puerta trasera del router. Pero tu PC puede seguir yendo a la puerta principal del banco a hacer sus trámites normales (navegar por internet).

---

🗣️ ¿Cómo es la conversación real entre el "guardia" de la PC y el Router?

Tal cual como dijiste, cuando intentas conectarte por la puerta trasera, pasa exactamente esta negociación:

1. **Tu PC toca la puerta del router y le dice:** _"Hola, soy el usuario `admin`, vengo a conectarme de forma remota por la puerta de SSH"_.
2. **El Router (El Guardia) responde:** _"A ver... déjame ver tus papeles. Yo solo hablo **SSH Versión 2**. ¿Tú qué versión hablas?"_
3. **Tu PC responde:** _"¡Perfecto! Yo también tengo instalado **SSH Versión 2** y traigo mi llave RSA de 2048 bits"_.
4. **El Router dice:** _"Excelente, combinamos perfectamente. Ahora sí, escribe tu contraseña `1234` a través de este túnel secreto y pasa adelante"_.

❌ ¿Qué pasa si las versiones NO coinciden?

Si tu PC fuera una computadora ultra vieja que solo tiene **SSH Versión 1**, cuando intente conectar con tu router blindado, el router le va a decir: _"Lo siento, amigo, yo no hablo el idioma viejo e inseguro de la Versión 1. Conexión rechazada"_. Y la PC se queda afuera.

---

⚠️ LA ADVERTENCIA DE "EL ANCESTRO INSEGURO" (Por qué inventamos SSH)

> **Antes de SSH, los ingenieros usaban un protocolo llamado TELNET para entrar al router. Telnet no tenía ningún guardia ni encriptación. Si usabas Telnet, tu contraseña `1234` viajaba por el cable en texto plano. Cualquiera con un programa gratis podía leer tu clave en el aire.**

Por eso tu profe ignora por completo a Telnet y se toma todo este tiempo enseñándote a configurar [SSHv2](https://www.emtec.com/ssh/ssh-v2.html). Porque en el mundo real, dejar Telnet encendido en un router es como dejar la puerta trasera del banco abierta y con un cartel que dice "Bienvenidos ladrones".