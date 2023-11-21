---
layout: single
title: Buffer overflow
excerpt: "En este artículo vamos a ver el ataque Buffer overflow, ya que es un concepto muy interesante, muy útil y en el que se basan exploits famosos como el eternalblue."
date: 2022-08-19
classes: wide
header:
  teaser: /assets/images/web-hacking/Buffer-overflow/buffer-overflow-attacks.png
  teaser_home_page: true
  icon: 
categories:
  - Web-Hacking
  - APP-Hacking
tags:  
  - Buffer overflow
  - Eternalblue
---

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow-attacks.png)

# ||||||| CUANDO ESTUDIE EN PROFUNDIDAD ESTE TEMA EXPLICARE COMO EXPLOTAR ESTA VULNERABILIDAD DE UNA FORMA MAS TÉCNICA |||||||

Esta es una vulnerabilidad que afecta a contraseñas como a campos de texto, ya sea en aplicaciones o páginas web, como en aplicaciones y programas. También algunos exploits famosos como es el eternalblue se basan en esta vulnerabilidad.


# EN QUE SE BASA ESTA VULNERABILIDAD:
## Buffer:

Antes de entrar en una forma más técnica es por ejemplo como un contenedor de agua que se puede llenar o vaciar para que luego esa agua valla por ejemplo a un grifo (el contenedor es el buffer y el grifo es la información que se está procesando en ese momento).

La memoria en el buffer **se guarda en la RAM con anterioridad**, para luego ser procesado. Por ejemplo, al ver un video es la parte que se ha descargado y aunque se vaya la conexión podemos seguir viéndolo, por ejemplo en youtube eso se muestra con una barrita blanca.

Claro, si le cuesta más descargarse que lo que le cuesta procesar la información (en el ejemplo de youtube que veas el video más rápido de lo que le da tiempo a descargarse) hay **lag** y, por lo tanto, hay parones, pero claro, puede pasar lo contrario, que **el tiempo de descarga sea más rápido que el tiempo de procesado** por lo que eso **puede llenar más RAM** que se le ha dado por lo que eso causa o que **se congele la pantalla** o **se corrompa la información** o directamente que se **crashee** y a eso se le llama **buffer overflow**, ósea, que pete el bufer porque se llena.


## Buffer overflow:

Básicamente consiste en **escribir más información en el buffer de lo que este es capaz de almacenar**, esto se hace con el fin de que **crashee**, o **corrompa** la información. Es como si unos niños a un depósito donde solo entran 10 litros intentan meter 5 más (osea 15l), claro, el líquido se saldría del buffer que en este caso es el depósito, entonces haría buffer overflow.


### Ejemplo:
Joe tiene una web donde los usuarios tienen que meter su nombre para acceder, **la web tiene 8 bits de bufer** de los nombres de usuario, porque no espera que nadie meta un nombre mayor a 8 letras. Pero Jane **mete un nombre de 10 letras**, entonces **las primeras 8 entrarían en el buffer** pero **las siguientes 2 harían overflow**. Para su sorpresa, la web se congelará y no dará como válido ningún otro input de nombre de usuario, causando una Denegación de Servicio (DoS).
		
Este ejemplo es un **buffer overflow** o **buffer overrun** típico. Claro, si con esto **introducimos una función en el overflow**, nuestra función **se copiará en una parte de la memoría del programa**, por lo tanto, podríamos llegar a conseguir **Arbitrari Code Execution** (ejecución arbitraria de comandos).

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow.png)


## VULNERABILIDAD y ATAQUES:

Los **lenguajes de programación más propensos** a sufrir esta vulnerabilidad son `C` y `C++`, pero **lenguajes más modernos** como `Java`, `Python` o `C#` tienen medidas para dificultar la aparición de estas vulnerabilidades, pero pueden seguir habiéndolas.

Esta vulnerabilidad se suele utilizar para **tomar el control** de alguna aplicación o sistema. Para eso se intenta hacer un buffer overflow para **sobrescribir la memoria** de la aplicación o la web para cambiar el path del programa, por lo que expone cierta información que lleva datos privados.

