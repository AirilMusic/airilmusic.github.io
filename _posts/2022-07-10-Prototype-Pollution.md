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

En un programa de bug bounty me he encontrado que una web tiene la version de `jQuery 3.2.1` y buscando que vulnerabilidades tiene esa versión me he encontrado con dos: **XSS** y **Prototype Pollution**
En ese programa la vulnerabilidad de **XSS** no estaba en el scope, entonces me he decidido por el **Prototype Pollution**, y pensando que habría algún exploit y no seria del otro mundo, me he encontrado con esta vulnerabilidad que me ha parecido curiosa y un poquito compleja, así que a la vez que yo voy aprendiendo sobre ella se me ha ocurrido escribir este artículo con la finalidad de que tanto yo como cualquier otra persona pueda entender en que consiste y como explotar esta vulnerabilidad.

Así que en este artículo veremos esta interesante y compleja vulnerabilidad.

Cabe recalcar que esta vulnerabilidad es una vulnerabilidad muy grave, ya que permite *ejecutar código remoto (RCE)* o hacer ataques de **denegación de servicios (DOS)**.

## ¿QUE ES?

El ataque de contaminación de prototipo **(prototype pollution attack)** es una técnica de seguridad que se utiliza para **manipular objetos JavaScript** en una aplicación web. El ataque se lleva a cabo mediante la **inyección de propiedades o métodos en el prototipo de un objeto**, lo que puede permitir al atacante ejecutar código malicioso o tomar el control de la aplicación web. Los desarrolladores pueden mitigar este tipo de ataques mediante la validación y sanitización de datos de entrada, así como mediante la implementación de controles de seguridad adicionales en el lado del servidor.

En JavaScript, la propiedad “prototype” se utiliza para definir las propiedades y métodos de un objeto. Los atacantes pueden explotarlo para modificar las propiedades y métodos de un objeto y tomar el control de la aplicación. Por ejemplo, si somos un usuario normal, sin privilegios, y el usuario tiene una propiedad **rol**, que puede ser por ejemplo user, podríamos llegar a utilizar el **prototipo para cambiar dicha propiedad** a admin y convertirnos en administradores sin necesidad de proveer credenciales. 

## ¿ CUANDO PODEMOS EXPLOTAR ESTA VULNERABILIDAD ?

Esta vulnerabilidad está presente en webs que tengan `jQuery` de una `versión menor a la 3.4.0`, es decir, de que desde `jQuery 3.9.0 hacia abajo` son vulnerables a este ataque. Y algunas versiones de `PHP` también son vulnerables a dicha vulnerabilidad.

Encontrar una web con una versión vulnerable es mas o menos fácil, sin embargo lo dificil es explotarlas, porque tenemos que conocer los objetos de los usuarios o de lo que queramos modificar, para poder modificarlo, y como lo normal es que no se puedan conocer hay que probar a fuerza bruta si hay campos tipicos como `rol`, `isAdmin`... o si mediante esos objetos se almacena información como puntos o dinero también se podría modificar, pero tenemos que saber si existe y como se llama el campo que queremos modificar. Por eso es una vulnerabilidad que se suele reportar que hay una versión vulnerable pero no se suele explotar. 

## TIPOS DE ATAQUE

# Ejecución remota de codigo (External Code Execution):
  
Aqui cabe aclarar que la ejecución remota de código normalmente solo es posible en los casos en que la base de código **evalúa un atributo específico de un objeto y luego ejecuta esa evaluación**.

Por ejemplo: eval(someobject.someattr) . En este caso, si el atacante contamina Object.prototype.someattr, es probable que pueda aprovechar esto para ejecutar el código.

# Inyección de propiedades:

El atacante **modifica propiedades de un objeto mediante el prototypo**, incluidas propiedades de seguridad como cookies o tokens, roles, puntos...

Por ejemplo: si una base de código verifica los privilegios someuser.isAdmin, entonces, cuando el atacante contamine Object.prototype.isAdmin y lo iguale true, podrá obtener privilegios de administrador.

Esto afecta tanto a servidores de aplicaciones como a servidores web.

# Denegacion de Servicios (DoS):

