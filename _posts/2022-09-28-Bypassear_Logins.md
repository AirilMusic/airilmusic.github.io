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

En este artículo vamos a ver `distintas formas que hay para bypassear un login`, es decir, formas para pasar un login sin necesidad de introducir credenciales, o en su defecto encontrar unas credenciales válidas con las que poder conectarnos con la cuenta de otro usuario.

## OSINT

Puede parecer gracioso pero `no es raro encontrar credenciales validas en internet`. 

Lo primero que tenemos que comprobar es sí para el servicio o la tecnología que esten utilizando hay `credenciales por defecto`, es decir, que tu al comprar eso tienes unas credenciales ya predefinidas, (por ejemplo en las raspberry pi es pi, o en mi instituto la del gmail es aniturri1234). Obviamente eso es lo primero que hay que cambiar cuando nos creamos una cuenta en algún lado... pero `a mucha gente se le olvida o no sabe` que ese usuario o entrada por defecto eso existe.

Otra forma que hay es mirar repositorios de `github`, preguntas en `stack overflow`... relacionados con la web que estamos testeando, ya que muchas veces durante el dessarrollo se publican partes del código y sin darse cuenta hay credenciales en ese código. `Parece estúpido pero es muy común XD`.

Y pues luego también `mirar si se han filtrado anteriormente credenciales` de esa web o de posibles usuarios de la web. 

Y otro consejo: buscar la web en `shodan`, ya que puedes encontrar información interesante y que te sera util. 

![](/assets/images/login-bypass/shodan.PNG)

## IDOR

Pongo esto como el primer método de bypassing, porque es mi favorito, es super gracioso, porque `es absurdo a mas no poder`. Y no es tan conocido, por ejemplo en bug bounty pasa una cosa bastante graciosa que es que la mayoría de personas buscan XSS o SQLi, pero se olvidan de esta vulnerabilidad tan graciosa. Y por otra parte, hace unos meses le esneñé esta vuln a un amigo que es programador de backend y se sorprendio porque no la conocia y es muy absurda. 

Primero explicaré como se hace y luego el porqué: 

## COMO EXPLOTAR ESTA VULNERABILIDAD  

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





## PORQUE PASA ESTO Y COMO EVITARLO



## SQLi

## No SQLi

## COOKIE HIJACKING

## SHELL SHOCK

## PADDING ORACLE ATTACK con PADDBUSTER

## BIT FLIPPER ATTACK para PADDING ORACLE ATTACK


