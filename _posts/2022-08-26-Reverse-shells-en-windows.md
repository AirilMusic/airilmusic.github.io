---
layout: single
title: Reverse Shells en Windows
excerpt: "En este artículo vamos a ver las formas que hay de mandarnos reverse shells cuando la maquina victima es Windows."
date: 2022-08-26
classes: wide
header:
  teaser: /assets/images/web-hacking/reverse-shell-windows/netcat.PNG
  teaser_home_page: true
  icon: 
categories:
  - Windows
tags:  
  - Windows
  - Reverse shell
---

He hecho este artículo ya que las `reverse shells` en `Windows` son diferentes a Linux y ya que he empezado a aprender Hacking para Windows, ya que en linux ya soy capaz de resolver maquinas sin ayuda pues se me ha ocurrido hacer este post.

# PONERNOS A LA ESCUCHA: 

Lo primero, antes de mandarnos una consola desde la máquina víctima necesitamos un `puerto` donde estemos `a la escucha` para que nos llegue ahí la shell.

Para esto solemos utilizar el puerto `443/tcp`, ya que es un puerto por el cual se transmiten datos en casos de páginas web, ya que es el puerto por el cual suelen correr los servicios `https`. Por lo tanto, los `firewalls y antivirus` `pueden detectar` datos transmitirse por ese puerto, pero `no le dan importancia`, ya que es normal que se manden datos por ahí. Y, por lo tanto, `somos menos detectables`.

Para esto utilizaremos el siguiente `comando` estando en `root`:

```
> nc -nlvp 443
listening on [any] 443 ...
```

![](/assets/images/web-hacking/reverse-shell-windows/netcat.PNG)