Este es el ataque más probable. DoS ocurre cuando el objeto contiene **funciones genéricas** que se **llaman implícitamente para varias operaciones** (por ejemplo, toStringy valueOf).

El atacante contamina `Object.prototype.someattry` altera su estado a un **valor inesperado** como Into Object. En este caso, **el código falla** y es probable que cause una denegación de servicio.

Por ejemplo: si un atacante contamina `Object.prototype.toString` definiéndolo como un número entero, si el código base en algún punto dependiera de `someobject.toString()` él, fallaría.

## ¿ EN QUE CONSISTE ?

Es importante saber que es una vulnerabilidad que **solo afecta a JavaScript**. Para explicarlo primero voy a utilizar la terminal del navegador.

*(Puede que aquí no se entienda bien, luego en la explotación se entiende mucho mejor)*

Primero vamos a **crear un objeto**:

```js
> obj = {airil: 1}

- airil: 1
```

Ahora para acceder a esta propiedad podemos usar el operador del punto o **indexarlo** de la siguiente manera:

```js
> obj.airil

1
```

Y tambien sabemos que si intentamos acceder a una **propiedad que no existe** nos devolvera **indefinido**.

```js
> obj.uwu

undefined
```

Ahora vamos a inspeccionar nuestro objeto y vamos a ver que tiene algunas **propiedades** y **metodos** que **no hemos definido**.

```js
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

Esto pasa porque **cuando se crean nuevos objetos, heredan las propiedades de un objeto especial llamado prototipo** (de hecho en lo que nos responde pone que es del "[[Prototype]]:"), que realmente es como cualquier otro objeto, asi que podemos verlo:

```js
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

Podemos ver que ambos objetos contienen las mismas cosas. Y si ponemos un numero en vez de `obj` nos da esto de vuelta:

```js
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

```js
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

```js
> Object.prototype
```

Y hay otras dos formas que utilizan **métodos especiales de Js** al llamar a nuestro objeto:

```js
> obj.__proto__
```

```js
> obj.constructor.prototype
```

Esas tres opciones te darán el mismo resultado.

Así podremos **modificar o añadir propiedades** a cualquier objeto. Por ejemplo:

```js
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

Y veremos qué hemos **añadido una nueva propiedad**.

¿Pero qué pasa si intentamos **modificar una propiedad existente**?

Por ejemplo, vamos a crear una función y vamos a intentar modificarla y luego ejecutarla:

```js
> obj.toString()

>Object.prototype.toString = 1

> obj.toString()

#Aqui nos dara un error diciendo que el string no es una funcion, eso es obvio
```

Pero que pasa si hacemos una función que por ejemplo ponga un pop-up de una alerta (pero puede ser cualquier otra cosa) y se lo añadimos a nuestro método `toString`:

```js
> function hacked() {alert('hacked')}

> Object.prototype.toString = hacked

> obj.toString()
```

Pues nos aparece el pop up, esto significa que hemos conseguido modificarla y poder ejecutarla. Claro, aquí esto no supone un problema, pero si alguien de forma maliciosa puede **inyectar código** JavaScript, se pueden hacer cosas no tan inofensivas. 

Por eso la vulnerabilidad se llama **Prototype Pollution** porque los usuarios pueden **contaminar el prototype** inyectándole código y así modificar el funcionamiento de aplicaciones o sitios web.


## ¿ COMO EXPLOTAR LA VULNERABILIDAD ?

Para esta explicación haré uso del siguiente laboratorio de prueba:

- **SKF-LABS**: [https://github.com/blabla1337/skf-labs](https://github.com/blabla1337/skf-labs)

De primeras nos encontramos con esto:

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution-1.png)

Nos registramos y nos permite mandar un mensaje supuestamente al usuario administrador:

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution-2.png)

Probamos a mandar y no pone nada, (como spoiler: en caso de que consigamos logearnos como admin, un poquito mas arriba nos pondrá `Admin: True`)

Entonces, ¿ahora como lo esplotamos?

Si nos fijamos en el código, esta utilizando un `merge()` para combinar dos objetos, el mensaje que mandamos (email y mensaje) y nuestra IP:

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution-3.png)

Resuldato:

```js
{
	email: 'test@test.com',
	msg: 'Hola esto es una prueba',
	ipAddress: '127.0.0.1'
}
```

Esto es una práctica que se puede hacer en programación, pero que a la vez puede traer algunos problemas y fallos de seguridad (sobre todo es vulnerable a `Prototype-Pollution)`.

Vamos a hacer un script de ejemplo `script.js` simulando lo que esta haciendo con el mensaje enviado para ver como vamos a poder explotarlo:

```js
var merge = function(target, source) {
	for(var attr in source) {
		if(typeof(target[attr]) === "object" && typeof(source[attr]) === "object") {
			merge(target[attr], source[attr]);
		} else {
			target[attr] = source[attr];
		}
	}
	return target;
};

