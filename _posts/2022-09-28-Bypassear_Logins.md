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

# WORKING PROGRESS

En este artículo vamos a ver `distintas formas que hay para bypassear un login`, es decir, formas para pasar un login sin necesidad de introducir credenciales, o en su defecto encontrar unas credenciales válidas con las que poder conectarnos con la cuenta de otro usuario.

## ÍNDICE

```
  - OSINT
  - IDOR
  - SQLi
  - No SQLi
  - COOKIE HIJACKING
  - SHELL SHOCK
  - PADDING ORACLE ATTACK con PADDBUSTER
  - BIT FLIPPER ATTACK para PADDING ORACLE ATTACK
  - PASSWORD SPRAYING
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

Probablemente haré un artículo solo para esto, porque es un tema muy extenso. Por eso aquí lo explicaré un poco por encima y aunque pondre algún ejemplo de como sería de forma manual, pero sobre todo voy a explicar como realizar este ataque con `sqlmap`.

# No SQLi

# COOKIE HIJACKING

# SHELL SHOCK

# PADDING ORACLE ATTACK con PADDBUSTER

# BIT FLIPPER ATTACK para PADDING ORACLE ATTACK

# PASSWORD SPRAYING

