---
layout: single
title: Buffer overflow
excerpt: "En este artículo vamos a ver el ataque Buffer overflow, ya que es un concepto muy interesante, muy útil y en el que se basan exploits famosos como el ethernalblue."
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
  - Ethernalblue
---

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow-attacks.png)

Esta es una vulnerabilidad que afecta a contraseñas como a campos de texto, ya sea en aplicaciones o páginas web, como en aplicaciones y programas. También algunos exploits famosos como es el ethernalblue se basan en esta vulnerabilidad.


# EN QUE SE BASA ESTA VULNERABILIDAD:
## Buffer:

Antes de entrar en una forma más técnica es por ejemplo como un contenedor de agua que se puede llenar o vaciar para que luego esa agua valla por ejemplo a un grifo (el contenedor es el buffer y el grifo es la información que se está procesando en ese momento).

Es lo que `se descarga en la RAM con anterioridad`, para luego ser procesado. Por ejemplo, al ver un video es la parte que se ha descargado y aunque se vaya la conexión podemos seguir viéndolo, por ejemplo en youtube eso se muestra con una barrita blanca.

Claro, si `le cuesta más descargarse que lo que le cuesta procesar` la información (en el ejemplo de youtube que veas el video más rápido de lo que le da tiempo a descargarse) hay `lag` y, por lo tanto, hay parones, pero claro, puede pasar lo contrario, que `el tiempo de descarga sea más rápido que el tiempo de procesado` por lo que eso `puede llenar más RAM` de la que se le ha dado por lo que eso causa o que `se congele la pantalla` o `se corrompa la información` o directamente que se `crashee` y a eso se le llama `buffer overflow`, ósea, que pete el bufer porque se llena.


## Buffer overflow:

Basicamente consiste en `escribir o descargar mas informacion en el buffer de lo que es capaz de retener`, esto se hace `con el fin de` que + `crashee`, o `corrompa` la informacion. Es como si unos ñiños a un deposito donde solo entran 10 litro intentan meter 5 mas, claro, el liquido se saldria del buffer que en este caso es el deposito, entonces 	haria buffer overflow.


### Ejemplo:
   Joe tiene una web donde los usuarios tienen que meter su nombre para acceder, `la web tiene 8 bits de bufer` de los nombres de usuario, porque no espera que nadie meta un nombre mayor a 8 letras. Pero Jane `mete un nombre de 10 letras`, entonces `las primeras 8 entrarian en el buffer` pero `las siguientes 2 harian overflow`. Para su sorpresa la web se conjelara y no dara como valido ningun otro input de nombre de usuario, causando una Denegacion de Servicio (DoS).
		
   Este ejemplo es un buffer overflow o buffer overrun tipico. Claro, si con esto `introducimos una funcion en el overflow`, nuestra funcion `se copiara en los campos de codigo de alrededor`, por lo tanto podriamos llegar a conseguir `Arbitrari Code Execution` (ejecucion arbitraria de comandos).

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow.png)

## Vulnerabilidad y Ataques:
