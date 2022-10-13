---
layout: single
title: Bypassear Logins
excerpt: "En este artículo vamos a ver distintas formas de bypassear un login."
date: 2022-09-28
classes: wide
header:
  teaser: /assets/images/login-bypass/portada.jpg
  teaser_home_page: true
  icon: 
categories:
  - Web-Hacking
tags:  
  - SQLi
  - OSINT
  - IDOR
---

# WORK IN PROGRESS

En este artículo vamos a ver `distintas formas que hay para bypassear un login`, es decir, formas para pasar un login sin necesidad de introducir credenciales, o en su defecto encontrar unas credenciales válidas con las que poder conectarnos con la cuenta de otro usuario. (`NOTA:` también voy a poner `algunos ataques que aunque no son exactamente al panel del loggin`, también sirven para conseguir un usuario de `administrador` con el que tomar el control de la web)

## ÍNDICE

```
  - OSINT
  - IDOR
  - SQLi
  - No SQLi
  - COOKIE HIJACKING (HTML injectio y XSS)
  - SHELL SHOCK
  - PADDING ORACLE ATTACK con PADDBUSTER
  - BIT FLIPPER ATTACK para PADDING ORACLE ATTACK
  - PASSWORD SPRAYING
  - SSTI
  - CLICKJAKING
  - BUFFER OVERFLOW
  - TYPE JUGGLIN ATTACK
  - DESERIALIZATION ATTACK with Node.js
  - XML --> XXE
```

# OSINT

Puede parecer gracioso pero `no es raro encontrar credenciales validas en internet`. 

Lo primero que tenemos que comprobar es sí para el servicio o la tecnología que esten utilizando hay `credenciales por defecto`, es decir, que tu al comprar eso tienes unas credenciales ya predefinidas, (por ejemplo en las raspberry pi es pi, o en mi instituto la del gmail es aniturri1234). Obviamente eso es lo primero que hay que cambiar cuando nos creamos una cuenta en algún lado... pero `a mucha gente se le olvida o no sabe` que ese usuario o entrada por defecto eso existe.

Otra forma que hay es mirar repositorios de `github`, preguntas en `stack overflow`... relacionados con la web que estamos testeando, ya que muchas veces durante el dessarrollo se publican partes del código y sin darse cuenta hay credenciales en ese código. `Parece estúpido pero es muy común XD`.

Y pues luego también `mirar si se han filtrado anteriormente credenciales` de esa web o de posibles usuarios de la web. 

Y otro consejo: buscar la web en `shodan`, ya que puedes encontrar información interesante y que te sera util. 

![](/assets/images/login-bypass/shodan.PNG)

# IDOR

Pongo esto como el primer método de bypassing, porque es mi favorito, es super gracioso, porque `es absurdo a mas no poder`. Y no es tan conocido, por ejemplo en bug bounty pasa una cosa bastante graciosa que es que la mayoría de personas buscan XSS o SQLi, pero se olvidan de esta vulnerabilidad tan graciosa. Y por otra parte, hace unos meses le esneñé esta vuln a un amigo que es programador de backend y se sorprendio porque no la conocia y es muy absurda. 

Primero explicaré como se hace y luego el porqué: 

## COMO EXPLOTAR ESTA VULNERABILIDAD  

Hay `dos formas`, `la básica`, y la menos común de encontrar, pero que no por ello tiene menos importancia:

Por ejemplo, nos hemos registrado en una web y queremos cambiar la información del perfil. Para eso, hacemos clic en `http://online-service.thm/profile?user_id=1305` y vemos nuestra información. Pero claro, podemos cambiarnos el `id` a otro. En este punto `si la web esta bien hecha no nos debería dejar`, pero `si no se ha tenido en cuenta que esto pues podríamos acceder a la información de otro perfil`. 

Claro, el ejemplo anterior `se puede complicar` un poco, por ejemplo que al acceder a una página `se mande una petición con la id`, y ahum se puede complicar mas, porque `la id puede viajar hasheada`. Para eso es tan facil como `capturar la petición y cambiar la id`, y si esta hasheada pues descifrar el algoritmo de encriptado (en webs los mas comunes son base64 y md5) y `hashear la nueva id` de esa forma antes de cambiarla en la petición. 

