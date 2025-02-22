---
layout: single
title: CPU in Minecraft
excerpt: "Esta es la explicación parte por parte de la segunda CPU que estoy haciendo en Minecraft."
date: 2022-09-24
classes: wide
header:
  teaser: /assets/images/CPU/oldcpu.png
  teaser_home_page: true
  icon: 
categories:
  - computación
tags:  
  - computación
---

# WORK IN PROGRESS

Voy a ir actualizando este post según valla avanzando con la CPU, ya que es un proyecto complicado que me llevará su tiempo.



Esta Es la segunda CPU que hago en Minecraft. La primera que hice (a principios de 4º de la ESO) fue bastante simple y muy cutre, pero mi intención es hacer una bien hecha, por lo tanto estoy creando esta (a principios de 2º de Bachillerato).

`Primera CPU:`

![](/assets/images/CPU/oldcpu.png)

https://www.youtube.com/watch?v=lzufHP3hNGU

# ÍNDICE
 
```
  -ALU
      ·Full Adders
      ·Complemento A2
      ·Comparador
      ·ALU entera
      ·Implementaciones extra
  
  -RELOJ
  
  -MEMÓRIAS
      ·ROM
      ·RAM
      ·Files
      ·...
   
  -PROGRAMAS
      ·Calculadora
      ·Juegos
      ·Editor de texto
  
  -GRÁFICOS
      ·Pantalla
      ·Tarjeta gráfica
  
  -TECLADO
      ·Teclado y Encoder
      ·Serialización de datos
      ·Decoder
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

Esto se puede utilizar como `reloj o como cronometro`. Da horas desde las 00:00.00 hasta las 23:59.59, es decir `horas, minutos y segundos`.

![](/assets/images/CPU/reloj1.png)

Claro, hay un problema, `en los últimos 4 digitos todos los numeros son siempre iguales` es decir, que van o de 0 a 5, o de 0 a 9. Pero, `en los primeros dos no`, osea, `en el primero puede ir de 0 a 2` y hay todo normal, `pero en el segundo` va cuando en el `primero digito es 0, entonces va de 0 a 9`, `cuando es 1, lo mismo`, pero `cuando es 2, entonces irá de 0 a 3`, por lo que hay que contemplar esos 3 casos, pera eso lo que he hecho es `un contador de 3 que limitará el número maximo antes de que vuelva a 0`.

![](/assets/images/CPU/reloj2.png)

# MEMÓRIAS

## ROM

**(ESTA VERSIÓN DE LAS MEMÓRIAS NO ES LA MAS ACTUALIZADA, LA ACTUAL ESTA MAS ADELANTE PERO YA QUE ESTE FUE EL PRIMER DISEÑO HE DECIDIDO DEJARLO)**

Voy a poner `un máximo de 10 slots en los que puedas añadir, borrar, editar o leer textos`. (En este caso voy a utilizar esta memória solo para textos, pero que la podría utilizar para almacenar cualquier otro tipo de datos)

Estos textos tendrán un máximo de `40 caracteres` (quizás mas). Para eso voy a utilizar un concepto de memória bastante básico, basicamente si quiero que almacene informacion activo el `dropper` y meta un item en el `hopper`, de esta forma se mantendra en el hopper hasta que lo desbloqueemos, y eso lo podemos detectar:

![](/assets/images/CPU/memoria1,2.png)

Claro, esto no lo puedo dejar así porque no serviría para nada. Lo primero que hay que hacer es `detectar en que byte del texto esta`, para eso utilizamos la parte de la derecha, que hace que al utilizar un byte `ese byte se bloquee y se desbloquee el siguiente`. Luego tenemos que `ver si ese byte esta desbloqueado` y si lo esta que `se escriba o se lea ahi la información`, para eso esta la parte de la izquierda.

![](/assets/images/CPU/memoria1.png)

En la foto hay un sitio donde pone `select`, eso es porque `cada bite del mismo byte` estarán en la misma columna, es decir, `uno encima del otro` y `se desbloqueará esa columna`. Y cada archivo estará en la misma fila. Luego `a su lado estara el siguiente archivo` de texto y asi hasta 10. 

Y también `el input del medio` es el `reset`, esque me acabo de dar cuenta de que no lo puese y me da pereza editar la foto. 

Tras unos cuantos cambios y algunos apaños para solucionar un par de problemitas que había y juntar varios módulos, queda así:

![](/assets/images/CPU/memorias2.png)

Y la `memoria de un archivo de texto` quedaría así:

![](/assets/images/CPU/memorias3.png)

![](/assets/images/CPU/memorias4.png)

Y ya la de los `10 espacios para archivos` así:

![](/assets/images/CPU/memorias5.png)

Ya lo he terminado, así que voy a explicar un poco lo que le he añadido:

Primero he puesto un sistema de `select de memoria`, es decir, para activar una unica memoria de las 10 posibles, para eso su input esta aquí abajo:

![](/assets/images/CPU/memorias7.png)

Con el input del select `bloqueo el output` de las memórias que no están seleccionadas y a la vez me llevo la señal al otro lado para `bloquear el input` de la memória, para que no modificar las que no están seleccionadas:

![](/assets/images/CPU/memorias6.png)

Entonces luego ya `conecto todos los inputs` de las memórias (ya que si no estan seleccionadas no se pueden modificar), `conecto los resets con el cable rojo` (que se me ha olvidado ponerlo pero también se bloquean) y pongo `un cable que dirá a las memórias cuando se han modificado` una vez, para poder dar espacio y que no se escriba nada en ese byte. Y a su vez también por el otro lado `conecto los outputs` de las memórias.

![](/assets/images/CPU/memorias8.png)

Y ya una vez terminada la `ROM`, quedaría así:

![](/assets/images/CPU/memorias9.png)

(ya lo siento porque me haya extendido tanto en esta parte pero esque llevo 4 días metiendole muchísimas horas y es mas complicado de lo que parece)

Y una pequeña cosa extra, `para poder mandar datos a la RAM` hay que hacer un pequeño apaño. Hay que `dividir el reset` en dos para poder moverse hacia adelante y hacia atrás en los bytes de la memoria sin que se borre la memória (la parte del select, es decir la nueva parte del reset es el cable amarillo):

Y vistos algunos fallos he hecho algún que otro apaño mas, pero no quiero escribir mas XD

### VERSIÓN ACTUALIZADA

Ya que la versión anterior es muy voluminosa a la hora de agrupar varios modulos uno tras otro, mediante prueba y error he estado intentando hacer una versión mas compacta y este es el resultado:

![](/assets/images/CPU/memorias10.png)

![](/assets/images/CPU/memorias11.png)

Y quedaría de este tamaño, en total son **640 bytes**:

![](/assets/images/CPU/memorias12.png)

![](/assets/images/CPU/memorias13.png)

## RAM

La idea es tener `una memória en la que puedas gardar cosas temporalmente`, entonces conectare `los outputs de las otras memórias al input de la ram`, y `el otput de esta, al input de las memórias` (para por ejemplo la opción de pegar texto ya copiado). También tengo que tener en cuenta la cantidad de caracteres y algunas cosas más, pero no me quiero extender.

La Ram estará posicionada en ese sitio, ya que esta cerca de las otras memórias y no estorba demasiado:

![](/assets/images/CPU/ram1.png)

Y pues ya todo conectado:

![](/assets/images/CPU/ram2.png)

# PROGRAMAS


# GRÁFICOS


# TECLADO


