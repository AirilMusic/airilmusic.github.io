---
layout: single
title: Web Hacking, Prototipe Pollution Attack
excerpt: "En este artículo vamos a intentar entender en que se basa el ataque Prototype Pollution y como hacerlo, ya que es algo que no es muy conocido y es super interesante."
date: 2022-07-10
classes: wide
header:
  teaser: /assets/images/web-hacking/prototype-pollution/prototype-pollution.png
  teaser_home_page: true
  icon: 
categories:
  - Web-Hacking
tags:  
  - Prototype-Pollution
---

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution.png)

En un programa de `bug bounty` me he encontrado que una web tiene la version de `jQuery 3.2.1` y buscando que vulnerabilidades tiene esa versión me he encontrado con dos: `XSS` y `Prototype Pollution`
Claro, en ese programa la vulnerabilidad de `XSS` no estaba en el scope, entonces me he decidido por el `Prototype Pollution`, y pensando que habría algún exploit y no seria del otro mundo, me he encontrado con esta vulnerabilidad que es muy compleja y no muchos entienden, así que a la vez que yo voy aprendiendo sobre ella se me ha ocurrido escribir este artículo con la finalidad de que tanto yo como cualquier otra persona pueda `entender en que consiste` y `como explotar esta vulnerabilidad`.
Así que en este artículo veremos esta interesante y compleja vulnerabilidad.


## ¿ CUANDO PODEMOS EXPLOTAR ESTA VULNERABILIDAD ?

Esta vulnerabilidad `está presente` en webs que tengan `jQuery` de una `versión menor a la 3.4.0`, es decir, de que `desde jQuery 3.9.0 hacia abajo` son vulnerables a este ataque.


## ¿ EN QUE CONSISTE ?

Es importante saber que es una vulnerabilidad que `solo afecta a JavaScript`. Para explicarlo primero voy a utilizar la terminal del navegador.

Primero vamos a `crear un objeto`:

```
> obj = {airil: 1}

airil: 1
  - [[Prototype]]: Object
  - constructor: ƒ Object()
  - hasOwnProperty: ƒ hasOwnProperty()
  - isPrototypeOf: ƒ isPrototypeOf()
  - propertyIsEnumerable: ƒ propertyIsEnumerable()
  - toLocaleString: ƒ toLocaleString()
  - toString: ƒ toString()
  - valueOf: ƒ valueOf()
  - __defineGetter__: ƒ __defineGetter__()
  - __defineSetter__: ƒ __defineSetter__()
  - __lookupGetter__: ƒ __lookupGetter__()
  - __lookupSetter__: ƒ __lookupSetter__()
  - __proto__: (...)
  - get __proto__: ƒ __proto__()
  - set __proto__: ƒ __proto__()
```

Ahora para acceder a esta propiedad podemos usar el `operador del punto` o `indexarlo` de la siguiente manera:

```
> obj.airil

1
```

Y tambien sabemos que si intentamos acceder a una `propiedad que no existe` nos devolvera `indefinido`.

```
> obj.uwu

undefined
```

Ahora vamos a inspeccionar nuestro objeto y vamos a ver que tiene algunas `propiedades` y `metodos` que `no hemos definido`.

```
> obj


## ¿ COMO EXPLOTAR LA VULNERABILIDAD ?






## ¿ COMO ARREGLAR LA VULNERABILIDAD ?