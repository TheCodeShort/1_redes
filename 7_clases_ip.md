```table-of-contents
title: Conceptos Red
minDepth: 1
maxDepth: 6
listStyle: number
```
# Las Clases de IP (Por su primer octeto)

## **Clase A (Rango: 1 al 126):** 

En la **Clase A**, el rango privado es únicamente el **10.**0.0.0 hasta el 10.255.255.255 , Cualquier IP que empiece por `10.X.X.X` es privada..
### El Rango de Clase A Privada

- **Rango Exacto:** Desde `10.0.0.0` hasta `10.255.255.255`
- **¿Cuál es la lógica del segundo octeto?** En la Clase A, la regla dice que **solo el primer octeto está congelado en el número 10**. El segundo octeto puede ser **cualquier número entre 0 y 255**.
- _Ejemplos privados:_ `10.0.1.5`, `10.50.20.1`, `10.255.255.254`.


Diseñada para redes gigantescas (como multinacionales o gobiernos). Su máscara por defecto es `/8` (`255.0.0.0`).



## **Clase B (Rango: 128 al 191):** 

En la **Clase B**, el rango privado va **desde el 172.16**.0.0 **hasta el 172.31**.255.255, Aquí **sí importa el segundo octeto**. Solo es privada si empieza entre `172.16.X.X` y `172.31.X.X`..

### El Rango de Clase B Privada (El más tramposo)

- **Rango Exacto:** Desde `172.16.0.0` hasta `172.31.255.255`
- **¿Cuál es la lógica del segundo octeto?** Aquí se congelaron por ley los números **desde el 16 hasta el 31**. Si pasamos el 16 y el 31 a código binario, comparten los mismos primeros 4 bits. Por eso el rango es tan específico.
- _Ejemplos privados:_ `172.16.5.10`, `172.20.100.1`, `172.31.255.50`.
- _¿Qué pasa si cambias el segundo octeto?_ Si usas `172.15.X.X` o `172.32.X.X`, automáticamente saltas a una **IP Pública** que le pertenece a alguien en Internet.

Diseñada para redes medianas o grandes (como universidades o grandes empresas). Su máscara por defecto es `/16` (`255.255.0.0`).

## **Clase C (Rango: 192 al 223):** 

En la **Clase C**, el rango privado es el típico de casa: **192.168.**0.0 hasta el 192.168.255.255 Los dos primeros octetos están fijos. Solo es privada si empieza exactamente por `192.168.1.X`.

### El Rango de Clase C Privada (Tu duda específica)

- **Rango Exacto:** Desde `192.168.0.0` hasta `192.168.255.255`
- **¿Cuál es la lógica del segundo octeto?** Para la Clase C privada, la norma mundial congeló **obligatoriamente los dos primeros octetos en `192.168`**.

Diseñada para redes pequeñas (como hogares o comercios pequeños). Su máscara por defecto es `/24` (`255.255.255.0`).

## Nota
Las Clases D (224-239) son para tráfico Multicast y la E (240-255) para investigación. El número 127 no se usa en redes porque está reservado para pruebas internas de tu propia máquina (loopback)

# Lo que define qué octeto cambia: La Máscara de Red (`/24`, `/16`, `/8`)

Para saber qué octetos están "bloqueados" (son de la red) y cuáles puedes "cambiar" (son para los hosts), debes mirar la **máscara**:

- **Máscara `/24` (Clase C por defecto):** Bloquea los **3 primeros octetos**. Solo cambia el último.
    - _Ejemplo:_ En `192.168.1.X`, los números `192.168.1` no se tocan. Solo juegas con la `X`.
- **Máscara `/16` (Clase B por defecto):** Bloquea los **2 primeros octetos**. Puedes cambiar los dos últimos.
    - _Ejemplo:_ En `172.16.X.X`, el `172.16` no se toca. Puedes cambiar los dos últimos octetos a tu antojo.

Por eso tu profesor, en su ejercicio, usó una IP de **Clase B** (`172.16.20.0`) pero le aplicó una máscara de **Clase C** (`/24`). Al ponerle `/24`, "bloqueó" el tercer octeto en `.20` para que coincida con la VLAN 20, dejando libre solo el último octeto para los dispositivos. ¡Ahí está el truco de magia que hizo!

# Tabla IP privada 

| Clase       | Primer Octeto (Fijo) | Segundo Octeto Permitido          | Ejemplo de IP Privada Válida |
| ----------- | -------------------- | --------------------------------- | ---------------------------- |
| **Clase A** | Debe ser **10**      | Cualquiera (**0 al 255**)         | `10.20.30.40`                |
| **Clase B** | Debe ser **172**     | Solo del **16 al 31**             | `172.20.50.1`                |
| **Clase C** | Debe ser **192**     | Debe ser **168** obligatoriamente | `192.168.1.50`               |
