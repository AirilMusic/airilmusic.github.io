---
layout: single
title: Web Hacking: Prototipe Pollution Attack
excerpt: "En este artículo vamos a intentar entender en que se basa el ataque Prototype Pollution y como hacerlo, ya que es algo que no es muy conocido y es super interesante."
date: 2022-07-10
classes: wide
header:
  teaser: 
  teaser_home_page: true
  icon: /assets/images/web-hacking/prototype-pollution.png
categories:
  - Web-Hacking
tags:  
  - Prototype-Pollution
---

![](/assets/images/web-hacking/prototype-pollution.png)

En un programa de `bug bounty` me he encontrado que una web tiene la version de `jQueri 3.2.1` y buscando que vulnerabilidades tiene esa versión me he encontrado con dos: `XSS` y `Prototype Pollution`
Claro, en ese programa la vulnerabilidad de `XSS` no estaba en el scope, entonces me he decidido por el `Prototype Pollution`, y pensando que habría algún exploit y no seria del otro mundo, me he encontrado con esta vulnerabilidad que es muy compleja y no muchos entienden, así que a la vez que yo voy aprendiendo sobre ella se me ha ocurrido escribir este artículo con la finalidad de que tanto yo como cualquier otra persona pueda `entender en que consiste` y `como explotar esta vulnerabilidad`.
Así que en este artículo veremos esta interesante y compleja vulnerabilidad.

