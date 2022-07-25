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

- airil: 1
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

- airil: 1
  · [[Prototype]]: Object
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

Esto pasa porque `cuando se crean nuevos objetos`, heredan `las propiedades de un objeto especial` (de hecho en lo que nos responde pone que es del "[[Prototype]]:") y ese objeto especial se puede llamar como el objeto prototipo, realmente es como cualquier otro objeto, asi que podemos verlo:

```
> Object.getPrototypeOf(obj)

· {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
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

Podemos ver que ambos objetos contienen las mismas cosas. Y si ponemos un numero en vez de "obj" nos da esto de vuelta:

```
> Object.getPrototypeOf(1)

· Number {0, constructor: ƒ, toExponential: ƒ, toFixed: ƒ, toPrecision: ƒ, …}
  - constructor: ƒ Number()
  - toExponential: ƒ toExponential()
  - toFixed: ƒ toFixed()
  - toLocaleString: ƒ toLocaleString()
  - toPrecision: ƒ toPrecision()
  - toString: ƒ toString()
  - valueOf: ƒ valueOf()
  - [[Prototype]]: Object
  - [[PrimitiveValue]]: 0
```

Y si probamos a poner un caracter o un string nos da algo parecido:

```
> Object.getPrototypeOf("a")

· String {'', constructor: ƒ, anchor: ƒ, at: ƒ, big: ƒ, …}
  - anchor: ƒ anchor()
  - at: ƒ at()
  - big: ƒ big()
  - blink: ƒ blink()
  - bold: ƒ bold()
  - charAt: ƒ charAt()
  - charCodeAt: ƒ charCodeAt()
  - codePointAt: ƒ codePointAt()
  - concat: ƒ concat()
  - constructor: ƒ String()
  - endsWith: ƒ endsWith()
  - fixed: ƒ fixed()
  - fontcolor: ƒ fontcolor()
  - fontsize: ƒ fontsize()
  - includes: ƒ includes()
  - indexOf: ƒ indexOf()
  - italics: ƒ italics()
  - lastIndexOf: ƒ lastIndexOf()
  - length: 0
  - link: ƒ link()
  - localeCompare: ƒ localeCompare()
  - match: ƒ match()
  - matchAll: ƒ matchAll()
  - normalize: ƒ normalize()
  - padEnd: ƒ padEnd()
  - padStart: ƒ padStart()
  - repeat: ƒ repeat()
  - replace: ƒ replace()
  - replaceAll: ƒ replaceAll()
  - search: ƒ search()
  - slice: ƒ slice()
  - small: ƒ small()
  - split: ƒ split()
  - startsWith: ƒ startsWith()
  - strike: ƒ strike()
  - sub: ƒ sub()
  - substr: ƒ substr()
  - substring: ƒ substring()
  - sup: ƒ sup()
  - toLocaleLowerCase: ƒ toLocaleLowerCase()
  - toLocaleUpperCase: ƒ toLocaleUpperCase()
  - toLowerCase: ƒ toLowerCase()
  - toString: ƒ toString()
  - toUpperCase: ƒ toUpperCase()
  - trim: ƒ trim()
  - trimEnd: ƒ trimEnd()
  - trimLeft: ƒ trimStart()
  - trimRight: ƒ trimEnd()
  - trimStart: ƒ trimStart()
  - valueOf: ƒ valueOf()
  - Symbol(Symbol.iterator): ƒ [Symbol.iterator]()
  - [[Prototype]]: Object
  [[PrimitiveValue]]: ""
```

Pero hay otras formas de acceder al `Prototype`:

```
> Object.prototype
```

Y hay otras dos formas que utilizan `metodos especiales de Js` al llamar a nuestro objeto:

```
> obj.__proto__
```

```
> obj.constructor.prototype
```

Esas tres ocpiones te daran el mismo resultado.

Asi podremos modificar o añadir propiedades a cualquier objeto. Por ejemplo:

```
> Object.prototype.new = 'added'
  'added'  
  
  
> Object.prototype

·{new: 'added', constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, …}
  new: "added"
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

## ¿ COMO EXPLOTAR LA VULNERABILIDAD ?






## ¿ COMO ARREGLAR LA VULNERABILIDAD ?