Ejemplo de como puede viajar la petición:

![](/assets/images/login-bypass/IDOR-1.png)

..................................................................................................................................................................................................................................................................................................

..................................................................................................................................................................................................................................................................................................

Y `la forma un poco mas compleja`, `mas común` y que es `mas peligrosa`, ya que `nos permite acceso total`: 

Esta vulnerabilidad sobre todo se da cuando `intentamos entrar` a un directorio (por ejemplo /admin) y `nos redirige` a un login. Entonces la forma mas facil de detectarla es hacer `Wfuzzing` a una web y ver si nos redirige. Sobre todo si el codigo de `la respuesta de la web` es `301` es bastante probable que `sea vulnerable`.

Como ejemplo voy a poner el Fortress Acerva, ya que aunque ese login era un rabit hole, se podia bypassear con un IDOR:

```
❯ wfuzz -c --hc=404 -t 200 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt http://10.13.37.11/FUZZ

********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://10.13.37.11/FUZZ
Total requests: 220560

=====================================================================
ID           Response   Lines    Word       Chars       Payload
=====================================================================

000000241:   301        9 L      28 W       315 Ch      "wp-content"
000000274:   401        14 L     54 W       458 Ch      "scripts"                                                            
000000786:   301        9 L      28 W       316 Ch      "wp-includes"
000000834:   301        9 L      28 W       308 Ch      "dev"
000001073:   301        9 L      28 W       315 Ch      "javascript"
000007180:   301        9 L      28 W       313 Ch      "wp-admin"                                                                                             
000011260:   301        9 L      28 W       312 Ch      "backups"                                                                                               
000095524:   403        9 L      28 W       276 Ch      "server-status"      
```

Primero hice Wfuzzing a su web, y uy, el directorio `/wp-admin` da `301` (entre otros) y si intentamos acceder `nos redirige a un login`, asi que apesta a `IDOR`. Por lo tanto voy a intentar `capturar la petición` con `burpsuit` de cuando intento acceder a `/wp-admin`.

Antes de nada necesitamos `modificar` la `configuración` de burpsuit `para interceptar la respuesta del lado del servidor`. Así que nos vamos a la configuración y cambiamos estas dos opciones:

![](/assets/images/login-bypass/IDOR-3.PNG)

Y ahora ya podemos continuar.

Primero voy a intentar acceder sin usar el `proxy para burp`, para que veais el `redirect`:

![](/assets/images/login-bypass/IDOR-2.PNG)

Vemos que nos hace redirect al login, por lo tanto vamos a intentar acceder sin proporcionar credenciales. Para eso lo primero que tenemos que hacer es `activar el proxy con foxy proxy` y `capturar la petición` al intentar acceder a `/wp-admin`:

![](/assets/images/login-bypass/IDOR-4.PNG)

Una vez capturada le daremos a `Forward` y veremos que nos pone la `respuesta del servidor`. Vemos que nos pone `302 Found`:

![](/assets/images/login-bypass/IDOR-5.PNG)

Claro, nosotros no queremos que nos haga un redirect, porque eso significa el codigo 302 (osea, todos los 300 algo), por lo tanto `vamos a cambiarlo` a ver que pasa:

![](/assets/images/login-bypass/IDOR-6.PNG)

Le damos de nuevo a `Forward` y:

![](/assets/images/login-bypass/IDOR-7.PNG)

(no se ve nada porque en esa máquina es un rabit hole, pero en hemos logrado acceso, en caso de que hubiese algo tendriamos acceso a eso, y eso es lo que importa)

Y aquí esta lo gracioso y absurdo de este ataque. `Hemos conseguido acceso` a un directorio en el cual no deberíamos estar XD. Claro, ahora `según hagamos alguna acción` nos va a `detectar` y nos va a mandar al `redirect`, asi que mientras no tengamos un usuario con la capacidad de estar ahí, deberemos `hacer esto para cada cosa que hagamos`.