var usuario = {"name": "Nombre", "age":"27"}
var admin = {"name": "Jon Doe", "age":57, "isAdmin":true}

var body = JSON.parse('{"email": "test@test.com", "msg": "Hola esto es una prueba"}');

console.log(merge({"ipAddress": "127.0.0.1"}, body));
```

Todo funciona igual (con la diferencia de que hemos creado dos objetos iguales pero uno de ellos tiene la propiedad de que es admin).

Si probamos a ver si el objeto `admin` tiene admin nos va a decir `true`, porque tiene la propiedad `"isAdmin": tue`:

```js
console.log("¿El usuario admin es administrador? -> " + admin.isAdmin);
```

Pero en cambio si probamos con el objeto `usuario` nos dice `undefined`, porque no tiene la propiedad `isAdmin` definida:

```js
console.log("¿El usuario usuario es administrador? -> " + usuario.isAdmin);
```

Aquí es donde entra el `Prototype`, porque cuando un objeto no tiene una propiedad asignada lo que hace es mirar por su `Prototype` que es de donde hereda ciertas propiedades.

Entonces si no esta sanitizado podemos cambiar la petición con `burpsuite` y declarar un prototipo con la propiedad que queremos para que al mirar al prototipo, como nuestro usuario no tiene la propiedad `isAdmin` que se la asigne.

En este código de ejemplo sería como hacer algo como esto:

```js
var body = JSON.parse('{"email": "test@test.com", "msg": "Hola esto es una prueba", "__proto__": {"isAdmin": true}}');
```

Y nos daría admin, porque como no tenemos esa propiedad va a mirar al prototipo que hemos creado, y como en el prototipo que hemos creado esta `"isAdmin": true` vamos a heredar esa propiedad, lo que nos convierte en administradores.

Entonces ahora pasamos el ejemplo "real" es decir la web del laboratorio de prueba. Lo que sabemos (tras ver el código) es que hay una propiedad que es `admin` que esta en true si eres admin y false si no lo eres. Por lo que si conseguimos contaminar el prototipo para crear una propiedad que sea `admin` y la seteamos a `true` por defecto, entonces nuestro usuario heredará esa propiedad para convertirse en administrador.

Entonces vamos a capturar la petición y vamos a probar a cambiar el `Content-Type` a `application/json` y representaremos la data en JSON. Nos lo da por bueno, así que también vamos a probar a meterle el nuevo prototipo con la propiedad `"admin": true` por defecto:

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution-4.png)

Y listo nos daria admin uwu

![](/assets/images/web-hacking/prototype-pollution/prototype-pollution-5.png)


## ¿ COMO ARREGLAR LA VULNERABILIDAD ?

Más que como arreglar la vulnerabilidad explicaré que se puede hacer para intentar prevenirla o solucionarla, pero no una forma de arreglarla como tal.

Para esto cirtas medidas que son recomendables tomar son las siguientes:

- **Conjelar el Prototype**: es decir que es recomendable utilizar `Object.freeze (Object.prototype)`.
- Hacer que se requiera la **validación del esquema** de la **entrada JSON**.
- **Evitar** usar **funciones de combinación recursivas inseguras**.
- Tambien es recomendable utilizar **objetos sin Prototype**, por ejemplo: Object.create(null). Y de esta forma se **rompe la cadena de contaminación** y hace que en caso de ataque, que sea mas leve.
- Otra cosa que es recomendable es utilizar **Maplugar de Object**.
