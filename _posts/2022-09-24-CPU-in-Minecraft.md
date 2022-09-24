---
layout: single
title: CPU in Minecraft
excerpt: "Esta es la explicación parte por parte de la segunda CPU que estoy haciendo en Minecraft."
date: 2022-09-24
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: 
categories:
  - computación
tags:  
  - computación
---

# WORKING PROGRESS

Voy a ir actualizando este post según valla avanzando con la CPU, ya que es un proyecto complicado que me llevará su tiempo.



Esta Es la segunda CPU que hago en Minecraft. La primera que hice fue bastante simple y muy cutre, pero mi intención es hacer una bien hecha, por lo tanto estoy creando esta.

`Primera CPU:`

![](/assets/images/CPU/oldcpu.png)

https://www.youtube.com/watch?v=lzufHP3hNGU

# ÍNDICE
 
```
  -ALU
  
  -RELOJ
  
  -MEMORIAS
      ·Texts
      ·RAM
      ·Files
      ·...
   
  -PROGRAMAS
      ·Calculadora
      ·Juegos
      ·Editor de texto
      ·...
  
  -GRAFICOS
      ·Pantalla
      ·Tarjeta grafica
      ·...
      
```

# ALU

Este es uno de los `componentes mas importantes` de una CPU, la `ALU` es la `Unidad Aritmetica Lógica` de la CPU, es decir, la parte que se encarga de los `calculos y las operaciones logicas`.

En la ALU que he hecho hay 3 partes importantes: `Full Adders`, `Modulo A2` y un `comparador`.

## FULL ADDERS

Con este circuito logramos hacer `sumas` en `binario`. A continuación dejo un esquema de como es el circuito con puertas lógicas:

![](/assets/images/CPU/full-adder-circuit.png)

Entonces lo que he hecho ha sido hacer lo mismo pero en el juego. Generalmente se utilizan dos formas de hacer esto, una es con `antorchas de redstone` (que son un objeto que permiten `invertir la señal`) o también se puede hacer con `comparadores` que es otro objeto con el que se pueden `comparar intensidades` o `hacer restas de intensidades`. En la primera CPU utilicé la primera form, pero en esta he optado por la segunda, ya que permite hacer el circuito mas limpio y compacto.

![](/assets/images/CPU/fulladders1.png)

## MÓDULO A2

Esta es la forma óptima de hacer `restas en binario` ya que nos permite hace restas con resultados positivos, negativos o 0.

Hay `dos formas` de hacer esto, pero voy a explicar la mas compleja pero que para este caso es `la mas simple`.

Tenemos `dos números en binario`, uno lo dejamos sin tocar, pero al otro le vamos a hacer un `complemento a1`, osea, lo vamos a invertir, es decir, que todos los `0 se conviertan en 1 y viceversa`. Si no hiciesemos nada mas y sumasemos los dos numeros se haría una resta, pero tendría `+0 y -0`, pera que eso no pase ahora le tendremos que hacer un `complemento a2` es decir, que le vamos a `sumar 1`. Y aquí ya podemos `sumar ambos números` y ya estaría.

Ahora vamos a pasar esto a Minecraft:

![](/assets/images/CPU/A2.png)

Ahí estoy utilizando 2 columnas de Full Adders (para que se vea mas claro el proceso), pero no es necesario, podemos directamente `injectar +1 en el carry in de los full adders` que es lo que haré en la ALU para compactarla. También aqui he tenido el problema de que no se porque no funciona del todo bien, pero eso lo explicaré con la ALU, porque ese problemita lo he solucionado ahí.

## COMPARADOR

Como los `números negativos en vinario son los positivos invertidos`, nos puede dar problemas al interpretar negativos como números enormes, por lo que necesitamos un output que nos diga si es `positivo o negativo`. Para eso lo que hay que hacer es `comparar cual de los números es mas grande`. Si `el primero es mayor al segundo` entonces es `positivos`, pero `sino es negativo`.

![](/assets/images/CPU/comparador1.png)

(es el output que vemos arriba del todo en la ALU)

## ALU COMPLETA

Una vez `juntados todos los módulos` y `quitada la primera columna de full adders del módulo a2`, queda así:

![](/assets/images/CPU/ALU1.png)

Vale, como he dicho antes `al hacer restas da algún problemita` cuando hago restas con resultado negativo va bien, pero con positivos no `(resultado = número que debería salir - 1)`. Por lo que decidí aprobechar el comparador para otra función a parte de la original, es decir, si `el resultado de una resta` será `positivo le hace +1`, pero `sino no` y funciona así que uwu. 

## IMPLEMENTACIONES EXTRA

Con esto me refiero a `multiplicaciones, divisiones...`. Esto lo `añadiré en el programa de calculadora`, ya que `no lo necesito para operaciones matematicas y logicas basicas de la CPU`.

# RELOJ


# MEMORIAS


# PROGRAMAS


# GRAFICOS