## PORQUE PASA ESTO Y COMO EVITARLO

Esta vulnerabilidad puede ocurrir cuando un servidor web recibe `input del usuario` para recuperar objetos (archivos, datos, documentos…). Si se pone mucha `confianza en los datos suministrados` en la petición, y `no se validan en la parte de servidor` para `confirmar que el objeto pertenece al usuario que lo pide`, puede darse una `IDOR`.


# SQLi

`El siguiente artículo será sobre esto`, ya que es `un tema muy extenso` que no me parece adecuado explicarlo entero aquí, ya que abarca demasiado. Por lo que aquí solo explicaré en que consiste y un poco por encima como funciona este ataque y como ya he mencionado me adentraré a fondo en el en el siguiente artículo.

## QUE ES Y PORQUE PASA?

Con dice el nombre, `inyectamos código SQL en un input` o campo que tenga acceso a una `base de datos` para así poder interactuar con la BBDD de forma directa, es decir, la BBDD interpretará literalmente la consulta y asi tendremos total acceso a ella, pudiendo ver su contenido... Algo que de otra forma no podríamos hacer. 

`Ejemplo`: hay una página de login donde hay dos campos, user y password (sin tener en cuenta las cookies, la url... que también pueden servir). Claro, nosotros podemos meter esos datos y compará si ese user coincide con esa contraseña, pero si la web `no sanitiza el input` del usuario, permite que se pueda "engañar" a la web y que `en vez de mandarle un user, que le mande nuestra consula SQL`, así la BBDD `interpretará nuestra consulta y nos devolverá lo que nosotros queramos`, por ejemplo; el nombre de la base de datos, de las tablas... (pudiendo ver usuarios, contraseñas, correos electronicos...).

Hay distintas formas (pero eso lo explicaré en el siguiente artículo):

```
  - SQLi
  - SQLi Blind
  - SQLi Mediante el tiempo de la respuesta
  - No SQLi (este lo explico despues)
  - SQLi mediante peticiones
  - ...
```

## COMO EFECTUAR ESTE ATAQUE

