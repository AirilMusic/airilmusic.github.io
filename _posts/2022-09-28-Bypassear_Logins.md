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

## SQLi

## No SQLi


## SHELL SHOCK

## PADDING ORACLE ATTACK con PADDBUSTER

## BIT FLIPPER ATTACK para PADDING ORACLE ATTACK