Claro, si conocemos el diseño de la memoria podemos mandar ciertas órdenes para inyectar comandos o código malicioso para obtener acceso.


### Explotación

Hay muchas estrategias para explotar el buffer overflow, pero las dos **más comunes** son:

-**Stack overflow attack**: esto es cuando intentamos meter en un buffer de un programa más datos de los que tiene asignados, esto casi siempre resulta en la corrupción de los datos adyacentes al bufer. Este es el más común de este tipo de ataques.

-**Heap overflow attack**: Esto pasa cuando el buffer donde es guardada la información tiene mucha más memoria asignada de la necesaria. Para explotar esto se corrompen los datos almacenados para que la aplicación o la web sobreescriban las estructuras internas. Este tipo de ataques se dirigen a los datos del grupo de memoria abierto conocido como montón (heap).


# EXPLICACIÓN DE CÓDIGO

El buffer overflow suele ocurrir porque los programadores no detectan e impiden que se **introduzca más información de la permitida**. Para solucionarlo, los programadores deberían prestarle más atención sobre todo a las líneas del código relacionadas con el buffer.

Ejemplos de **codigo vulnerable**:

```c
variable $username [8]
print “Enter Username:”
getstring ($username)
```

El anterior código es vulnerable, ya que **le asigna 8 bytes** a la variable `$username`, pero no pone nada que **limite** que se puedan escribir más bytes. Es decir, que el programa está hecho para meter nombres como Paco, o como Manolito, pero si se mete un nombre como AntonioXXXXXXX el programa crasheará. Por desgracia o afortunadamente, depende como se mire, los lenguajes como `C` o `C++` no están preparados para este tipo de errores, entonces si no se programa bien la aplicación la información sobrante al buffer se escribirá en la memoria.

Para buscar esta vulnerabilidad en el código: es importante entender como funciona el código, luego tienes que prestar especial atención a donde estén programados los **inputs externos** y ver si es vulnerable o si tiene **funciones vulnerables**: `gets()`, `strcpy()`, `strcat()`, `printf()`. Estas funciones, si no se usan cuidadosamente, pueden abrir las puertas a ataques de este tipo.


### Formas para buscar estas vulnerablidades:

·**Estática**: buscar entre miles de líneas de código, pero puede ser una tarea draconiana y que pasen desapercibidas un montón de vulnerabilidades, por lo que hay programas que automatizan la búsqueda de este tipo de vulnerabilidades en el código: `Checkmarx`, `Coverity`.

·**Dinámica**: esto  consiste en ejecutar el programa y probar de forma manual o automatizada para ver si hay esta vulnerabilidad. Programas que lo automatizan: `Appknox`, `Veracode Dynamic Analysis`, `Netsparker`.


# MITIGAR LA VULNERABILIDAD

Lo primero es la **codificación segura**, funciones de manejo seguro del buffer, **revisión del código**, **detección** de buffer overflow de forma **dinámica** y **detección de exploits** a través del **sistema operativo**.

También tenemos que **testear el código**, pero simplemente con análisis estáticos o dinámicos no es suficiente, **hay que hacer ambos**, ya que sino puede dar cabida a falsos positivos o negativos. 

Pero la **forma más fácil de evitar** este problema es simplemente **no usar** lenguajes vulnerables a este tipo de ataques, por ejemplo `C` o `C++`, ya que **permiten acceso a la memoria y al buffer**, y, en cambio, **usar** otros más seguros como `Python`, `Java`, `C#`, `.NET`...

Claro, eso no se puede hacer siempre, porque puede suponer mucho trabajo rehacer todo el código..., entonces hay que **tener en cuenta** estos puntos:

```
· Comprobar los límites del buffer y que estén preparados para que no se pueda meter más datos de los permitidos.

· Proteger el espacio ejecutable de la aplicación, web...

· Usar sistemas operativos modernos y actualizados.
```
