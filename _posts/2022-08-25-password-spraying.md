---
layout: single
title: Password Spraying
excerpt: "En este artículo vamos a ver el ataque Password Spraying, que nos es muy útil cuando tenemos un numero limitado de intentos al loguearnos en algun lugar."
date: 2022-08-25
classes: wide
header:
  teaser: /assets/images/web-hacking/password-spraying/password-spraying.jpg
  teaser_home_page: true
  icon: 
categories:
  - Web-Hacking
  - SSH
tags:  
  - Password Spraying
  - SSH
---

![](/assets/images/web-hacking/password-spraying/password-spraying-2.jpg)

Este ataque lo hacemos cuando tenemos un `número de intentos limitados` para loguearnos en algún lugar, es decir, cuando nos bloquean los intentos o nos hacen esperar después de 3 o 5 intentos (puede ser un número distinto), por lo tanto, `no` podemos hacer `fuerza bruta` de las contraseñas `(hay que tener en cuenta que al igual que la fuerza bruta, solo es recomendable recurrir a esta técnica cuando no nos queda nada más por intentar, ya que al igual que la fuerza bruta hacemos mucho ruido, por lo que somos bastante detectables)`. Este ataque se puede resumir en cinco palabras: `pocas contraseñas para muchos usuarios`.

# EN QUE CONSISTE ESTA TÉCNICA:

Como ya he mencionado `esta técnica la utilizamos` cuando `no` podemos hacer `fuerza bruta` porque tenemos un `número limitado de intentos` de ingresar contraseñas en un login. Hay que recalcar que `cuantos más usuarios` haya registrados, nuestra `posibilidad` de acierto es `mayor`.

A diferencia de cuando utilizamos fuerza bruta, probamos un `número muy pequeño` de posibles `contraseñas`, normalmente utilizamos las más habituales, pero dependiendo cuál es el objetivo podemos utilizar otras. Y `probamos esas contraseñas` en `todos los usuarios` a ver si tenemos suerte y una funciona.

Es `verdad que `a` veces` se suele utilizar ``una única contraseña`` para `minimizar el tiempo` que tardemos en encontrar un usuario y contraseña válido, `o` también para intentar `minimizar un poco el ruido` que hagamos, pero `si tenemos la posibilidad` de utilizar más de una contraseña `es recomendable utilizarlas` para aumentar nuestras posibilidades de acierto.

Y en caso de no conocer los usuarios, sí que los podemos probar a fuerza bruta con un listado de posibles usuarios.

`Entonces resumiendo: esta técnica consiste en tener un listado de usuarios (o posibles usuarios) y otro listado muy pequeño de contraseñas y probar esas pocas contraseñas en todos los usuarios.`

Aquí dejo una foto en la que se explica bastante bien y de forma visual en que consiste esta técnica.

![](/assets/images/web-hacking/password-spraying/example.webp)

# COMO EXPLOTAR ESTA TÉCNICA:

Por lo que he estado viendo, hay varias herramientas para este tipo de ataque; `burpsuit`, `herramientas que hay en github`... O incluso podríamos crar un `script` que lo haga, no sería muy complicado.

Pero yo aquí solo voy a enseñar a hacer el ataque con `burpsuit`, para `webs`, ya que lo puedo probar con una máquina de HTB sin complicarme mucho.

`(en esta explicación doy por hecho que ya sabes utilizar burpsuit, si no sabes primero aprende aunque sea como capturar peticiones (es muy simple) y luego ya vuelve aquí)`

Para esto primero tendremos que capturar la petición del loguin y en burpsuit la mandaremos al `intruder` con `Ctl + I`:



# COMO PREVENIR ESTA TÉCNICA:

Las formas de prevención de este ataque son bastante lógicas y son las mismas que para todos estos tipos de ataques a contraseñas:

```
·Tener una contraseña robusta (que tenga números, letras minúsculas y mayúsculas, caracteres raros...)

·Cambiar la contraseña de vez en cuando.

·No utilizar la misma contraseña para todo.

·Utilizar doble factor de autenticación.

```