(`NOTA:` antes de realizar este ataque es recomendable saber que tipo de BBDD tiene, pero sino se puede probar un poco a fuerza bruta, ya que no es lo mismo MySQL, que SQLserver, que SQL... (si no se especifica `sqlmap` prueba todas a fuerza bruta, pero si se especifica se pueden lograr mejores resultados en menos tiempo)

Para explicar un poco por encima `como funcionan las consultas SQL`, poruqe `lo mas importante` que tenemos que saber es `como funcionan las BBDD`  y `como funcionan las cunsultas SQL`. Y ver como utilizarlas para realizar este ataque voy a poner un ejemplo muy básico (`nota:` los ataques que se hacen en un entorno real son mas complicados, pero eso lo explicaré en otro artículo, porque sino este sera demasiado largo)

Ejemplo consultas SQL:

Considere una aplicación de compras que muestre productos en diferentes categorías. Cuando el usuario hace clic en la categoría Regalos, su navegador solicita la URL:

```
https://insecure-website.com/products?category=Gifts
```

Esto hace que la aplicación realice una consulta SQL para recuperar detalles de los productos relevantes de la base de datos:

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

Esta consulta SQL pide a la base de datos que devuelva:

```
  - todos los detalles (*)
  - de la tabla de productos
  - donde la categoría es Regalos
  - y liberado es 1.
```

La restricción se está utilizando para ocultar los productos que no se liberan. Para productos inéditos, presumiblemente . `released = 1released = 0`

La aplicación no implementa ninguna defensa contra los ataques de inyección SQL, por lo que un atacante puede construir un ataque como:

```
https://insecure-website.com/products?category=Gifts'--
```

Esto da como resultado la consulta SQL:

```
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

La clave aquí es que la secuencia de doble guión es un indicador de comentario en SQL, y significa que el resto de la consulta se interpreta como un comentario. Esto elimina efectivamente el resto de la consulta, por lo que ya no incluye . Esto significa que se muestran todos los productos, incluidos los productos no lanzados. `--AND released = 1`

Yendo más allá, un atacante puede hacer que la aplicación muestre todos los productos de cualquier categoría, incluidas las categorías que no conoce:

```
https://insecure-website.com/products?category=Gifts'+OR+1=1--
```

Esto da como resultado la consulta SQL:

```
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```

La consulta modificada devolverá todos los artículos en los que la categoría sea Regalos o 1 sea igual a 1. Como siempre es verdadero, la consulta devolverá todos los elementos. `1=1`

.............................................................................................................................................................

.............................................................................................................................................................

Claro, ese es un ataque muy simple, pero se pueden hacer cosas mas complejas como listar la BBDD, es decir, ver que BBDD hay y su nombre, las tablas que tiene, las columnas...

Por ejemplo:

Para listar la BBDD:

```
' union select database()-- -
```

En caso de que huviese mas columnas, por ejemplo 4 y la cuarta fuese la que interpreta:

```
' union select 1,2,3,database()-- - 
```

Para ver la version de la BBDD:

```
' union select version()-- -
```

Para listar otras BBDD-s existentes:

```
' union select schema_name from information_schema.schemata-- -
```

Ver las tablas de otra base de datos que hemos encontrado:

```
' union select table_name from information_schema.tables where table_schema="{nombre de la BBDD}"-- -
```

Ver las columnas de una tabla de esa otra BBDD:

```
' union select column_name from information_schema.columns where table_schema="{nombre de la BBDD}" and table_name="{nombre de la tabla}"-- -
```

(`NOTA:` lo explicaré bien en el siguiente artículo, y también explicaré `sqlmap` en ese artículo)

## PREVENCIÓN

Aunque se podrían `sanear las entradas` usando métodos como `mysqli_real_escape_string`, es más recomendable la `utilización de sentencias preparadas o parametrizadas`. Las sentencias preparadas te permitirán ejecutar la misma sentencia con gran eficiencia.

`Tips` para incrementar la seguridad:

```
  1- No confiar en el input del usuario, filtrar el input para bloquear caracteres que puedan estar en consultas SQL (/`'%&="|!¡;:.,)
  2- Bloquear palabras en el input como SELECT, FROM, WHERE...
  3- Verificar y filtrar parametros que no solo sean inputs, por ejemplo logins, sino que también: parámetros de entrada y campos tipo hidden de las páginas web, coockies, url...
  4- No utilizar sentencias SQL construidas dinámicamente.
  5- No proporcionar mayor información de la necesaria.
```

# No SQLi

Esto se basa en un concepto que hemos visto antes que era que si la respuesta era `algo o sino 1=1` se bypasseaba el login porque se cumplia lo de 1=1, pero esta vez tenemos que decir que `la contraseña es distinta a algo`, entonces `interpreta como valido` y bypassea el login.

`Hay dos formas`, si no funciona la primera hay que probar la segunda:

`PRIMERA:`
En esta no tocaremos la petición mas que para `cambiar` un poco la parte de las `credenciales`. Siempre que tenga un `formato parecido` cambiaremos la linea por la siguiente (vale admin o cualquier otro usuario que sepamos que existe):

```
user=admin&password[$ne]=uwu
```

`SEGUNDA:`

El anterior metodo no siempre funciona, pero tambien podemos mandar la 	informacion en `json`, así que:

Primero en burpsuite en `capturamos la petición` del login y `cambiamos` la linea donde pone `Content-Type` por: Content-Type: `aplication/json`
luego `donde el user y password lo reemplazaremos por` lo siguiente:
	
  ```json  
{
	"user": "admin",
	"password":{
		"$ne":"uwu"
	}
}
```

# COOKIE HIJACKING

Antes de ver como robar cookies de otros usuarios para poder logearnos como ellos sin sus credenciales, debemos ver que es el HTML injection y el Cross Site Scripting (XSS).

## HTML injection



## Cross Site Scripting (XSS)

## Cookie Hijacking


# SHELL SHOCK

# PADDING ORACLE ATTACK con PADDBUSTER

# BIT FLIPPER ATTACK para PADDING ORACLE ATTACK

# PASSWORD SPRAYING

